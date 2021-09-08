---
title: "使用 GitLab CI 与 Argo CD 实践 GitOps"
tags:
    - kubernetes
categories: [ TECH ]
image: "img/Statue-of-Liberty.jpeg"
---

## argocd简介

Argo CD是用于Kubernetes的声明性GitOps持续交付工具，遵循GitOps模式，该模式使用Git仓库作为定义所需应用程序状态的真实来源。

Argo CD可在指定的目标环境中自动部署所需的应用程序状态，应用程序部署可以在Git提交时跟踪对分支，标签的更新，或固定到清单的特定版本。

argoCD支持的Kubernetes 配置清单包括helm charts、kustomize或纯YAML/json文件等。

## helm部署argocd

参考：

https://artifacthub.io/packages/helm/argo/argo-cd

https://github.com/argoproj/argo-helm/tree/master/charts/argo-cd

添加helm仓库

```
helm repo add argo https://argoproj.github.io/argo-helm
```

部署argocd

```bash
helm install argocd argo/argo-cd \
  --namespace=argocd --create-namespace \
  --set global.image.repository="argoproj/argocd" \
  --set dex.image.repository="dexidp/dex" \
  --set server.service.type=NodePort \
  --set server.service.nodePortHttp=30085 \
```

获取admin用户密码

```bash
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```

## 部署gitlabCI

安装gitlab

```bash
docker run -d --name gitlab \
  --restart always \
  --hostname gitlab.prod.cloudcele.com \
  -p 8929:8929 -p 2289:22 \
  -e GITLAB_OMNIBUS_CONFIG="external_url 'http://gitlab.prod.cloudcele.com:8929'; gitlab_rails['gitlab_shell_ssh_port'] = 2289; gitlab_rails['initial_root_password'] = 'Gitlab@123';" \
  -v /data/gitlab/config:/etc/gitlab \
  -v /data/gitlab/logs:/var/log/gitlab \
  -v /data/gitlab/data:/var/opt/gitlab \
  gitlab/gitlab-ce:14.1.5-ce.0
```

安装gitlab-runner

```bash
helm install gitlab-runner \
  --namespace=gitlab-runner \
  --create-namespace \
  --version=0.31.0 \
  --set image=gitlab/gitlab-runner:alpine-v14.1.0 \
  --set runners.helpers.image="gitlab/gitlab-runner-helper:x86_64-8925d9a0" \
  --set gitlabUrl=http://10.0.0.5:8929 \
  --set runnerRegistrationToken="3XxYzq83EhVigW6SAw8Y" \
  --set rbac.create=true \
  --set rbac.clusterWideAccess=true \
  --set privileged=true \
  --set runners.name=k8s-helm-runner \
  --set runners.tags=k8s-runner \
  --set runners.runUntagged=true \
  gitlab/gitlab-runner
```

创建gitlab仓库

```bash
stages:
- build
- deploy

variables:
  CI_REGISTRY_IMAGE: local.registry.cloudcele.com:9080/library/nginx
  CI_USERNAME: root
  CI_PASSWORD: Gitlab%40123

build:
  stage: build
  image:
    name: local.registry.cloudcele.com:9080/library/kaniko-executor:debug
    entrypoint: [""]
  script:
    - mkdir -p /kaniko/.docker
    - echo "10.0.0.5 local.registry.cloudcele.com" >> /etc/hosts
    - echo "{\"auths\":{\"local.registry.cloudcele.com:9080\":{\"auth\":\"$(echo -n admin:Harbor12345 | base64)\"}}}" > /kaniko/.docker/config.json
    - /kaniko/executor --insecure --context $CI_PROJECT_DIR --dockerfile $CI_PROJECT_DIR/Dockerfile --destination $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA

deploy:
  stage: deploy
  image: cnych/kustomize:v1.0
  before_script:
    - git remote set-url origin http://${CI_USERNAME}:${CI_PASSWORD}@gitlab.prod.cloudcele.com:8929/root/nginx-demo.git
    - git config --global user.email "gitlab@git.k8s.local"
    - git config --global user.name "GitLab CI/CD"
  script:
    - git checkout -B master
    - cd deployment/
    - kustomize edit set image $CI_REGISTRY_IMAGE:$CI_COMMIT_SHORT_SHA
    - cat kustomization.yaml
    - git commit -am 'DEV image update'
    - git push -o ci.skip origin master
  only:
    - master
```





```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: apps
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 2
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: local.registry.cloudcele.com:9080/library/nginx:latest
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
  namespace: apps
  labels:
    app: nginx
spec:
  type: NodePort
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
    nodePort: 30080
  selector:
    app: nginx
```



```
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- deployment.yaml
```



```
FROM nginx
COPY index.html /usr/share/nginx/html/
```



## 通过UI创建应用

参考：https://argoproj.github.io/argo-cd/getting_started

1、添加Repositories

2、创建Applications


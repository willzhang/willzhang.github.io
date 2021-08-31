---
title:      "linux 安装 docker"
author:     "will"
date:       2021-08-31
---

## linux 安装 docker

Docker 是一个开源的应用容器引擎，让开发者可以打包他们的应用以及依赖包到一个可移植的镜像中，然后发布到任何流行的 Linux或Windows 机器上。

![](docker.png)

安装参考：

<https://github.com/docker/docker-install>
<https://docs.docker.com/engine/install/centos/#install-using-the-convenience-script>

官方脚本安装docker

```bash
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```

配置镜像加速

```bash
mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://uyah70su.mirror.aliyuncs.com"]
}
EOF
```

启动docker服务

```bash
systemctl enable --now docker
```

运行一个nginx docker容器

```bash
docker run -d --name nginx \
  --restart always \
  -p 80:80 \
  nginx
```

浏览器访问nginx页面

```bash
http://127.0.0.1:80
```

容器常用命令

```bash
# 查看所有容器，包括运行状态和非运行状态容器
docker ps -a

# 启动nginx容器
docker start nginx

# 停止nginx容器
docker stop nginx

# 重启nginx容器
docker restart nginx

# 查看容器运行日志
docker logs -f nginx

# 删除nginx容器，选项-f无需停止强制删除
docker rm -f nginx
```


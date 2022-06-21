---
title: "RHCE 题库"
tags:
    - linux
categories: [ TECH ]
date:       2022-06-21
image: "img/Statue-of-Liberty.jpeg"
---
## RHCE题库
**RHCE满分300分，210分即通过，时间4小时（下午）。**

**参考：**[https://chowdera.com/2022/02/202202220450472345.html](https://chowdera.com/2022/02/202202220450472345.html)

## 考试环境
**重要配置信息**
在考试期间，除了您就坐位置的台式机之外，还将使用多个虚拟系统。您不具有台式机系统的 root 访问权，但具有对虚拟系统的完整 root 访问权。
**系统信息**
在本考试期间，您将操作下列虚拟系统：

![[Pasted image 20220621192035.png]]

这些系统的 IP 地址采用静态设置。请勿更改这些设置。
主机名称解析已配置为解析上方列出的完全限定主机名，同时也解析主机短名称。
**帐户信息**
所有系统的 root 密码是 flectrag。
请勿更改 root 密码。除非另有指定，否则这将是用于访问其他系统和服务的密码。此外，除非另有指定，否则此密码也应用于您创建的所有帐户，或者任何需要设置密码的服务。
为方便起见，所有系统上已预装了 SSH 密钥，允许在不输入密码的前提下通过 SSH 进行 root 访问。请勿对系统上的 root SSH 配置文件进行任何修改。
Ansible 控制节点上已创建了用户帐户 greg。此帐户预装了 SSH 密钥，允许在 Ansible 控制节点和各个 Ansible 受管节点之间进行 SSH 登录。请勿对系统上的 greg SSH 配置文件进行任何修改。您可以从 root 帐户使用 su 访问此用户帐户。
**重要信息**
除非另有指定，否则您的所有工作（包括 Ansible playbook、配置文件和主机清单等）应当保存在控制节点上的目录 /home/greg/ansible 中，并且应当归 greg 用户所有。所有 Ansible 相关的命令应当由 greg 用户从 Ansible 控制节点上的这个目录运行。
**其他信息**
一些考试项目可能需要修改 Ansible 主机清单。您要负责确保所有以前的清单组和项目保留下来，与任何其他更改共存。您还要有确保清单中所有默认的组和主机保留您进行的任何更改。
考试系统上的防火墙默认为不启用，SELinux 则处于强制模式。
如果需要安装其他软件，您的物理系统和 Ansible 控制节点可能已设置为指向 content 上的下述存储库：
http://content/rhel8.0/x86_64/dvd/BaseOS
http://content/rhel8.0/x86_64/dvd/AppStream
一些项目需要额外的文件，这些文件已在以下位置提供：
http://materials
产品文档可从以下位置找到：
http://materials/docs/ansible/html
其他资源也进行了配置，供您在考试期间使用。关于这些资源的具体信息将在需要这些资源的项目中提供。
**重要信息**
请注意，在评分之前，您的 Ansible 受管节点系统将重置为考试开始时的初始状态，您编写的 Ansible playbook 将通过以 greg 用户身份从控制节点上的目录 /home/greg/ansible 目录运行来应用。在 playbook 运行后，系统会对您的受管节点进行评估，以判断它们是否按照规定进行了配置。
## 考试题目
### 1、安装和配置 Ansible
按照下方所述，在控制节点 172.25.250.254 上安装和配置 Ansible：
安装所需的软件包
创建名为 /home/greg/ansible/inventory 的静态清单文件，以满足以下要求：
172.25.250.9 是 dev 主机组的成员
172.25.250.10 是 test 主机组的成员
172.25.250.11 和 172.25.250.12 是 prod 主机组的成员
172.25.250.13 是 balancers 主机组的成员
prod 组是 webservers 主机组的成员
创建名为 /home/greg/ansible/ansible.cfg 的配置文件，以满足以下要求：
主机清单文件为 /home/greg/ansible/inventory
playbook 中使用的角色的位置包括 /home/greg/ansible/roles
解题方法：
```bash
[greg@bastion ~]$ sudo dnf install -y ansible
[greg@bastion ~]$ mkdir -p /home/greg/ansible
[greg@bastion ~]$ cd /home/greg/ansible
[greg@bastion ansible]$ vim inventory
[dev]
172.25.250.9
[test]
172.25.250.10
[prod]
172.25.250.11
172.25.250.12
[balancers]
172.25.250.13
[webservers:children]
prod
[all:vars]
ansible_user=root
ansible_password=redhat

[greg@bastion ansible]$ cp /etc/ansible/ansible.cfg ./
[greg@bastion ansible]$ mkdir roles

[greg@bastion ansible]$ vim /home/greg/ansible/ansible.cfg
#总共只修改四行
inventory = /home/greg/ansible/inventory
roles_path = /home/greg/ansible/roles
host_key_checking = False
remote_user = root

[greg@bastion ansible]$ ansible --version
[greg@bastion ansible]$ ansible-inventory --graph
```
### 2、创建和运行 Ansible 临时命令
作为系统管理员，您需要在受管节点上安装软件。
请按照正文所述，创建一个名为 /home/greg/ansible/adhoc.sh 的 shell 脚本，该脚本将使用 Ansible 临时命令在各个受管节点上安装 yum 存储库：
存储库1：

- 存储库的名称为 EX294_BASE
- 描述为 EX294 base software
- 基础 URL 为 http://content/rhel8.0/x86_64/dvd/BaseOS
- GPG 签名检查为启用状态
- GPG 密钥 URL 为 http://content/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release
- 存储库为启用状态

存储库2：

- 存储库的名称为 EX294_STREAM
- 描述为 EX294 stream software
- 基础 URL 为 http://content/rhel8.0/x86_64/dvd/AppStream
- GPG 签名检查为启用状态
- GPG 密钥 URL 为 http://content/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release
- 存储库为启用状态

解题方法：
```bash
[greg@bastion ansible]$ ansible-doc yum_repository

[greg@bastion ansible]$ vim adhoc.sh
#!/bin/bash
ansible all -m yum_repository -a 'name="EX294_BASE" description="EX294 base software" baseurl="http://content/rhel8.0/x86_64/dvd/BaseOS" gpgcheck=yes enabled=yes gpgkey="http://content/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release"'
ansible all -m yum_repository -a 'name="EX294_STREAM" description="EX294 stream software" baseurl="http://content/rhel8.0/x86_64/dvd/AppStream" gpgcheck=yes enabled=yes gpgkey="http://content/rhel8.0/x86_64/dvd/RPM-GPG-KEY-redhat-release"'

[greg@bastion ansible]$ chmod +x adhoc.sh
```
### 3、安装软件包
安装软件包
创建一个名为 /home/greg/ansible/packages.yml 的 playbook :

- 将 php 和 mariadb 软件包安装到 dev、test 和 prod 主机组中的主机上
- 将 RPM Development Tools 软件包组安装到 dev 主机组中的主机上
- 将 dev 主机组中主机上的所有软件包更新为最新版本

解题方法：
```yaml
---
- name: install packages
  hosts: dev,test,prod
  tasks:
    - name: install php
      yum:
        name: php
        state: present
    - name: install mariadb
      yum:
        name: mariadb
        state: present
- name: install dev tools and update all
  hosts: dev
  tasks:
    - name: install rpm dev tools
      yum:
        name: "@RPM Development Tools"
        state: present
    - name: update all packages
      yum:
        name: "*"
        state: latest
```
### 4、使用 RHEL 系统角色
安装 RHEL 系统角色软件包，并创建符合以下条件的 playbook /home/greg/ansible/timesync.yml ：

- 在所有受管节点上运行
- 使用 timesync 角色
- 配置该角色，以使用当前有效的 NTP 提供商
- 配置该角色，以使用时间服务器 172.25.254.254
- 配置该角色，以启用 iburst 参数

解题方法：
```
[greg@bastion ansible]$ sudo yum install rhel-system-roles
[greg@bastion ansible]$ vim ansible.cfg
#仅修改一行
roles_path = /home/greg/ansible/roles:/usr/share/ansible/roles

[greg@bastion ansible]$ ansible-galaxy list
[greg@bastion ansible]$ cp /usr/share/doc/rhel-system-roles/timesync/example-timesync-playbook.yml timesync.yml

[greg@bastion ansible]$ vim timesync.yml
---
- hosts: all
  vars:
    timesync_ntp_servers:
      - hostname: 172.25.254.254
        iburst: yes
  roles:
    - rhel-system-roles.timesync
```
### 5、使用 Ansible Galaxy 安装角色
使用 Ansible Galaxy 和要求文件 /home/greg/ansible/roles/requirements.yml 。从以下 URL 下载角色并安装到 /home/greg/ansible/roles ：

- http://materials/haproxy.tar 此角色的名称应当为 balancer
- http://materials/phpinfo.tar 此角色的名称应当为 phpinfo

解题方法：
```
[greg@bastion ansible]$ mkdir roles
[greg@bastion ansible]$ cd roles
[greg@bastion roles]$ vim requirements.yml
---
 name: balancer
  src: http://materials/haproxy.tar
- name: phpinfo
  src: http://materials/phpinfo.tar
  
[greg@bastion ansible]$ ansible-galaxy install -r /home/greg/ansible/roles/requirements.yml -p /home/greg/ansible/roles
```
### 6、创建和使用角色
根据下列要求，在 /home/greg/ansible/roles 中创建名为 apache 的角色：

- httpd 软件包已安装，设为在系统启动时启用并启动
- 防火墙已启用并正在运行，并使用允许访问 Web 服务器的规则
- 模板文件 index.html.j2 已存在，用于创建具有以下输出的文件 /var/www/html/index.html ：Welcome to HOSTNAME on IPADDRESS。其中，HOSTNAME 是受管节点的完全限定域名，IPADDRESS 则是受管节点的 IP 地址。

解题方法：
```yaml
[greg@bastion roles]$ ansible-galaxy init apache
[greg@bastion roles]$ vim apache/tasks/main.yml
---
- name: install httpd
  yum:
    name: httpd
    state: present
- name: start httpd service
  service:
    name: httpd
    state: started
    enabled: yes
- name: start firewalld
  firewalld:
    service: http
    permanent: yes
    state: enabled
    immediate: yes
- name: cp files
  template:
    src: index.html.j2
    dest: /var/www/html/index.html

[greg@bastion roles]$ vim apache/templates/index.html.j2
Welcome to {{ ansible_fqdn }} on {{ ansible_default_ipv4.address }}
```
### 7、从 Ansible Galaxy 使用角色
根据下列要求，创建一个名为 /home/greg/ansible/roles.yml 的 playbook ：

- playbook 中包含一个 play， 该 play 在 balancers 主机组中的主机上运行并将使用 balancer 角色。
   - 此角色配置一项服务，以在 webservers 主机组中的主机之间平衡 Web 服务器请求的负载。
   - 浏览到 balancers 主机组中的主机（例如 http://172.25.250.13 ）将生成以下输出：Welcom to serverb.lab.example.com on 172.25.250.11
   - 重新加载浏览器将从另一 Web 服务器生成输出：`Welcom to serverc.lab.example.com on 172.25.250.12`
- playbook 中包含一个 play， 该 play 在 webservers 主机组中的主机上运行并将使用 phpinfo 角色。
   - 请通过 URL /hello.php 浏览到 webservers 主机组中的主机将生成以下输出：Hello PHP World from FQDN
   - 其中，FQDN 是主机的完全限定名称。Hello PHP World from serverb.lab.example.com，另外还有 PHP 配置的各种详细信息，如安装的 PHP 版本等。
- 同样，浏览到 http://172.25.250.12/hello.php 会生成以下输出：Hello PHP World from serverc.lab.example.com，另外还有 PHP 配置的各种详细信息，如安装的 PHP 版本等。

解题方法：
```yaml
[greg@bastion ansible]$ vim roles.yml
---
- name: install balancer role
  hosts: balancers
  roles:
    - balancer
- name: install phpinfo role
  hosts: webservers
  roles:
    - phpinfo
- name: install apache role
  hosts: webservers
  roles:
    - apache
```
### 8、创建和使用逻辑卷
创建一个名为 /home/greg/ansible/lv.yml 的 playbook ，它将在所有受管节点上运行以执行下列任务：

- 创建符合以下要求的逻辑卷：
   - 逻辑卷创建在 research 卷组中
   - 逻辑卷名称为 data
   - 逻辑卷大小为 1500 MiB
- 使用 ext4 文件系统格式化逻辑卷
- 如果无法创建请求的逻辑卷大小，应显示错误信息Could not create logical volume of that size，并且应改为使用大小 800 MiB。
- 如果卷组 research 不存在，应显示错误信息Volume group done not exist。
- 不要以任何方式挂载逻辑卷

解题方法：
```yaml
[greg@bastion ansible]$ vim /home/greg/ansible/lv.yml
---
- name: create and use lvm
  hosts: all
  tasks:
    - name: create and mk lvm
      block: 
        - name: create lvm
          lvol:
            vg: research
            lv: data
            size: 1500m
        - name: filesystem
          filesystem:
            fstype: ext4
            dev: /dev/research/data
      rescue:
        - debug:
            msg: 'Could not create logical volume of that size'
        - name: create lvm new
          lvol:
            vg: research
            lv: data
            size: 800m
          when: ansible_lvm.vgs.research is defined
        - name: filesystem new
          filesystem:
            fstype: ext4
            dev: /dev/research/data
          when: ansible_lvm.vgs.research is defined
        - debug:
            msg: "Volume group does not exist"
          when: ansible_lvm.vgs.research is undefined
```
### 9、生成主机文件
生成主机文件

- 将一个初始模板文件从 http://materials/hosts.j2 下载到 /home/greg/ansible
- 完成该模板，以便用它生成以下文件：针对每个清单主机包含一行内容，其格式与 /etc/hosts 相同
- 创建名为 /home/greg/ansible/hosts.yml 的 playbook ，它将使用此模板在 dev 主机组中的主机上生成文件 /etc/myhosts 。

该 playbook 运行后， dev 主机组中主机上的文件 /etc/myhosts 应针对每个受管主机包含一行内容：
```yaml
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6

172.25.250.9    workstation.lab.example.com      workstation
172.25.250.10   servera.lab.example.com servera
172.25.250.11   serverb.lab.example.com serverb
172.25.250.12   serverc.lab.example.com serverc
172.25.250.13   serverd.lab.example.com serverd
```
注：清单主机名称的显示顺序不重要。
解题方法A：
```bash
[greg@bastion ansible]$ wget http://materials/hosts.j2
[greg@bastion ansible]$ vim hosts.j2
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6
172.25.250.9    workstation.lab.example.com      workstation
172.25.250.10   servera.lab.example.com servera
172.25.250.11   serverb.lab.example.com serverb
172.25.250.12   serverc.lab.example.com serverc
172.25.250.13   serverd.lab.example.com serverd

[greg@bastion ansible]$ vim hosts.yml
---
- name: 生成主机文件
  hosts: dev
  tasks:
          - name: one
            template:
                    src: /home/greg/ansible/hosts.j2
                    dest: /etc/myhosts
```
解题方法B：
```yaml
[greg@bastion ansible]$ wget http://materials/hosts.j2

[greg@bastion ansible]$ vim hosts.j2
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
::1 localhost localhost.localdomain localhost6 localhost6.localdomain
{% for host in groups['all'] %}
{{ hostvars[host]['ansible_facts']['default_ipv4']['address'] }} {{ hostvars[host]['ansible_facts']['fqdn'] }} {{ hostvars[host]['ansible_facts']['hostname'] }}
{% endfor %}

[greg@bastion ansible]$ vim hosts.yml
---
- name: generate hosts file
  hosts: all
  tasks:
    - name: template
      template:
        src: /home/greg/ansible/hosts.j2
        dest: /etc/myhosts
      when: 'inventory_hostname in groups.dev'
```
### 10、修改文件内容
按照下方所述，创建一个名为 /home/greg/ansible/issue.yml 的 playbook ：

- 该 playbook 将在所有清单主机上运行
- 该 playbook 会将 /etc/issue 的内容替换为下方所示的一行文本：
   - 在 dev 主机组中的主机上，这行文本显示 为：Development
   - 在 test 主机组中的主机上，这行文本显示 为：Test
   - 在 prod 主机组中的主机上，这行文本显示 为：Production

解题方法：
```yaml
[greg@bastion ansible]$ vim issue.yml
---
- name: change files content
  hosts: all
  tasks:
    - name: copy dev
      copy:
        content: 'Development'
        dest: /etc/issue
      when: 'inventory_hostname in groups.dev'
    - name: copy test
      copy:
        content: 'Test'
        dest: /etc/issue
      when: 'inventory_hostname in groups.test'
    - name: copy prod
      copy:
        content: 'Production'
        dest: /etc/issue
      when: 'inventory_hostname in groups.prod'
```
### 11、创建 Web 内容目录(待确认是否需要创建组)
按照下方所述，创建一个名为 /home/greg/ansible/webcontent.yml 的 playbook ：

- 该 playbook 在 dev 主机组中的受管节点上运行
- 创建符合下列要求的目录 /webdev ：
   - 所有者为 webdev 组
   - 具有常规权限：owner=read+write+execute ， group=read+write+execute ，other=read+execute
   - 具有特殊权限：设置组 ID
- 用符号链接将 /var/www/html/webdev 链接到 /webdev
- 创建文件 /webdev/index.html ，其中包含如下所示的单行文件： Development
- 在 dev 主机组中主机上浏览此目录（例如 http://172.25.250.9/webdev/ ）将生成以下输出：Development

解题方法：
```yaml
[greg@bastion ansible]$ vim webcontent.yml
---
- name: 创建 Web 内容目录
hosts: dev
tasks:
- name: one
file:
path: /webdev
state: directory
group: webdev
mode: '2775'
- name: two
file:
src: /webdev
dest: /var/www/html/webdev
state: link
- name: three
copy:
content: 'Development'
dest: /webdev/index.html
                    setype: httpd_sys_content_t
                    
                    
                    
---
- name: craete webcontent
  hosts: dev
  tasks:
    - name: create group
      group:
        name: webdev
        state: present
    - name: create dir
      file:
        path: /webdev
        group: webdev
        mode: '2775'
        state: directory
    - name: create link
      file:
        src: /webdev
        dest: /var/www/html/webdev
        state: link
    - name: create file
      copy:
        content: 'Development'
        dest: /webdev/index.html
        setype: httpd_sys_content_t
```
### 12、生成硬件报告
创建一个名为 /home/greg/ansible/hwreport.yml 的 playbook ，它将在所有受管节点上生成含有以下信息的输出文件 /root/hwreport.txt ：

- 清单主机名称
- 以 MB 表示的总内存大小
- BIOS 版本
- 磁盘设备 vda 的大小
- 磁盘设备 vdb 的大小
- 输出文件中的每一行含有一个 key=value 对。

您的 playbook 应当：

- 从 http://materials/hwreport.empty 下载文件，并将它保存为 /root/hwreport.txt
- 使用正确的值改为 /root/hwreport.txt
- 如果硬件项不存在，相关的值应设为 NONE

解题方法：
```yaml
[greg@bastion ansible]$ vim hwreport.yml
---
- name: generate hw report 
  hosts: all
  vars:
    hw_all:
      - hw_name: HOST
        hw_cont: "{{ inventory_hostname | default('NONE',true) }}"
      - hw_name: MEMORY
        hw_cont: "{{ ansible_memtotal_mb | default('NONE',true) }}"
      - hw_name: BIOS
        hw_cont: "{{ ansible_bios_version | default('NONE',true) }}"
      - hw_name: DISK_SIZE_VDA
        hw_cont: "{{ ansible_devices.vda.size | default('NONE',true) }}"
      - hw_name: DISK_SIZE_VDB
        hw_cont: "{{ ansible_devices.vdb.size | default('NONE',true) }}"
  tasks:
    - name: download empty
      get_url:
        url: http://materials/hwreport.empty
        dest: /root/hwreport.txt
    - name: generate
      lineinfile:
        path: /root/hwreport.txt
        regexp: "^{{ item.hw_name }}"
        line: "{{ item.hw_name }}={{ item.hw_cont }}"
      loop: "{{ hw_all }}"
```
### 13、创建密码库
按照下方所述，创建一个 Ansible 库来存储用户密码：

- 库名称为 /home/greg/ansible/locker.yml
- 库中含有两个变量，名称如下：
   - pw_developer，值为 Imadev
   - pw_manager，值为 Imamgr
- 用于加密和解密该库的密码为 whenyouwishuponastar
- 密码存储在文件 /home/greg/ansible/secret.txt 中

解题方法：
```bash
[greg@bastion ansible]$ vim ansible.cfg
#仅修改一行
vault_password_file = /home/greg/ansible/secret.txt

[greg@bastion ansible]$ vim locker.yml
---
pw_developer: Imadev
pw_manager: Imamgr
[greg@bastion ansible]$ echo whenyouwishuponastar > secret.txt
[greg@bastion ansible]$ ansible-vault encrypt locker.yml
```
### 14、创建用户帐户
从 http://materials/user_list.yml 下载要创建的用户的列表，并将它保存到 /home/greg/ansible

- 在本次考试中使用在其他位置创建的密码库 /home/greg/ansible/locker.yml 。创建名为 /home/greg/ansible/users.yml 的 playbook ，从而按以下所述创建用户帐户：
   - 职位描述为 developer 的用户应当：
      - 在 dev 和 test 主机组中的受管节点上创建
      - 从 pw_developer 变量分配密码
      - 是补充组 devops 的成员
   - 职位描述为 manager 的用户应当：
      - 在 prod 主机组中的受管节点上创建
      - 从 pw_manager 变量分配密码
      - 是补充组 opsmgr 的成员
- 密码采用 SHA512 哈希格式。
- 您的 playbook 应能够在本次考试中使用在其他位置创建的库密码文件 /home/greg/ansible/secret.txt 正常运行。

解题方法：
```yaml
[greg@bastion ansible]$ wget http://materials/user_list.yml
[greg@bastion ansible]$ vim users.yml
---
- name: craete user account
  hosts: all
  vars_files:
    - /home/greg/ansible//user_list.yml
    - /home/greg/ansible/locker.yml
  tasks:
    - name: create group1
      group:
        name: devops
        state: present
      loop: "{{ users }}"
      when: item.job == 'developer' and (inventory_hostname in groups.dev or inventory_hostname in groups.test)
    - name: create user1
      user:
        name: "{{ item.name }}" 
        password: "{{ 'pw_developer' | password_hash('sha512','mysecretsalt') }}"
        groups: devops
      loop: "{{ users }}"
      when: item.job == 'developer' and (inventory_hostname in groups.dev or inventory_hostname in groups.test)
    - name: create group2
      group:
        name: opsmgr
        state: present
      loop: "{{ users }}"
      when: item.job == 'manager' and inventory_hostname in groups.prod
    - name: create user2
      user:
        name: "{{ item.name }}" 
        password: "{{ 'pw_manager' | password_hash('sha512','mysecretsalt') }}"
        groups: opsmgr
      loop: "{{ users }}"
      when: item.job == 'manager' and inventory_hostname in groups.prod
```
### 15、更新 Ansible 库的密钥
按照下方所述，更新现有 Ansible 库的密钥：

- 从 http://materials/salaries.yml 下载 Ansible 库到 /home/greg/ansible
- 当前的库密码为 insecure8sure
- 新的库密码为 bbs2you9527
- 库使用新密码保持加密状态

解题方法：
```bash
[greg@bastion ansible]$ wget http://materials/salaries.yml
[greg@bastion ansible]$ ansible-vault rekey --ask-vault-pass salaries.yml
Vault password: 粘贴当前密码
New Vault password: 粘贴新密码
Confirm New Vault password: 粘贴新密码

[greg@bastion ansible]$ ansible-vault view salaries.yml
```
### 16、配置 cron 作业（新增）
创建一个名为 /home/greg/ansible/cron.yml 的 playbook ，

- 配置 `cron` 作业，该作业`每隔 2 分钟`运行并执行以下命令：
- `logger "EX294 in progress"`，以用户 `natasha` 身份运行

解题方法：
```yaml
[greg@bastion ansible]$ vim cron.yml
---
- name: create cronjob
  hosts: all
  tasks:
    - name: create user
      user:
        name: natasha
        state: present
    - name: run cronjob
      cron:
        name: "logger info"
        minute: "*/2"
        job: 'logger "EX294 in progress"'
        user: natasha
```
### 17、创建分区（隐藏题、有概率出现）
在balancers主机上，划分新的分区，设备为/dev/vdd，编号1，大小1500m，格式化成ext4，mount到/newpart1目录，如果空间不够，分800m，如果没有vdd，报错
解题方法：
```yaml
---
- name: create partition
  hosts: balancers
  tasks:
    - name: start create partitoin
      block:
        - name: parted
          parted:
            device: /dev/vdd
            number: 1
            state: present
            part_end: 1500MiB
        - name: filesystem
          filesystem:
            fstype: ext4
            dev: /dev/vdd1
        - name: mount
          mount:
            path: /newpart1
            src: /dev/vdd1
            fstype: ext4
            state: mounted
      rescue:
        - debug:
            msg: "can not create that partition with space"
        - name: parted
          parted:
            device: /dev/vdd
            number: 1
            state: present
            part_end: 800MiB
          when: ansible_facts.devices.vdd is defined
        - name: filesystem
          filesystem:
            fstype: ext4
            dev: /dev/vdd1
          when: ansible_facts.devices.vdd is defined
        - name: mount
          mount:
            path: /newpart1
            src: /dev/vdd1
            fstype: ext4
            state: mounted
          when: ansible_facts.devices.vdd is defined
        - debug:
            msg: "vdd not exsit"
          when: ansible_facts.devices.vdd is undefined
```
### 18、安装RHEL角色（隐藏题、有概率出现）
安装RHEL角色，并使用SELinux角色，要求在所有节点运行，将SELinux设置为强制模式。
```bash
[greg@bastion ansible]$ dnf install rhel-system-roles -y
[greg@bastion ansible]$ cp -rf /usr/share/ansible/roles/linux-system-roles.selinux ./
[greg@bastion ansible]$ vim selinux.yml
#安装出来后可以改也可以不改，改的话就剩下这些就可以，多余的删除。
---
- hosts: all
  vars:
          selinux_policy: targeted
          selinux_state: enforcing
  roles:
    - role: roles/rhel-system-roles.selinux
  tasks:
          - name: apply SElinux role
            block:
                    - include_roles:
                            name: roles/rhel-system-roles.selinux
            rescue:
                    - name: check
                      fail:
                      when: not selinux_reboot_required
                    - name: reboot
                      reboot:
                    - name: changed
                      include_role:
                        name: roles/rhel-system-roles.selinux
```


title: useful_commands
date: 2016-05-22 01:04:22
tags:
- command
- grep
- awk
- xargs
- ssh
- git
---
>grep awk xrags ssh git

<!--more-->

### grep 
grep -ERano  -A(B|C) 5 --include="\*.py" "regular_express" ./

### awk
xxx |awk '{print $1}'

### xargs
xxxx | xargs kill

### ssh config
~/.ssh/config
```
Host github
    HostName github.com
    Port 22
    User git
    IdentityFile ~/.ssh/github
```
密钥登录
* ssh-keygen -t rsa
* ssh-copy-id -i .ssh/id_rsa.pub root@192.168.72.129
* 修改登录主机的 /etc/ssh/sshd_config 去掉 AuthorizedKeysFile 一行注释(RSAAuthentication yes PubkeyAuthentication yes)
* 禁止采用password登录
>ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
* 重启ssh服务

###git服务器搭建
* sudo apt-get -y install openssh-server openssh-client
* sudo apt-get -y install git
* sudo adduser —system —shell /bin/sh —group —disabled-password —home /home/git git
* 生成rsa，ssh-keygen -t rsa
* sudo apt-get -y install gitolite
* sudo su - gitolite
* gl-setup /tmp/ubuntugege.pub
* exit, git clone ssh://git@localhost/gitolite-admin.git
* 修改 /gitolite-admin/conf/gitolite.conf 增加代码仓库
* 配置keys，增加用户
* 完成

### git补丁生成
patch 生成
>git format-patch HEAD^ <==最近的1次commit的patch

>git format-patch HEAD^^ <==最近的2次commit的patch

>git format-patch HEAD^ <==最近的3次commit的patch

>git format-patch HEAD^ <==最近的4次commit的patch

打补丁
* git am —abort
git apply —reject xxxx.patch
* 根据apply的输出提示以及mm/sparse.c.rej文件中的描述，手动修正代码。
* 改好之后，用 git add 把 mm/sparse.c 添加到缓冲区，同时也要把其他没有冲突合并成功了的文件也加进来，因为在作 apply 操作的时候他们也发生了变化：
$ git add mm/sparse.c
* 最后：$ git am —resolved
 Applying: tree 冲突解决成功

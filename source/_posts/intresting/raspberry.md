title: raspberry
date: 2015-10-16 01:59:47
tags:
- raspberry
---

>树莓派2

<!--more-->

#####当前已配置项：
>redis
celery
django
nsq

#####挂载硬盘
>sudo apt-get install ntfs-3g
ntfs 硬盘驱动
sudo apt-get install samba samba-common-bin
samba服务，配置/etc/samba/smb.conf

#####kivy
>apt-get install debian-keyring
gpg —keyserver pgp.mit.edu —recv-keys 0C667A3E
gpg —armor —eeport 0C667A3E | apt-key add -

>运行示例问题
>>https://github.com/kivy/kivy/issues/2115



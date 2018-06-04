# Ubuntu

14.04 LTS, trusty

16.04 LTS, xenial

安装open jdk8：
```
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-8-jdk
sudo update-alternatives --config java
sudo update-alternatives --config javac
```

Oracle Jdk:
```
sudo add-apt-repository ppa:webupd8team/java -y
sudo apt-get update
sudo apt-get install oracle-java8-installer
sudo apt-get install oracle-java8-set-default
jave -version
```

## apt-get
sudo apt-get update
sudo apt-get install -y docker.io


## sudoers
对应的文件为`/etc/visudoers`，使用visudo进行修改。
`%group`

```
%admin  ALL=(ALL) ALL
```
修改为：
```
%admin  ALL=(ALL) NOPASSWD:ALL
```

sudo service sudo restart

## 重启服务器 
* shutdown -r now

## manage docker as a non-root user

sudo gpasswd -a $USER docker
newgrp docker
docker run hello-world

## 时区
```
tzselect   ## 查看时区，不会修改
sudo cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime ## 这里才会真正修改时区
sudo ntpdate time.windows.com
```

sudo date -s MM/DD/YY //修改日期
sudo date -s hh:mm:ss //修改时间
sudo hwclock --systohc //修改硬件时间。非常重要，如果没有这一步的话，后面时间还是不准


* dpkg-reconfigure tzdata ## tzselect 不会修改时区，只是查看时区。这个命令是修改时区
* zdump /etc/localtime
* zdump /usr/share/zoneinfo/Asia/Shanghai ##  查看时区当前的事件

## 国内源

* http://mirrors.163.com/ 网易镜像已经很久没更新了

修改文件：/etc/apt/sources.list

推荐使用阿里云镜像：
* https://opsx.alibaba.com/mirror


sudo apt-get update
sudo apt-get install build-essential


## sshd
第一次运行要生成host key：
sudo ssh-keygen -A
sudo /etc/init.d/ssh start


sudo apt install openssh-client openssh-server
man sshd_config



## 问题
Failed to allocate directory watch: Too many open files

ulimit -a 

ulimit -n
ulimit -n 8192

/etc/security/limits.conf


sysctl fs.inotify

```
wudeng@s3-10-80-0-160:~/S3Server$ sudo service rsyslog restart
Failed to allocate directory watch: Too many open files

wudeng@s3-10-80-0-160:~/S3Server$ sysctl fs.inotify.max_user_instances
fs.inotify.max_user_instances = 128

wudeng@s3-10-80-0-160:~/S3Server$ sysctl fs.inotify.max_user_instances=512
sysctl: setting key "fs.inotify.max_user_instances": Read-only file system

wudeng@s3-10-80-0-160:~/S3Server$ cat /proc/sys/fs/inotify/max_user_instances 
128

wudeng@s3-10-80-0-160:~/S3Server$ echo 512 > /proc/sys/fs/inotify/max_user_instances
bash: /proc/sys/fs/inotify/max_user_instances: Read-only file system

```

## sysctl

修改内核参数。
sysctl variable

## systemd
unit file

/etc/systemd/system/redis.service

systemctl
man systemctl
systemctl list-units

## locale

安装zh_CN.UTF-8：
sudo apt-get install language-pack-zh-hans



wudeng@s3-10-80-0-160:~/S3Server$ locale -a
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_COLLATE to default locale: No such file or directory
C
C.UTF-8
POSIX
zh_CN.utf8
zh_SG.utf8

没有安装en_US.utf8

安装en_US.utf8:

wudeng@s3-10-80-0-160:~/S3Server$ sudo locale-gen en_US.UTF-8
/bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
Generating locales (this might take a while)...
  en_US.UTF-8... done
Generation complete.

再检查一下，正常了：
wudeng@s3-10-80-0-160:~/S3Server$ locale -a                  
C
C.UTF-8
en_US.utf8
POSIX
zh_CN.utf8
zh_SG.utf8



查看当前系统语言环境：

locale

vim /etc/default/locale
```
LANG="en_US.UTF-8"
LANGUAGE="en_US:en"
```

vim ~/.bashrc
```
export LC_TYPE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```


sudo locale-gen -en_US:en
sudo locale-gen --purge

/usr/share/i18n/SUPPORTED



sudo vim /etc/locale.gen
sudo locale-gen




sudo apt-get install pkg-config



```bash
#!/bin/bash

## login key
sudo cat "ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC136qXZbULTRmbYnwefTIVNje+03q8RS3GM+toy53afjlRPevbYqPkkFpN6eesVrAfXjOiNjpbpQgJaEt9YXnUbBUWSpPh58K8a0ZIJexAuMHSnXtNACsQPyh6eoZYNMqohz0U82Eo+98/DShS1/0rEEgzEAilozEYr+GhGb8ICMc3KfHsEjMsjb3+xg5kuZgA9Kfz4Ze82kDLLj+gKalLc+YADbgWagFZbPSIT2FM4kEXT8gRNnQO06eOmvHTan4/ZGRXADkJm4cN6eAaeINRh1eoxg7jIIB0NBi4MtC8hMSTL63Knf9B8d56CbgPga7lHggEfSg8Z5G/AhatMYTx wudeng@s3-10-80-0-160" >> /home/ubuntu/.ssh/authorized_keys

## git alias
git config --global user.name 'wudeng'
git config --global user.email 'wudeng256@gmail.com'
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit 
git config --global alias.st status 
git config --global core.filemode false
git config --global push.default simple
git config --global core.autocrlf input
git config --global core.editor "vim"


## timezone
sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

## source list
sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak
sudo tee /etc/apt/sources.list > /dev/null << EOF
deb http://mirrors.aliyun.com/ubuntu/ xenial main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main

deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main

deb http://mirrors.aliyun.com/ubuntu/ xenial universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial universe
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates universe

deb http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main
deb http://mirrors.aliyun.com/ubuntu/ xenial-security universe
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security universe
EOF

## install
sudo apt update
sudo apt install build-essential lrzsz ack-grep mongodb libreadline-dev autoconf cmake -y
```

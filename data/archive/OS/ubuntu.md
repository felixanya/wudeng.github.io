# Ubuntu

14.04 LTS, trusty

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
tzselect
sudo cp /usr/share/zoneinfo/Asia/Shanghai  /etc/localtime
sudo ntpdate time.windows.com
```

sudo date -s MM/DD/YY //修改日期
sudo date -s hh:mm:ss //修改时间
sudo hwclock --systohc //修改硬件时间。非常重要，如果没有这一步的话，后面时间还是不准


* dpkg-reconfigure tzdata
* zdump /etc/localtime
* zdump /usr/share/timezone/Asia/Shanghai

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

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
* http://mirrors.163.com/


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

# sysctl

修改内核参数。
sysctl variable
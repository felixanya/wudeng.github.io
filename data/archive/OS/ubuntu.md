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
* visudo
* 重启服务器 shutdown -r now

## manage docker as a non-root user

sudo gpasswd -a $USER docker
newgrp docker
docker run hello-world

## 时区
* dpkg-reconfigure tzdata
* zdump /etc/localtime
* zdump /usr/share/timezone/Asia/Shanghai

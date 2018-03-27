# jenkins on centos

Jenkins是一个独立的开源自动化服务器，可用于自动化各种任务，如构建，测试和部署软件。

## 安装Java8
查看java版本：`java -version`，如果低于Java8，需要先需要安装Java8：
```
yum install -y java-1.8.0-openjdk java-1.8.0-openjdk-devel
```

## 安装jenkins
```
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat/jenkins-ci.org.key
yum install -y jenkins
```

## 启动
```
service jenkins start
```

浏览器打开：http://192.168.4.168:8080/，根据引导输入密码，创建管理员。
选择插件，等待安装完毕。

## 构建

写编译脚本的时候无需执行svn update
有些命令找不到，可以创建符号链接，比如:

ln -s /usr/local/lib/erlang/bin/escript /usr/bin/escript
ln -s /usr/local/lib/erlang/bin/erl /usr/bin/erl

Jenkins支持远程触发构建，这个功能非常好用：
可以在svn中创建post-commit钩子，这样每次提交svn都会触发。
也可以通过脚本手动构建。

远程构建有两种方式：
* 用户认证的方式，需要提供用户名和API Token。

http://admin:e6b0c2432be915265ffa044487874448@192.168.4.168:8080/job/tianshu_server/build?token=servermanagerbuild

* 匿名的方式。直接通过Token远程控制构建，需要先安装插件：Build Authorization Token Root。
这样匿名用户也可以远程执行构建了：

curl "http://192.168.4.168:8080/buildByToken/build?job=tianshu_server&token=servermanagerbuild"

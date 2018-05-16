# Vagrant

虚拟机管理软件，区别于Docker的虚拟机容器，更加重量级。用于开发环境搭建。

## 安装

windows：
安装vagrant。
安装Virtualbox.

vagrant init ubuntu/xenial64
vagrant up

## box
相当于虚拟机镜像。直接下载不了，用迅雷或者FDM等下载工具下载好box，添加进去。
vagrant box
- add
- list
- remove
- update
- outdated
- prune
- repackage

## Vagrangfile
* 配置端口映射
    - config.vm.network "forwarded_port", guest: 80, host: 8080
    - config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"
* 网络
* 文件挂载
    - config.vm.synced_folder "../data", "/vagrant_data"

## 命令

| vagrant port          | 查看端口映射           |
| vagrant status        | 查看虚拟机状态         |
| vagrant ssh           | 连接虚拟机             |
| vagrant up            | 启动虚拟机             |
| vagrant halt          | 停止虚拟机             |
| vagrant suspend       | 挂起虚拟机             |
| vagrant resume        | 恢复挂起的虚拟机       |
| vagrant destroy       | 停止并删除虚拟机       |
| vagrant global-status | 查看全局虚拟机状态     |
| vagrant reload        | 重启并加载新的配置文件 |

或者可以通过其他ssh工具如SecureCRT、putty等连接上去，
对应的私钥存储在当前项目目录的.vagrant\machines\default\virtualbox\private_key, 用户名为vagrant，密码也是vagrant.

mkdir ubuntu
cd ubuntu
vagrant box list
vagrant init ubuntu         -- 生成Vagrangfile
vagrant up
vagrant ssh


## /etc/sudoers
加入这两行到文件**最后**
```
vagrant ALL=(ALL) NOPASSWD: ALL
Defaults:vagrant !requiretty
```


## 参考文档
* http://www.vagrantbox.es/
* https://pan.baidu.com/s/1gfNCud1

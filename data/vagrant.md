# Vagrant

虚拟机管理软件，区别于Docker的虚拟机容器，更加重量级。用于开发环境搭建。
比如可以在windows下用你喜欢的编辑器来编辑文件，然后直接在linux里面执行。
这个相比用sftp上传要方便很多。

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


## ubuntu 16.04 xenial
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

## 共享文件夹问题

在Vagrantfile中配置syncd_folder是可以直接在虚拟机中访问到的。但是有一个问题：无法为共享文件夹中的文件创建符号链接。
这是因为Virtualbox的共享文件夹不支持文件链接。解决这个问题需要：
* 用管理员启动虚拟机，运行cmd的时候使用右键选择管理员。
* 修改配置
```ruby
config.vm.synced_folder "../work", "/work"
config.vm.synced_folder "../playground", "/playground"
config.vm.provider "virtualbox" do |vb|
    vb.gui = false
    vb.memory = "2048"
    vb.customize ["setextradata", :id, "VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root", "1"]
end
```
这个v-root我也不知道是啥，改成自己的共享文件夹名字应该也是可以的。

打开VirtualBox，可以找到vagrant对应的虚拟机名字，然后通过名字可以查询相应的配置，发现都已经是1了。怀疑这个配置已经是默认的了。
那么只要用管理员权限打开vagrant up就可以了。（有待验证）
```
D:\vagrant>"C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" getextradata "vagrant_default_1526390412522_24907" enumerate
Key: GUI/LastNormalWindowPosition, Value: 640,257,720,445
Key: VBoxInternal2/SharedFoldersEnableSymlinksCreate/playground, Value: 1
Key: VBoxInternal2/SharedFoldersEnableSymlinksCreate/v-root, Value: 1
Key: VBoxInternal2/SharedFoldersEnableSymlinksCreate/vagrant, Value: 1
Key: VBoxInternal2/SharedFoldersEnableSymlinksCreate/work, Value: 1
```


the solution:
```
# if running inside VirtualBox on a shared folder
# you must enable symlinks on the shared folder
$ VBoxManage setextradata "<vm name>" VBoxInternal2/SharedFoldersEnableSymlinksCreate/<shared folder> 1

# verify with
$ VBoxManage getextradata "<vm name>" enumerate
```



To summarize:

Symlinking works in VirtualBox but is disabled by default "for security reasons". As such, you need to enable it manually and run VirtualBox as administrator. The simple steps are as follows:

On the host machine (assuming Windows), but some variation of this will work in any OS: cd C:\Program Files\Oracle\VirtualBox

Run the following command:
VBoxManage setextradata VM_NAME VBoxInternal2/SharedFoldersEnableSymlinksCreate/SHARE_NAME 1
replacing VM_NAME with your Virtual Machine's name (if you don't know this, in VBox go to Machine > Settings > General > Basic > Name --- also replace SHARE_NAME with the name of your shared folder, if you don't remember this, go to Machine > Settings > Shared Folders.

Restart your VM AND VirtualBox, but run it as administrator. On Windows hosts, just right click the icon for VirtualBox and select Run as Administrator.


参考：https://github.com/puphpet/puphpet/issues/2726

## 参考文档
* http://www.vagrantbox.es/
* https://pan.baidu.com/s/1gfNCud1 ubuntu-16.06 xenial box下载

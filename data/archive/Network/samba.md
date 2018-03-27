# Install samba on CentOS 6

## 安装Samba

安装:
```
sudo yum install samba samba-client samba-common
```

检查版本:
```
smbd --version
```

配置开机启动:
```
sudo chkconfig smb on
sudo chkconfig nmb on
```

禁用SELinux，修改`/etc/selinux/config`:
```
SELinux=disabled
```

samba使用137,138, 139, 445端口：
(Old system)
* Port 137 – UDP NetBIOS name service (WINS)
* Port 138 – UDP NetBIOS datagram
* Port 139 – TCP NetBIOS Session (TCP), Windows File and Printer Sharing (this is the most insecure port)

(Active Directory)
* Port 445 - Microsoft-DS Active Directory, Windows shares (TCP)
* Port 445 - Microsoft-DS SMB file sharing (UDP)

配置防火墙:
```
sudo iptables -I INPUT 4 -m state --state NEW -m udp -p udp --dport 137 -j ACCEPT
sudo iptables -I INPUT 5 -m state --state NEW -m udp -p udp --dport 138 -j ACCEPT
sudo iptables -I INPUT 6 -m state --state NEW -m tcp -p tcp --dport 139 -j ACCEPT

sudo iptables -I INPUT 7 -m state --state NEW -m tcp -p tcp --dport 445 -j ACCEPT
sudo iptables -I INPUT 8 -m state --state NEW -m udp -p udp --dport 445 -j ACCEPT
sudo service iptables save
```

## 配置匿名共享

建立匿名共享文件夹，设置所有人可读写。
```
mkdir -p /data/share
chmod 0777 /data/share
```

检查windows的工作组：

```
net config workstation
```

一般都是WORKGROUP。

备份`/etc/samba/smb.conf`, 编辑内容如下:
```
[global]
workgroup = WORKGROUP
security = share
map to guest = bad user

[MyShare]
path = /data/share
browsable = yes
writeable = yes
guest ok = yes
read only = no
```


重启服务：
```
service smb restart
service nmb restart
```

## 配置用户和组

```
sudo groupadd smbgrp
```

```
cd /data/
sudo mkdir secure
sudo chown -R arbab:smbgrp secure/ 
ls -l 
sudo chmod -R 0770 secure/
ls -l
```

新增用户，设置密码
```
sudo usermod -a -G smbgrp ts
sudo smbpasswd -a ts
```

编辑配置文件：
```
[Secure]
path = /samba/secure
valid users = @smbgrp
guest ok = no
writable = yes
browsable = yes
```

检查语法：

```
testparm
```

重启服务。


Windows上面可以通过配置网络驱动器来访问共享文件夹。

## 参考文档

* https://rbgeek.wordpress.com/2012/05/25/how-to-install-samba-server-on-centos-6/
* https://wiki.centos.org/HowTos/SetUpSamba

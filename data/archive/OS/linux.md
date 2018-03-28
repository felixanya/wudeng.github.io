# Linux

## 查看系统版本
* lsb_release -a
* `cat /etc/*release`
* uname -a

/etc/sudoers
* use `visudo` to edit
* username hosts=(users:groups) commands
* %group
```
root ALL=(ALL:ALL) ALL
```
The root user can execute from ALL terminals, acting as ALL(any) users, and run ALL(any) command.

```
go2linux ALL=(ALL) NOPASSWD: ALL
```
not to be asked password

visudo
```
export VISUAL=vim; visudo
export VISUAL=nano; visudo
```


useradd vagrant
groupadd admin
usermod -a -G admin vagrant

* list all users: `compgen -u`
* list all groups: `compgen -g`
* /etc/group

## 文件系统

* 基本分区,主分区: 可以马上使用不能再分区
* 扩充分区：一个或多个逻辑分区的容器。必须再分区才能使用，再分下去叫做逻辑分区，没有数量限制
* 基本分区 + 扩充分区 <= 4 ?

设备管理
    IDE: hdx~   hd 类型（ide硬盘）x(盘号) ~ （分区，前四个是主分区或扩展分区，从5开始就是逻辑分区）
        a : 基本盘
        b : 基本从属盘
        c ：辅助主盘
        d ：辅助从属盘
        hda3 ：第一个ide硬盘上的第三个主分区或扩展分区
        hdb2：第二个ide硬盘上的第二个主分区或扩展分区
    SCSI: sd*
分区数量
    1-16的序列号码：hda1~hda4 主分区，hda5~hda16 逻辑分区
    每个硬盘设备最多能有4个主分区
    主分区：启动操作系统，引导程序
常用分区
    /boot 操作系统内核和在启动系统过程中所有要用到的文件。
    /usr
    /home
    /var/log
    /tmp
    /bin
    /dev
    /opt
    /sbin

Fdisk 不能理解GPT根式化磁盘
    p 显示硬盘分区表信息
    d 删除分区
    n 新分区 p/e
    t 分区类型
    l 支持的分区类型
    w 保存
    q 直接退出
    m 显示选项

fdisk -l /dev/sda
cat /proc/partitions 查看所有磁盘及分区情况
df -lh

parted
gparted
gdisk

GPT GUID Partition Table 支持高达128个分区
MBR 磁盘主引导记录 master boot record

lvm: logic volumn manager

cat /etc/fstab


## 启动流程

BIOS -> MBR(GRUB) -> Kernel -> Init -> Runlevel
- BIOS: 检查CPU、内存
    POST 上电自检Power-On Self Test，CPU/内存、显卡，键盘鼠标等，完成后从存储器中清除
    Runtime服务一直保留
- MBR 512bytes
    446byte用于BootLoader程序，
    64bytes用于存储分区表信息，DPT Disk Partition Table
    最后2bytes用于MBR有效性检查。
- GRUB Grand Unified BootLoader, lilo
    stage1: 446b
    stage1.5
    stage2
- Kernel
    initrd (Initial RAM Disk)
    /boot/grub/grub.conf
- Init
- Runlevel
    0 - 关机模式 /etc/rc.d/rc0.d/ S*启动时需要start的服务 K*关机是需要关闭的服务，数字越小越先执行
    1 单一用户模式 /etc/rc.d/rc1.d/
    2 多用户模式（无网络）
    3 多用户模式（命令行）
    4 保留
    5 多用户模式（图形界面）
    6 重启


## 网络

Mac地址：more /sys/class/net/eth1/address


* ifdown eth0
* ifup eth0
这两个命令都是找到/etc/sysconfig/network-scripts/ifcfg-eth0的内容进行设定。如果以ifconfig eth0 ...
来设定或者修改了网络接口以后，就无法再以ifdown eth0的方式来关闭了。因为ifdown会分析对比目前的网络参数
与ifcfg-eth0是否相符，不符合的话就会放弃该次动作。因此使用ifconfig修改完毕后，应该要以ifconfig eth0 down才能够关闭接口。


静态ip：/etc/sysconfig/network-scripts/ifcfg-eth0
service network restart

修改ip：
    即时生效: `ifconfig eth0 192.168.1.155 netmask 255.255.255.0`
    重启生效: 修改/etc/sysconfig/network-scripts/ifcfg-eth0
修改default gateway：
    即时生效: `route add default gw 192.168.1.1`
    重启生效: 修改/etc/sysconfig/network-scripts/ifcfg-eth0
修改dns
    修改/etc/resolv.conf 修改后即时生效，重启同样有效
修改host name
    即时生效: hostname test1
    重启生效: 修改/etc/sysconfig/network

## 日志
先分类，再分级别，
authpriv
daemon
syslog
mail
cron
news

local0-local7 系统保留


级别：
debug
info
notice
warn
err
crit
alert
emerg

rhel5 : syslog
rhel6: rsyslog /etc/rsyslog.conf

service rsyslog restart
chkconfig rsyslog on

kern.* 内核类型的所有级别日志

.none 不记录任何信息
.
.=
.! 不记录

wall -send message to everyone

DATE TIME HOSTNAME APP PID:MESSAGE

local0


logrotate 创建新文件，改名旧文件，重启服务
/etc/logrotate.conf

## 开关机记录
last
lastb
最近一次开机时间：last -1 reboot 或 who -b

VEHT pair (virtual ethernet pair)
    a pair of ethernet devices
    works at link layer

network namespaces - logically isolated network stack

uses
    connects two seperate network namespacs
    connects a container or vm to a virtual switch (ovs)

used with linux containers e.g docker, openstack etc

command:

ip link
ip link add tab1 type veth peer name tab2
ip link

ip netns
ip netns add red
ip netns add blue
ip link
ip link set tap1 netns red
ip link set tap2 netns blue
ip link



ip netns exec red ip a
ip netns exec red ip link set tab1 up
ip netns exec red ifconfig 192.168.1.2/24

ip netns exec blue ip b
ip netns exec blue ip link set tab2 up
ip netns exec blue ifconfig 192.168.1.3/24

ip netns exec red ping 192.168.1.3
ip netns exec blue ping 192.168.1.2



sysctl net.ipv4.ip_forward




ip netns

sudo ip netns add red
sudo ip netns add green

ip netns
ls /var/run/netns

sudo ip netns exec red ip link
sudo ip netns exec green ip link


open vSwitch

veth pair - pipe


ovs-vsctl add-port OVS1 tap-r
ovs-vsctl show


ip link set tap-r netns dhcp-r
ip link set tag-g netns dhcp-g

ip netns exec dhcp-r bash
ip link set dev lo up
ip link set dev tap-r up

exit


screen
  -l
  ctrl+a,d
* https://lug.ustc.edu.cn/wiki/start

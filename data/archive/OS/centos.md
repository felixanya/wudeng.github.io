# CentOS

## 查看当前系统版本
* `cat /etc/*release`
* rpm --query centos-release
* lsb_release -a

## 升级glibc
执行程序的时候遇到错误：`libc.so.6: version GLIBC_2.14 not found`
从上面报错可以看出，程序运行时候，没有找到“GLIBC_2.14”这个版本库，而默认的Centos6.5 glibc版本最高为2.12, 所以需要更新系统glibc库。

glibc是gnu发布的libc库，即c运行库，glibc是linux系统中最底层的api，几乎其它任何运行库都会依赖于glibc。
glibc除了封装linux操作系统所提供的系统服务外，它本身也提供了许多其它一些必要功能服务的实现。

很多linux的基本命令，比如cp, rm, ll,ln等，都得依赖于它，如果操作错误或者升级失败会导致系统命令不能使用，严重的造成系统退出后无法重新进入，所以操作时候需要慎重。

查看系统当前glibc库版本：
`strings /lib64/libc.so.6 |grep GLIBC_`
或者：`ldd --version`

执行`ll /lib64/libc**`可以看出此时的libc.so.6是libc-2.12.so的别名。

下载网址：https://ftp.gnu.org/gnu/glibc/

```
tar -xzvf glibc-2.17.tar.gz
cd glibc-2.17
mkdir build
../configure --prefix=/opt/glibc-2.17
make && make install

rm -rf /lib64/libc.so.6 // 删除之前的软链接，可能导致系统命令不可用
LD_PRELOAD=/opt/glibc-2.17/lib/libc-2.17.so ln -s /opt/glibc-2.17/lib/libc-2.17.so /lib64/libc.so.6
```

删除libc.so.6之后可能导致系统命令不可用的情况，可使用如下方法解决：
```
LD_PRELOAD=/opt/glibc-2.17/lib/libc-2.17.so ln -s /opt/glibc-2.17/lib/libc-2.17.so /lib64/libc.so.6
```
`LD_PRELOAD`是一个环境变量，定义在程序运行前优先加载的动态链接库，本处作用就是在执行后面的ln命令时，指定使用的glibc库，这样命令就可以正常使用了。

或者使用sln代替ln。

升级完以后发现经常出现locale的warning，并且无法正常修改时区。
```
[ts@ts tools]$ locale
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_ALL to default locale: No such file or directory
LANG=en_US.utf-8
LC_CTYPE="en_US.utf-8"
LC_NUMERIC="en_US.utf-8"
LC_TIME="en_US.utf-8"
LC_COLLATE="en_US.utf-8"
LC_MONETARY="en_US.utf-8"
LC_MESSAGES="en_US.utf-8"
LC_PAPER="en_US.utf-8"
LC_NAME="en_US.utf-8"
LC_ADDRESS="en_US.utf-8"
LC_TELEPHONE="en_US.utf-8"
LC_MEASUREMENT="en_US.utf-8"
LC_IDENTIFICATION="en_US.utf-8"
LC_ALL=en_US.utf-8
```

执行：yum -y update glibc glibc-common 后变正常了。


## yum
Yellowdog Updater, Modified，它是一个C/S架构的软件，能够对基于RPM(Redhat Package Manager)格式的软件包进行管理。
提供自动解决依赖关系，软件包的分组，软件包的升级等功能。
构成一个完整的 yum 服务，需要以下部分：

- yum 服务器上的服务仓库（存储 rpm 文件和索引文件）
- 提供 rpm 和索引下载的网络服务（http 或者 ftp）
- 客户端的 yum 命令行工具
- 客户端仓库配置信息和插件扩展模块

交互式的，基于rpm的包管理工具。类似的工具还有apt-get/smart等。
* yum clean 清理缓存
  - expire-cache
  - packages
  - headers
  - metadata
  - rpmdb
  - plugins
  - all
* yum makecache 缓存创建
* yum list 查看所有仓库的可用软件包, 还可以跟上包名查看特定的软件包，包名可以使用通配符匹配
  - zlib*
  - all
  - {available|updates|installed|extras|obsoletes}
* yum grouplist


如果一个软件包有多个版本可用，yum 默认安装最新的那个，如果向指定版本，那么在包名后跟上想要安装的版本号

yum install PACKAGE-VERSION


配置文件
/etc/yum.conf 是否使用缓存，缓存文件路径等。
`/etc/yum.repo.d/*.repo`

http://ju.outofmemory.cn/entry/173260

国内源：
* http://mirrors.163.com/.help/centos.html
* http://mirros.sohu.com

yum install -y tree
yum install -y zsh
yum remove zsh
安装一组软件包：
yum grouplist
yum groupinstall "Development tools"
yum list z*
yum search zsh


配置开机启动
/etc/rc.d/rc.local

## 更新时间

显示时区：
date -R
data +%z

修改时区：tzselect

ntpdate time.apple.com
ntpdate cn.pool.ntp.org

查看硬件时间： hwclock
硬件时间同步到系统时间：hwclock --hctosys
系统时间同步到硬件时间：hwclock --systohc

/etc/sysconfig/clock

| Area          | HostName                   |
| Worldwide     | pool.ntp.org               |
| Asia          | asia.pool.ntp.org          |
| Europe        | europe.pool.ntp.org        |
| North America | north-america.pool.ntp.org |
| Oceania       | oceania.pool.ntp.org       |
| South America | south-america.pool.ntp.org |

## 网络

/etc/sysconfig/network-scripts/ifcfg-eth*

service network start/stop/restart/status

ifconfig eth0 up
ifconfig eth0 down

/etc/resolv.conf


lsattr
chattr 文件扩展属性
  +i -i
  +a -a



rpm
rpmfind.net
rpm.pbone.net
www.rpmseek.com

  管理rpm包
  mount /dev/cdrom /mnt
  lrzsz
  --help
  -i install
  -v verbose
  -h hash 打印#
  --nodeps
  -ql 安装后产生的文件
  -qf `which zsh` 是哪个文件安装的
  -pql /mnt/Packeages/lrzsz..rpm 预先查看一个包会生成哪些文件
  rpm -Uvh 升级
  rpm -e 卸载
  rpm -qpi 查看一个包的作用
  rpm --import RPM-GPG-KEY-redhat-release




  rpmbuild --rebuild `*.src.rpm`
  uname -m

tar --help


## CentOS 6


安装autoconf2.69
```bash
curl -L -O http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz
tar zxf autoconf-2.69.tar.gz
cd autoconf-2.69
yum install -y openssl-devel
./configure
make && make install
```



* http://mirrors.163.com/.help/centos.html
* http://support.ntp.org/bin/view/Servers/NTPPoolServers

# windows工具汇总

等待来自 xxx 服务的事务处理响应超时(30000 毫秒)。

127.0.0.1 crl.microsoft.com

service control manager

event id : 7011
https://technet.microsoft.com/en-us/library/cc756319(v=ws.10).aspx

sc query service_name
如
sc query zhudongfangyu


## netsh
netsh winsock reset

netsh 
> help


https://chocolatey.org/ package manager for windows


## windows 7 密码
* 直接拔电源
* 重启
* 需要出现“修复启动”

startup repair
view problem details, 找到最后的记事本链接。


### c:\\windows\\system32\\utilman.exe

启动windows进度条的时候按住电源。或者reset。重启。出现修复选项。Launch Startup Repaire(recommend)
进入记事本。修改cmd为utilman.exe


net user

whoami

net user



## net 命令
帮助：net help | more

* net user 用户管理。创建、修改、删除
    - net user administrator /active:yes 管理员权限
* net accounts 更新用户账户
* net config 
    - net config server
    - net config workstation
* net stop [service] 关闭服务

## windows 10

windows subsystem for linux

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

* Ubuntu
* OpenSUSE
* Debian GNU/LINUX


## UAC
UserAccountControl
此电脑->属性->安全和维护->更改用户控制设置。


## win+r

* 启动项：msconfig
* 组策略：regeidt
* 设备管理器：devmgmt.msc
* 任务管理器：taskmgr
* 控制面板：control
* 用户账户 control userpasswords2

### 老毛桃


## 一些好用的windows工具

* everything
* ARC: Advanced Rest Client
* firebug
* fiddler
* wireshark
* [privoxy](http://www.privoxy.org/)
* [Mingw-64: gcc compiler on windows](http://mingw-w64.org/doku.php/start)
* msys2
    * http://www.msys2.org/
    * pacman -Syuu
    * Install: pacman -S <package_names|package_groups>
    * Remove: pacman -R <package_names|package_groups>
    * Search: packman -Ss <name_pattern>
*  Win+Tab 3D切换程序

## [Cmder](http://cmder.net/)

windows下cmd的替代品。可以通过注册表的方式加入到文件夹的右键菜单中。
将下面的文件保存为reg文件，双击执行导入即可。
这样可以直接在文件夹中打开Cmder而不用每次都一路cd进去。

```
REGEDIT4

[HKEY_CLASSES_ROOT\Directory\Background\shell]

[HKEY_CLASSES_ROOT\Directory\Background\shell\cmd]
@="CMD"

[HKEY_CLASSES_ROOT\Directory\Background\shell\cmd\command]
@="cmd.exe /s /k pushd \"%V\""

[HKEY_CLASSES_ROOT\Directory\Background\shell\Cmder]
@="Cmder"

[HKEY_CLASSES_ROOT\Directory\Background\shell\Cmder\command]
@="D:\\soft\\cmder\\Cmder.exe /start \"%V\""
```


## 其他windows提供的工具

* [Process Explorer](https://docs.microsoft.com/en-us/sysinternals/downloads/process-explorer)
    - 可以查看进程打开了哪些文件

## 遇到的一些奇葩问题

windows 10 休眠以后无法连接网络，重启才行。

尝试方法：
* 更新网卡驱动，重启。发现网卡属性面板发生了变化。简洁了很多。
* 设备管理器->网卡->属性->高级
    - 电源管理：允许计算机关闭此设备以节省电源 去掉勾选，~~（感觉主要是这个问题，有待验证）~~ 更新：确实是这个问题，今天没有遇到这个问题了。
    - 速度与双工 自动协商 改为100M全双工
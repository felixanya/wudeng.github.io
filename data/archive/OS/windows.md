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

## windows 10

windows subsystem for linux

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

* Ubuntu
* OpenSUSE
* Debian GNU/LINUX


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

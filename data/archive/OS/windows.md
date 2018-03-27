# windows工具汇总

等待来自 xxx 服务的事务处理响应超时(30000 毫秒)。

127.0.0.1 crl.microsoft.com

service control manager

event id : 7011
https://technet.microsoft.com/en-us/library/cc756319(v=ws.10).aspx

sc query service_name
如
sc query zhudongfangyu





https://chocolatey.org/ package manager for windows

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

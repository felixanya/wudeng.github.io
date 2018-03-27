# Security Enhanced Linux



/etc/sysconfig/selinux

SELINUX
    enforcing
    permissive
    disabled
SELINUXTYPE
    targeted
    strict
    
getenforce 获取当前的SELINUX值
setenforce 设置
sestatus 显示状态

多数情况下东西运行不了只要一个restorecon就可以搞定

ls -Z / --context


user:rule:type:mls


* https://wiki.centos.org/HowTos/SELinux

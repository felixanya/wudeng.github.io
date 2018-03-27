


what

一个程序，用来管理ssh私钥。


有啥用？

* 不同的主机使用不同的私钥。ssh_config也可以做到。
* 私钥设置了密码，可以免去重复的输入密码的操作。将秘钥加入ssh-agent的时候会提示输入密码，以后通过ssh-agent登陆就不需要再次输入密码了。


How

怎样启动：

* ssh-agent bash
* eval `ssh-agent`

怎样配置？

首先启动ssh-agent，然后添加私钥：
ssh-add ~/.ssh/id_rsa


怎样查看秘钥：
ssh-add -l
ssh-add -L

删除：
ssh-add -d /path/to/id_rsa
ssh-add -D


加锁：
ssh-add -x

解锁：
ssh-add -X


关闭：
ssh-agent -k

* http://www.zsythink.net/archives/2407

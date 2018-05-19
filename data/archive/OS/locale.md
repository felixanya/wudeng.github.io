# locale总结


locale把按照所涉及到的文化传统的各个方面分成12个大类，这12个大类分别是：  

1、语言符号及其分类(LC_CTYPE)  
2、数字(LC_NUMERIC)  
3、比较和排序习惯(LC_COLLATE)  
4、时间显示格式(LC_TIME)  
5、货币单位(LC_MONETARY)  
6、信息主要是提示信息,错误信息,状态信息,标题,标签,按钮和菜单等(LC_MESSAGES)  
7、姓名书写方式(LC_NAME)  
8、地址书写方式(LC_ADDRESS)  
9、电话号码书写方式(LC_TELEPHONE)  
10、度量衡表达方式 (LC_MEASUREMENT)  
11、默认纸张尺寸大小(LC_PAPER)  
12、对locale自身包含信息的概述(LC_IDENTIFICATION)。 


其中，与中文输入关系最密切的就是LC_CTYPE，LC_CTYPE规定了系统内有效的字符以及这些字符的分类，诸如什么是大写字母，小写字母，大小写转换，标点符号、可打印字符和其他的字符属性等方面。
而locale定义zh_CN中最最重要的一项就是定义了汉字(Class“hanzi”)这一个大类，当然也是用Unicode描述的，这就让中文字符在Linux系统中成为合法的有效字符，而且不论它们是用什么字符集编码的。  


设定locale就是设定12大类的locale分类属性，即12个LC_*。除了这12个变量可以设定以外，为了简便起见，还有两个变量：LC_ALL和LANG。 
它们之间有一个优先级的关系：LC_ALL > LC_* > LANG 。 可以这么说，LC_ALL是最上级设定或者强制设定，而LANG是默认设定值。
* 如果你设定了LC_ALL＝zh_CN.UTF-8，那么不管LC_*和LANG设定成什么值，它们都会被强制服从LC_ALL的设定，成为 zh_CN.UTF-8。
* 假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_*=en_US.UTF-8，并且没有设定LC_ALL的话，那么系统的locale设定以LC_*=en_US.UTF-8。
* 假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_*，和LC_ALL均未设定的话，系统会将LC_*设定成默认值，也就是LANG的值zh_CN.UTF-8。  
* 假如你设定了LANG＝zh_CN.UTF-8，而其他的LC_CTYPE=en_US.UTF-8，其他的LC_*，和LC_ALL均未设定的话，那么系统的locale设定将是：LC_CTYPE=en_US.UTF-8，其余的 LC_COLLATE，LC_MESSAGES等等均会采用默认值，也就是 LANG的值，也就是LC_COLLATE＝LC_MESSAGES＝……＝ LC_PAPER＝LANG＝zh_CN.UTF-8。

* 如果你需要一个纯中文的系统的话，设定LC_ALL= zh_CN.XXXX，或者LANG=zh_CN.XXXX都可以，当然你可以两个都设定，但正如上面所讲，LC_ALL的值将覆盖所有其他的locale设定，不要作无用功。  
* 如果你只想要一个可以输入中文的环境，而保持菜单、标题，系统信息等等为英文界面，那么只需要设定 LC_CTYPE＝zh_CN.XXXX，LANG=en_US.XXXX就可以了。 这样LC_CTYPE＝zh_CN.XXXX，而LC_COLLATE＝LC_MESSAGES＝……＝ LC_PAPER＝LANG＝en_US.XXXX。
* 假如你什么也不做的话，也就是LC_ALL，LANG和LC_*均不指定特定值的话，系统将采用POSIX作为lcoale，也就是C locale


LANG - Specifies the default locale for all unset locale variables 
LANGUAGE - Most programs use this for the language of its interface 
LANGUAGE是设置应用程序的界面语言。而LANG是优先级很低的一个变量，它指定所有与locale有关的变量的默认值


查看本地locale设置:

```bash
ubuntu@ubuntu-xenial:~$ locale
LANG=en_US.UTF-8
LANGUAGE=
LC_CTYPE="en_US.UTF-8"
LC_NUMERIC="en_US.UTF-8"
LC_TIME="en_US.UTF-8"
LC_COLLATE="en_US.UTF-8"
LC_MONETARY="en_US.UTF-8"
LC_MESSAGES="en_US.UTF-8"
LC_PAPER="en_US.UTF-8"
LC_NAME="en_US.UTF-8"
LC_ADDRESS="en_US.UTF-8"
LC_TELEPHONE="en_US.UTF-8"
LC_MEASUREMENT="en_US.UTF-8"
LC_IDENTIFICATION="en_US.UTF-8"
LC_ALL=en_US.UTF-8
```

查看支持的locale：
```bash
ubuntu@ubuntu-xenial:~$ locale -a
C
C.UTF-8
en_US.utf8
POSIX
```

生成新的locale：
```
ubuntu@ubuntu-xenial:~$ sudo locale-gen zh_CN.UTF-8
Generating locales (this might take a while)...
  zh_CN.UTF-8... done
Generation complete.
ubuntu@ubuntu-xenial:~$ locale -a
C
C.UTF-8
en_US.utf8
POSIX
zh_CN.utf8
```

可以看出生成了新的locale：zh_CN.utf8


或者编辑/etc/local.gen，找到zh_CN.UTF-8，去掉前面的注释。然后直接运行sudo locale-gen就会生成这个文件中的locale。

重启启动bash，就能使用export LC_ALL=zh_CN.UTF-8来设置locale了。





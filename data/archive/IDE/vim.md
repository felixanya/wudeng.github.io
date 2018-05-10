# vim


windows下目前只有老版本的vimerl可以成功。支持rebar3的话需要修改check_erl.erl文件。把rebar3的输出目录加进去。

统计单词出现的个数：`:%s/pattern//gn`

nerdtree:
* `C` 将所选目录设为根目录
* `?` 帮助文档



```bash
git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh
```

更新
```bash
cd ~/.vim_runtime
git pull --rebase
```

## 插件

* 管理buf ,o :BufExplorer
* 最近打开的文件 ,f :MRU
* 快速找到文件或buf ,j ctrlp.vim
* nerdtree ,nn
* 突出显示  ,z

* 保存 ,w
* 关闭当前buf ,bd
* 关闭所有buf ,ba

* ,tn 
* ,to
* ,tc
* ,te

* ,cd
* ,g :Ack
* ,pp  粘贴模式
* 选中文本以后 gv 调用ack
* 选中文本后 ,r 替换文本



### ack.vim

echo  &runtimepath


apt-get install ack-grep



安装zh_CN.UTF-8：
sudo apt-get install language-pack-zh-hans



wudeng@s3-10-80-0-160:~/S3Server$ locale -a
locale: Cannot set LC_CTYPE to default locale: No such file or directory
locale: Cannot set LC_MESSAGES to default locale: No such file or directory
locale: Cannot set LC_COLLATE to default locale: No such file or directory
C
C.UTF-8
POSIX
zh_CN.utf8
zh_SG.utf8

没有安装en_US.utf8

安装en_US.utf8:

wudeng@s3-10-80-0-160:~/S3Server$ sudo locale-gen en_US.UTF-8
/bin/bash: warning: setlocale: LC_ALL: cannot change locale (en_US.UTF-8)
Generating locales (this might take a while)...
  en_US.UTF-8... done
Generation complete.

在检查一下，正常了：
wudeng@s3-10-80-0-160:~/S3Server$ locale -a                  
C
C.UTF-8
en_US.utf8
POSIX
zh_CN.utf8
zh_SG.utf8


sudo locale-gen --purge
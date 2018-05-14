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

* 定位文件
* git diff
* ack 搜索
* 目录树浏览

## 插件

* 管理buf ,o :BufExplorer
* 最近打开的文件 ,f :MRU
* 快速找到文件或buf ,j ctrlp.vim 或者ctrl+f
* nerdtree ,nn :NERDTreeTrigger, nf :NERDTreeFind
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

在vim里面使用ack的时候总是报locale的warning，只要在.bashrc中配置：
```
export LC_TYPE=zh_CN.UTF-8
export LC_ALL=zh_CN.UTF-8
```
即可。使用locale查看的时候，如果是定义了环境变量的是没有引号的。其他都是带引号的。


echo  &runtimepath


sudo apt-get install ack-grep


纯perl 5，所以支持windows。

* 自动忽略VCS
```
$ grep pattern $(find . -type f | grep -v '\.git')
$ ack pattern
```
* 指定文件类型
```
$ grep pattern $(find . -name '*.pl' -or -name '*.pm' -or -name '*.pod' | grep -v .git)
$ ack --perl pattern
```

配置文件.ackrc，关闭--noenv

选项：
* -i, --ignore-case
* -v, --invert-match
* -w, --word-regexp
* -Q, --literal
* -f, 只打印要扫描的文件而不执行扫描
    - ack -f --perl > all_perl_files

* https://beyondgrep.com/


同类的应用还有：
* ack
* ag    the silver searcher
* git-grep
* GNU grep
* ripgrep


## tab
gt
gT
,te

## fold
zi

:help zi

## ctags
ctags -R .

## 参考文档
* https://github.com/amix/vimrc.git
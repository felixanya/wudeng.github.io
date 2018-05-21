# Git

* `.git`文件夹
* `.gitignore` 忽略文件

* git help 获取帮助
* git init
* git clone
* git add
* git status
* git config

## 分支
* `git branch`
    - `--list` 列出本地分支，当前分支以`*`开头
    - `-r` 列出远程分支
    - `-a` 查看所有分支，包括本地和远程分支
    - `git branch my2.6.14 v.2.6.14` 以v.2.6.14 tag为基准，创建新分支my2.6.14
* `git checkout lesson-19` checkout分支


## 常用命令

* git fetch origin : harmless
* git pull = git pull + git merge

查看远程仓库地址
* git config --get remote.origin.url
* git ls-remote --get-url
* git remote get-url origin
* git remote show origin
* git remote -v

## [branching model (Vicent Driessen)](http://nvie.com/posts/a-successful-git-branching-model/)

两个主要分支：
* master 生产环境。只用来发布重大版本。
* develope 日常开发环境，汇总下个版本的提交。 

git checkout -b develop master
...
git checkout master
git merge --no-ff develop           --no-ff是为了保证版本演进的清晰

三个临时性分支：
* feature 功能分支
* hotfix 热更新分支，修复maser上的bug。需要merge到master以及develop分支。
* release 预发布，测试环境 


| branch  | branch from | merge          |
|---------|-------------|----------------|
| feature | develop     | develop        |
| release | develop     | develop,master |
| hotfix  | master      | develop,master |

* feature
    - git checkout -b myfeature develop 从develop分支创建一个myfeature的分支，并切换过去
    - commit...         在新分支上开发并commit
    - git checkout develop  回到develop分支
    - git merge --no-ff myfeature merge新分支上的变化
    - git branch -d myfeature   删除新分支
    - git push origin develop   将develop推送到仓库
* release 测试环境，基于develope创建，修复bug，最后合并到develope和master分支，并发布master到生产环境，然后删除release分支。
    - git checkout -b release-1.2 develop
    - ./bump-version.sh 1.2
    - git commit -a -m "Bumped version number to 1.2"
    - fix bug...
    - git checkout master
    - git merge --no-ff release-1.2
    - git tag -a 1.2
    - git checkout develop
    - git merge --no-ff release-1.2
    - git branch -d release-1.2
* hotfix
    - git checkout -b hotfix-1.2.1 master
    - ./bump-version.sh 1.2.1
    - git commit -a -m "Bumped version number to 1.2.1"
    - fix bug...
    - git commit -m "Fix severe production problem"
    - git checkout master
    - git merge --no-ff hotfix-1.2.1
    - git tag -a 1.2.1
    - git checkout develop
    - git merge --no-ff hotfix-1.2.1
    - git branch -d hotfix-1.2.1

## git-flow

### 初始化
* git flow init

### 开发
* git flow feature start rss-feed
* git commit -am 'rss feed'
* ...
* git flow feature finish rss-feed
* git push

### 测试
* git flow release start v1.1.0
* git commit -am "fix bug 100"
* ...

### 发布
* git flow release finish v1.1.0
* git push origin master

## .gitconfig

### socks代理
[http]
	proxy = 'socks5://127.0.0.1:1080'
[https]
	proxy = 'socks5://127.0.0.1:1080'

## stash

git stash save
git checkout branch
git stash pop

放弃本地修改：
git reset --hard HEAD


git reset HEAD file 放弃暂存区修改
git checkout -- file 放弃工作区修改

* 私有GitLab


## tag

* 命令git push origin <tagname>可以推送一个本地标签；
* 命令git push origin --tags可以推送全部未推送过的本地标签；
* 命令git tag -d <tagname>可以删除一个本地标签；
* 命令git push origin :refs/tags/<tagname>可以删除一个远程标签。

## log
```
git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

git commit --amend -m "New Commit message" 修改commit message。做这步之前索引区是干净的。否则会一起提交。
git push --force

https://stackoverflow.com/questions/179123/how-to-modify-existing-unpushed-commits?utm_medium=organic&utm_source=google_rich_qa&utm_campaign=google_rich_qa
https://help.github.com/articles/changing-a-commit-message/
https://sethrobertson.github.io/GitFixUm/fixup.html

git lg


* https://jonas.github.io/tig/ Tig is an ncurses-based text-mode interface for git.

sudo apt-get install libncurses-dev

## 常见问题
* git clone https的时候报错："fatal: HTTP request failed"
方案一：把https改成git，这种方法治标不治本。
方案二：sudo yum update -y nss curl libcurl

出错原因：GitHub Permanently disable deprecated algorithms in February 22, 2018 19:00 UTC
https://githubengineering.com/crypto-removal-notice/


## git push without password
使用ssh，而不是默认的https：
`git remote set-url origin git@github.com:wudeng/vimrc-1.git`

[参考](https://help.github.com/articles/changing-a-remote-s-url/#switching-remote-urls-from-https-to-ssh)

ssh-keygen -t rsa -C "wudeng256@gmail.com" 生成id_rsa, id_rsa.pub文件。
把公钥放到github，将id_rsa秘钥放到`c:\Users\ejoy\.ssh\`目录下，这样git push的时候不需要提供账号密码。

或者缓存https密码：
git config credential.helper store

```
git config --global credntial.helper 'cache --timeout 7200'
```
缓存2小时。

git config --global user.name 'wudeng'
git config --global user.email 'wudeng256@gmail.com'


## origin & upstream    

upstream: a link with the original repo
origin: your own fork

## submodule

git submodule init
git submodule update

忽略子模块的变化
.gitmodules
```
[submodule "bundle/fugitive"]
	path = bundle/fugitive
	url = git://github.com/tpope/vim-fugitive.git
    ignore = dirty
```

## merge && rebase

rebase 衍合，将一个分支上的修改在当前分支上重放。log上只有一条线。
merge 合并，讲一个分支上的修改合并到当前分支。log体现为两个链条合一。

当在一个分支上工作时，需要把主分支的修改同步到当前分支就使用rebase。
这个时候不适合用merge。在将特性分支merge到主分支之前使用rebase，能够保证merge不会出现冲突。


现在想把experiment上的修改合到master上面。
1. 检出experiment分支，对master进行衍合。
```
git checkout experiment
git rebase master
```
2. 回到master分支，进行一个快进合并。 
```
git checkout master
git merge experiment
```

将master push到远程分支之前，如果远程分支已经发生了变化，可以先rebase远程分支：git pull -r
最后再git push


## 虚拟机共享文件
```
忽略文件mode变化
git config --global core.filemode false
忽略^M
git config --global core.autocrlf true
```

git config --global core.editor "vim"



## 参考文档

* https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow
* https://github.com/petervanderdoes/gitflow-avh
* https://book.git-scm.com/
* https://jeffkreeftmeijer.com/git-flow/
* http://danielkummer.github.io/git-flow-cheatsheet/
* https://gitee.com
* http://git.oschina.net/progit/
* http://gitready.com/ 学习网站
* https://git-scm.com/book/zh/v2

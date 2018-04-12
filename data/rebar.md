rebar

rebar编译的时候是通过读取src目录下的app.src文件来进行的。

## 下载
```bash
wget --no-check-certificate https://raw.github.com/wiki/rebar/rebar/rebar
```

## 编译
```
rebar help compile
```

```
mkdir project
cd project
mkdir -p apps/echo_svr
cd apps/echo_svr
../../rebar create-app appid=echo_serv


echo {sub_dirs, ["apps/echo_serv", "rel"]} > rebar.config

./rebar compile


```



escript rebar get-deps
escript rebar comile eunit

{deps_dir, ["deps"]}.
{cover_enabled, true}

rebar的目录管理：当前目录下`ebin`，`deps/*/ebin`
启动程序：
可以用make来简化操作。
`start werl -pa ebin deps/*/ebin  -env ERL_MAX_ETS_TABLES 10000 -setcookie abcdeft +A 8 +K true +P 120000   -name galaxy-empire@127.0.0.1 -s slg_model start`


{deps, [

]}.

rebar用来编译很方便，如果不想用rebar生成release。
rebar编译后如果的各个application都有独立的ebin输出目录。
本地启动的时候可以通过pa指定ebin目录来启动节点。
服务器部署的时候可以提前将ebin打包。

* https://github.com/rebar/rebar
* http://blog.csdn.net/agiknight/article/details/7248624 测试有效。

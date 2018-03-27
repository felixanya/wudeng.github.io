# rebar3
```
├── LICENSE
├── README.md
├── apps
│   └── myapp
│       └── src
│           ├── myapp.app.src
│           ├── myapp_app.erl
│           └── myapp_sup.erl
├── config
│   ├── sys.config
│   └── vm.args
├── lib
│   └── mylib
│       ├── LICENSE
│       ├── README.md
│       ├── rebar.config
│       └── src
│           ├── mylib.app.src
│           └── mylib.erl
└── rebar.config
```

参考rebar.config.sample的写法。

debug：
windows下：set DEBUG=1, set DEBUG=
linux:DEBUG=1, DEBUG=
```
{erl_opts, [debug_info]}.
{deps, []}.

%% 定义默认发布环境(default环境)
{relx, [{release, { myapp, "0.1.0" },
        % 指定应用依赖 myapp会先于sasl被启动，辅助性的lib，无法被启动，默认为{mylib, permanent}
         [myapp, {mylib, load},
          sasl]},

        {sys_config, "./config/sys.config"},
        {vm_args, "./config/vm.args"},

    %% 当dev_mode==true时 _build/default/rel/myapp/lib/目录下的库其实是_build/default/lib目录下对应lib的软链接，这样重新编译后，无需重新发布，重启或热加载代码即可
        {dev_mode, true},
        %% 是否在发布目录中包含虚拟机 即为一个独立的运行环境
        {include_erts, false},

        {extended_start_script, true}]
}.

%% 定义其它发布环境
%% 参数使用覆盖(override)机制，即这里面没有定义的参数，将使用默认发布环境(default)配置
{profiles, [{prod, [{relx, [{dev_mode, false},
                            {include_erts, true}]}]
            }]
}
```

四种环境定义：
* default
* prod
* native
* test

relx
hex.pm

* update
* new
    - app: a stateful OTP application with a supervision tree, as a single application project
    - lib: a library OTP application (without supervision trees), useful for grouping together various modules, as a single application project
    - release: an umbrella project ready to be released
    - escript: a special form of single application project that can be built as a runnable script
    - plugin: structure for a rebar3 plugin
* compile
* release


## release
relx
```
rebar3 deps
rebar3 new release myrel
rebar3 release
rebar3 as prod tar

```
profiles定义在rebar.config中。rebar3 as <profile> <command>
rebar3 as prod
```
{profiles, [{prod, [{relx, [{dev_mode, false},
                            {include_erts, true}]}]
            }]
}.
```

`.\_build\default\rel\myrel\bin\myrel.cmd console`

在当前文件新建一个脚本文件`rebar3.bat`，跟`rebar3`文件一起丢到`c:\windows\`目录下面，

```rebar3.bat
@echo off
escript c:\windows\rebar3 %*
```

这样就可以直接用rebar3命令。

创建脚手架代码：`rebar3 new app the_name_of_app`，会在当前目录下生成the_name_of_app文件夹，里面有三个文件。
进入这个文件夹运行`rebar3 compile`


rebar3 pkgs
rebar3 update

rebar3 shell
  r3:do(compile)
  r3:do(ct)
  r3:do(dialyzer)

how：
实时编译更新代码。用reloader。


windows下rebar出错了，可以通过
set DEBUG=1
来设置环境变量，打开输出。

调试完毕，
set DEBUG=
就可以清除环境变量了，等号后面没有东西。=0也是没用的。应该不是检查环境变量的值，而是看环境变量是否存在。

通过
set DEBUG 就能查看环境变量。

## rebar

http://blog.csdn.net/agiknight/article/details/7248624 测试有效。

escript rebar get-deps
escript rebar comile eunit

{deps_dir, ["deps"]}.
{cover_enabled, true}

rebar的目录管理：当前目录下`ebin`，`deps/*/ebin`
启动程序：
可以用make来简化操作。
`start werl -pa ebin deps/*/ebin  -env ERL_MAX_ETS_TABLES 10000 -setcookie abcdeft +A 8 +K true +P 120000   -name galaxy-empire@127.0.0.1 -s slg_model start`

src目录下有app.src文件，编译后会拷贝到ebin目录下。code:priv_dir(App)也是好的。问题在于打包的时候，各个分散的ebin目录。如何部署?
rebar generate
rebar3 release 生成release。
rebar3 as prod tar 打包之前要编译好。

配置文件里面，sub_dirs：指定目录。

rebar generate 需要读取 reltool.config, 生成一个文件夹。里面有bin/erts-version/lib/log/release等文件夹。将这个文件夹打包，就能放到任意机器目录下去执行了。
reltool.config中的overlay是干啥的
lib_dirs,
app

windows下用rebar3 release创建的bin文件夹里面只有windows的启动程序，怎么生成linux的启动程序？
bin/mynode/ console

rebar采用reltool, 而rebar3采用relx，

windows环境下，
  * rebar生成的release是linux环境的。也就是说rebar产生的release无法再windows下运行。只能自己写执行脚本。

  * rebar3生成的是当前环境的release。那么如何在windows下生成release的发布反而成了难点。一个可能的方案是通过配置overlay来实现拷贝。

推荐的方式是将linux下编译的erlang拷贝到windows下面。然后指定include_erts为目标erlang路径。
http://erlang-questions.erlang.narkive.com/gDmRuUoW/releases-and-different-oss
```
wget https://www.openssl.org/source/openssl-1.0.1j.tar.gz
tar -xvzf openssl-1.0.0j.tar.gz
cd openssl-1.0.0j/
./config --prefix=/usr shared -fPIC
sudo make
sudo make install

then Erlang (R16B03-1) is configured with

./configure --disable-dynamic-ssl-lib --with-ssl=/usr/ --enable-smp-support --without-termcap
```

还有一个思路是用docker来编译。

http://www.rebar3.org/docs/releases#cross-compiling

cross compile
--include-erts=$PATH_TO_EXTRACTED_LINUX_ERTS    {include_erts, $PATH_TO_EXTRACTED_LINUX_ERTS}

Yes, it is absolutely possible to build releases cross-platform. You need to get the ERTS directory from the Erlang installation on the Pi (in other words, the root Erlang directory), and then configure your release to reference that ERTS when packaging (in the case of exrm, via rel/relx.config) with {include_erts, "path/to/pi_erts"}.. When you build your release, you can then deploy the tarball to the Pi and you’ll be set.


{include_erts, "PATH/TO/ERLANG_FROM_DEVICE"}.
{system_libs, "PATH/TO/ERLANG_FROM_DEVICE/lib"}.

经过测试发现，这种方法也行不通。rebar3会报错。可能是rebar的bug导致的。

使用tar包解压运行的时候，只能用
./bin/server_manager console
不能直接到bin文件夹运行。否则会报错：(cannot expand $ERTS_LIB_DIR in bootfile)
生成tar包只能用{include_erts,true}, 否则会报错：{"init terminating in do_boot",{'cannot load',error_handler,get_files}}



/usr/local/lib/erlang/lib/erlang/erts-5.10.4/bin/erlexec -boot /tmp/test/tmp/releases/0.1.0/start -mode embedded -boot_var ERTS_LIB_DIR /usr/local/lib/erlang/lib/erlang/lib -config /tmp/test/tmp/releases/0.1.0/sys.config -args_file /tmp/test/tmp/releases/0.1.0/vm.args -pa -- console


/tmp/test/tmp/erts-5.10.4/bin/erlexec -boot /tmp/test/tmp/releases/0.1.0/start -mode embedded -boot_var ERTS_LIB_DIR /tmp/test/tmp/lib -config /tmp/test/tmp/releases/0.1.0/sys.config -args_file /tmp/test/tmp/releases/0.1.0/vm.args -pa -- console


开服：
bin/server_manager start

shell连过去：
bin/server_manager attach
退出的时候不能用Ctrl+G-q，这种方式会把整个节点关掉。https://stackoverflow.com/questions/12926291/erlang-detach-shell-from-node-quit-shell-without-killing-node
而要用Ctrl+D（EOF）

## 传统的发布方式
```
## 打包后保留文件夹结构
ebin:
	tar czvf ebin.tar.gz -C ./_build/default/ \
		lib/emysql/ebin \
		lib/goldrush/ebin \
		lib/lager/ebin \
		lib/memcache/ebin \
		lib/mochiweb/ebin \
		lib/server_manager/ebin

## 全部打包到ebin文件夹
ebin2:
	tar czvf ebin.tar.gz -C ./_build/default/lib/server_manager/ ebin \
		-C ../goldrush/ ebin \
		-C ../lager/ ebin \
		-C ../memcache/ ebin \
		-C ../mochiweb/ ebin \
		-C ../emysql/ ebin
```

编写main.erl文件。导出start/0, stop/0函数。用于启动和关闭。
```
erl -pa lib/*/ebin -run main -boot start_sasl -detached
```

## 参考文档

* https://www.rebar3.org
* https://hex.pm/
* http://wudaijun.com/2016/09/erlang-rebar3/

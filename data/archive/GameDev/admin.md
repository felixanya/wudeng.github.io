# 管理后台

bin/conf.json 配置文件
    db ssdb的ip，端口，线程池大小
    log?
        ip port
    callserver ip:port?
    pprof?
    ipay?
    robotip?
    robotport?
    mode?
    port web服务器的端口

bin/csv
    策划配置文件，由excel生成
bin/AmazeUI
    前端文件
    am-icon-home
    am-active

run.sh
    bash run.sh windows下运行网站

build_linux.sh
    linux下编译

src/admin.go
    网站入口
    admincall
    basic/ssdb/gossdb ssdb客户端
    data
    flag 解析命令行参数
    net/http    监听
    operation
    role
    statistics
    user
    _ scv
    github.com/golang/glog 日志系统
        glog.Info("")
        glog.Fatalf("%s", err)
        glog.V(2)
    github.com/labstack/echo    http服务器框架
    github.com/labstack/echo/middleware 中间件

src/data
    conf 读取配置，conf.json
    data_admin
        用户管理model
        定义超级用户
        定义用户组
    data_admin_record
        管理员操作记录
    data_ipay 充值情况

## 需要了解的库

* encoding/json
    json.Unmarshal(data, &Conf)
* io/ioutil
    ReadAll(f)

## [贵州麻将管理后台](https://github.com/dolotech/admins.git)

### [SSDB](http://ssdb.io/)
使用docker搭建ssdb服务器
    docker run -it --rm -p 8888:8888 -v /var/lib/ssdb:/var/lib/ssdb wendal/ssdb

### GO
使用 go get -u github.com/name/package 获取依赖的包，需要提前设置gopath
    go get



### 前端界面

    .tpl-content-wrapper 正文内容
        .am-breadcrumb 面包屑
        .tpl-portlet-components
            .tpl-block
                .am-u-sm-12


* https://github.com/vue-bulma/vue-admin

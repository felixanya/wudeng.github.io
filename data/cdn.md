
回源


强缓存: 不发请求到服务器
    Expire : http 1.0，返回GMT的绝对时间
        expires: thu, 02 feb 2017 08:00:00 GMT
    Cache-Control : http 1.1，返回秒为单位的过期时间
        max-age=128000

协商缓存：发请求到服务器
    Last-Modified, If-Modified-Since
    ETag, If-None-Match
    304 NOT CHANGED

实现方案：
    服务器语言直接在返回头中加上相应头字段
    修改服务器配置，全局设置或者添加响应头字段
        mod_expires


资源重新命名，如index.js -> index_a083082f.js
    优点：可用性好。新的资源文件不会覆盖正在运行的资源文件。
    缺点：大量无效的旧版文件存在于CDN或版本管理服务器

资源链接添加变化的参数：index.js -> index.js?hash=a083082f
    资源更新日期或者文件内容的hash值。

gulp-hash-list 读取资源，计算hash值，按指定的格式生成一个清单文件。
gulp-asset-revision 读取资源列表的清单文件，替换html中的js，css等资源引用地址。
    同时支持新文件名以及参数的形式刷新资源版本号



gulp 原生的gulp-rev + gulp-rev-collector
    只支持新的文件名，不支持添加参数的形式

https://www.jianshu.com/p/fbc519d43924



新文件名：index.53214.js
    webpack
    HtmlWebpackPlugin 将新生成的js自动加入html中

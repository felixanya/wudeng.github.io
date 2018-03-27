# NPM 札记

## npm: Node Package Manager
Node.js的模块依赖管理工具。
npm最重要的命令是npm help，掌握了这个命令，就相当于掌握了linux下的man命令，什么问题都能通过查看文档来解决。
比如要查看配置相关的信息：通过npm help config即可打开网页查看相关的信息。

npm的配置来自命令行，环境变量以及.npmrc文件，以及package.json文件。
npm help config
npm config list [-l] [--json]
npm config edit
npm get <key>
npm set <key> <value> [-g|--global]

通过`npm get`可以查看默认配置。

如果加了proxy配置，下载的时候可能导致503，too many open connections的错误。
去掉代理即可。

## 升级npm
windows 7升级npm：
- PowerShell中运行：Set-ExecutionPolicy Unrestricted -Scope CurrentUser -Force
- npm install --global --production npm-windows-upgrade
- npm-windows-upgrade
- 用上下方向键选择要安装的版本，回车安装
也可以使用`npm-windows-upgrade --npm-version latest`安装最新版本。
等待完成即可。使用npm -v可以查新新的版本号。
```
PS C:\Users\Administrator> npm -v
5.6.0
```

## 操作模式
* 全局模式: -g --global

全局模式的包安装在{prefix}/lib/node_modules/目录下。
{prefix}是一个配置变量。可以通过`npm config get prefix`查看。

* 本地模式：
npm init 创建 package.json

npm install --save-dev 安装开发时需要的包。

## 淘宝镜像
npm默认的镜像地址在国外，所以最好切换成国内的淘宝镜像。
查看镜像：
```
npm config get registry
http://registry.npmjs.org/
```
通过cnpm使用：
```
npm install -g cnpm --registry=https://registry.npm.taobao.org
```
`cnpm config get registry` 可以看到，cnpm已经使用淘宝的镜像了。
安装成功以后就可以用cnpm命令取代npm了：`cnpm install [name]`。

当然也可以通过修改配置文件达到使用镜像的目的。可以直接命令行修改。
```
npm set registry https://registry.npm.taobao.org # 注册模块镜像
npm set disturl https://npm.taobao.org/dist # node-gyp 编译依赖的 node 源码镜像
npm set chromedriver_cdnurl http://cdn.npm.taobao.org/dist/chromedriver # chromedriver 二进制包镜像
npm set operadriver_cdnurl http://cdn.npm.taobao.org/dist/operadriver # operadriver 二进制包镜像
npm set phantomjs_cdnurl http://cdn.npm.taobao.org/dist/phantomjs # phantomjs 二进制包镜像
npm set sass_binary_site http://cdn.npm.taobao.org/dist/node-sass # node-sass 二进制包镜像
npm set electron_mirror http://cdn.npm.taobao.org/dist/electron/ # electron 二进制包镜像
npm set selenium_cdnurl=http://npm.taobao.org/mirrors/selenium
npm set node_inspector_cdnurl=https://npm.taobao.org/mirrors/node-inspector
npm cache clean # 清空缓存
```

或者直接修改`$HOME/.npmrc`文件：

```
; proxy=http://127.0.0.1:1080/
registry=https://registry.npm.taobao.org/
disturl=https://npm.taobao.org/dist
chromedriver_cdnurl=http://cdn.npm.taobao.org/dist/chromedriver
operadriver_cdnurl=http://cdn.npm.taobao.org/dist/operadriver
phantomjs_cdnurl=http://cdn.npm.taobao.org/dist/phantomjs
sass_binary_site=http://cdn.npm.taobao.org/dist/node-sass
electron_mirror=http://cdn.npm.taobao.org/dist/electron/
selenium_cdnurl=http://npm.taobao.org/mirrors/selenium
node_inspector_cdnurl=https://npm.taobao.org/mirrors/node-inspector
```

## YARN

yarn是解决npm一些痛点的的替代品。速度更快，更可靠，更安全。
* 极其快速：Yarn 会缓存它下载的每个包，所以无需重复下载。它还能并行化操作以最大化资源利用率，安装速度之快前所未有。
* 特别安全：Yarn会在每个安装包被执行前校验其完整性。
* 超级可靠：Yarn 使用格式详尽而又简洁的 lockfile文件 和确定性算法来安装依赖，能够保证在一个系统上的运行的安装过程也会以同样的方式运行在其他系统上。

## 参考文档

* https://www.npmjs.com/
* [npm-windows-upgrade](https://github.com/felixrieseberg/npm-windows-upgrade)
* https://npm.taobao.org/

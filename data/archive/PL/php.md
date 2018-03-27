php
====

重要版本
----
* 5.0 (2004) 面向对象模型
* 5.3 (2009) 匿名函数，命名空间
* 5.4 (2012) traits
php 5.x的下一个版本是7，版本6被跳过了。https://wiki.php.net/rfc/php6

集成包
----
* MAMP：支持windows和mac os x
* XAMPP: 支持windows，mac os x以及linux

命令行模式
----
查看php信息，`php -i`，创建hello.php脚本，
```
<?php
if($argc != 2) {
    echo "Usage: php hello.php [name].\n";
    exit(1);
}
$name = $argv[1];
echo "Hello, $name\n";
```
在命令行执行：
```
> php hello.php
Usage: php hello.php [name]
> php hello.php world
Hello, world
```

交互模式
----
php -a

* Interactive mode enabled 没有交互模式，相当于从stdin读取php文件，需要用tag包起来
* Interactive shell 交互模式，类似Erlang或者Python的交互输入，REPL

也可以通过php -m 查看是否有readline 如果没有，那就没有交互模式。

内置服务器
----

如果php版本大于5.4，可以使用内置的web服务器，根目录为当前目录。记得python 2.x版本也有一个搭建服务器的命令：
`python -m SimpleHTTPServer 8000`。
python3:
`python3 -m http.server 8000`

```
php -S localhost:8000
```

MySql Improved Extension
----
mysqli

int error_reporting([int $level])
设置报告错误级别error_reporting。在开发过程中很有必要显示它们。
默认值为 E_ALL & ~E_NOTICE，表示除了E_NOTICE其他都显示。
E_STRICT并不包含在E_ALL中。
在开发过程中设置error_reporting=E_ALL, display_errors=On
```
define('ENVIRONMENT', 'development');
if (ENVIRONMENT == 'development' || ENVIRONMENT == 'dev') {
    error_reporting(E_ALL);
    ini_set("display_errors", 1);
}
```

合并数组
* array_merge()
* +

自动加载类
```
<?php
    if(!function_exists('classAutoLoader')){
        function classAutoLoader($class){
            $class=strtolower($class);
            $classFile=$_SERVER['DOCUMENT_ROOT'].'/include/class/'.$class.'.class.php';
            if(is_file($classFile)&&!class_exists($class)) include $classFile;
        }
    }
    spl_autoload_register('classAutoLoader');
?>
```
或者
```
function __autoload($classname) {
    $filename = "./". $classname .".php";
    include_once($filename);
}
```
定义常量：`define(string $name, mixed $value [,bool $case_insensitive = false])`
检查常量：`defined(string $name)`
检查变量：`isset`
检查函数：`function_exists()`


分割字符串
`array explode(string $delimiter, string $string [, int $limit])`

composer
----
php包依赖管理工具，配置文件：composer.json
[国内镜像](https://pkg.phpcomposer.com)
设置方法，-g表示全局，去掉则表示当前项目：
```
composer config -g repo.packagist composer https://packagist.phpcomposer.com
```
常用命令，加上-v|vv|vvv 开启输出：
* composer selfupdate 更新composer自己
* composer install 安装依赖
* composer update  更新依赖

extract 从数组中将变量导入到当前符号表

面向对象SOLID原则
----
* Single responsiblity principle 单一功能原则
* Open/closed principle 开闭原则，对扩展开放，对修改封闭
* Liskov substitution principle，里氏替换原则
* Interface segregation principle，接口隔离
* Dependency inversion principle，依赖反转原则。依赖于抽象而不是一个实例。依赖注入是该原则的一种实现方法。


web框架
----
简单框架，源码非常简单，非常适合上手。
https://github.com/panique/mini
https://github.com/panique/mini2
https://github.com/panique/mini3

参考文档
----
[](https://laravel-china.github.io)
http://php.net/manual/en/book.mysqli.php
http://php.net/manual/zh/function.error-reporting.php
https://getcomposer.org
http://www.php-fig.org/
[PHP The Right Way](http://laravel-china.github.io/php-the-right-way/)


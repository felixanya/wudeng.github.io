# Erlang开发规范

## 命名
* 变量：驼峰BasicInfo
* 函数：basic_info()

## 导出函数
* 导出函数要export，并且放在文件的开头，内部函数放在文件尾部。
* 不要在源码文件中export_all，为方便调试可以写在编译选项中，如EMakefile中。

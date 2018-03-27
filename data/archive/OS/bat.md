
`.cmd`

注释
    REM
    ::

@ECHO OFF

定义变量：
    SET FOO="bar" =前后没有空格
    SET /A four=2+2

使用变量：
    echo %FOO%
    命令行参数 %1, %2 等
    IF NOT DEFINED FOO SET FOO="bar"

循环：
    FOR /L %A IN (1, 1, 10) DO @echo hello


@echo off

SET ROOT_DIR="/home/dtll/resources/tianshu"
SET TAR_FILE="resouce.tar.gz"
SET KEY="caizhichao.ppk"
SET HOST="caizhichao@192.168.4.168"

tar zcf %TAR_FILE% ../server/
ssh -i %KEY% %HOST% "rm -rf %ROOT_DIR%/*"
scp -i %KEY% %TAR_FILE% "caizhichao@120.92.43.188:/tmp/"
ssh -i %KEY% %HOST% "tar zxf %TAR_FILE% -C %ROOT_DIR%"

pause


* http://steve-jansen.github.io/guides/windows-batch-scripting/index.html

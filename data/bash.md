# bash

## 修改文件编码: gb2312 -> utf8
```bash
for f in $(ack -f); do 
    encoding=$(file -b --mime-encoding $f)
    if [ "$encoding" == "iso-8859-1" ]; then 
        echo $f; 
        iconv -f gb2312 -t utf-8 --add-signature $f | sponge $f
    fi
done
```


## 输出颜色信息
```bash
bldgrn='\e[1;32m' # Green
bldylw='\e[1;33m' # Yellow
txtrst='\e[0m'    # Text Reset
function warning (){
    echo -n -e "${bldylw}WARNING:"
    echo -n -e "${txtrst}"
    echo $1
}
function info()
{
    echo -n -e "${bldgrn}INFO:"
    echo -n -e "${txtrst}"
    echo $1
}
```

## Here Document
```bash
## 如果有tab，tab也会进入文件中
cat << EOF > /tmp/youfile
These contents will be written to the file.
        This line is indented.
EOF
```

```bash
## 可以在<<后面加-来禁用前面的tab
if true ; then
    cat <<- EOF > /tmp/yourfilehere
    The leading tab is ignored.
    EOF
fi
```

```bash
## 如果不想要解释变量，用单引号括起来
cat << 'EOF' > /tmp/yourfilehere
The variable $FOO will not be interpreted.
EOF
```

```bash
## 通过管道过滤
cat <<'EOF' |  sed 's/a/b/' | sudo tee somefilerequireroot 
foo
bar
baz
EOF
```

## Here Strings

```bash
tr a-z A-Z <<< 'one two three'
```
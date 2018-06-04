

python -m SimpleHttpServer 7777 #python2
python -m http.server 7777 #python3

## python2 vs python3

2.7


| python2        | python3 |
|----------------|---------|
| urllib,urllib2 | urllib  |
| unicode        | str     |
| str            | bytes   |

## [logging](https://docs.python.org/2/howto/logging.html)

- basicConfig(level, format)
  - level:
    - logging.WARNING 默认值
    - logging.DEBUG
    - logging.INFO
  - format:
    - "%(levelname)s"
    - "%(message)s"
    - "%(asctime)s"
  - filename = 'example.log'
  - filemode
    - 'w'
- info(message)
- debug(message)
- warning(message)

## [xlrd](https://github.com/python-excel/xlrd)

xlrd.open_workbook(filename)

xlrd.book
  - class xlrd.book.Book
    - nsheets
    - sheets()
    - sheet_names()
    - sheet_by_index(0)
    - sheet_by_name(name)

xlrd.sheet
  - class xlrd.sheet.Sheet
    - name
    - nrows
    - ncols
    - cell_value(rowx, colx)
    - row(rx)
    - row_values(rx)

## [itertools](https://docs.python.org/2/library/itertools.html)

dropwhile(pred, seq), dropwhile(lambda x: x<5, [1,4,6,4,1]) --> 6 4 1

## pip

安装pip：
```
wget https://bootstrap.pypa.io/get-pip.py --no-check-certificate
python get-pip.py
```

ubuntu:
```
sudo apt install python-pip
```
python -m pip install --force-reinstall pip

使用pip安装包：
pip install flask
pip install -r requirements.txt


pip默认使用http全局代理，也就是ie浏览器里面配置的代理，注意不要被坑了。

ie不支持socks代理，shadowsocks同时提供了socks5代理和http代理，所以ie也能用shadowsocks的代理。


https://docs.python.org/dev/howto/pyporting.html

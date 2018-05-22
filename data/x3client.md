# x3client

project/raw_data 配置文件生成
    - "XlsxToLua.exe" "../raw_data" "../script/core/config" "-noClient" "-noLang" "-allowedNullNumber" "-allowedNullString" "-ignoreConfirm"
    - https://github.com/zhangqi-ulua/XlsxToLua

配置文件 script/core/config
    - buff
    - buildings
    - buildings_view
    - config 全局的配置变量
    - hand_effect
    - mahjong2man
    - meld_effect
    - skill
    - warrior_view
    - warrior

ej2dx.exe 
    -d 
    -w
    -h
    -t 标签 island | island_expolorer
    -u {login_ip,login_port,game_port,user}
    -r 默认是script，可以指定explorer

world.lua
    - fight_start(session, data)
    - ai
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

world:

观察着模式：

observers: 用msg来索引函数列表，当收到msg的时候，触发函数。
使用world:observe(msg, cb)来注册回调函数，订阅msg事件。


发生事件时，触发回调函数：world:message(message)  
调用：world:trigger(message, ...)


world主要订阅go_fight事件。当发生go_fight时间时，需要执行回调函数。
go_fight: camp_id所在的玩家已经准备好出兵。


island:yield_warriors(camp, frame, melds, hands) 在frame帧执行函数
    frame_triggers[frame] = {
        {callback, ...}
    }

    到特定帧执行相应的函数

    default_config(world, false)

player_mt:go_fight(melds, hands, no_warrior)



network.lua
    connect(cfg, cb)



单机流程：

portal.lua
    - on_touch(touched, what, x, xy)
        - touched.name = "offline"
        - handle = mt:offline()
    - mt:offline()
        - is_offline = true
        - mahjong.world:offline_battle("localhost")
            - guide_battle = false
            - network.connect({login_ip = "localhost"}, cb(session)) 通过network拿到session并调用 self:enter_battle(session)
                - 创建岛 core.island
                - offline_session = 1，调用cb，也就是world:enter_battle(1, roomid)
                    - roomid = nil
                    - init_global() 初始化global
                        - glue
                        - scene_mgr
                        - battle:new()
                            - battle:init()
                                - message.battle_register()
                                    - "building_new"
                                    - "fight_start"
                                        - battle:on_new_map(map)
                                    - "baby_warrior_new"
                                    - "warrior_born"
                - dispatch(frame+1, 0, "fight_start")
                    - do_dispatch
                        - handler = fight_start
                            - self.ai = default_ai(self)


        - self:enter_game()



抽牌逻辑：
    - player.draw_count 每次发牌数量 = 1
    - option_count = 3 可选的牌的数量
    - keep_pick
    - drawed[]
    - cards[]

    - island.draw_count
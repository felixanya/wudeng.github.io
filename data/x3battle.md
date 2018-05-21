# x3battle

island_explorer ctrl+r 刷新

explorer  
    - ope_panel 面板
        - warrior_panel 战斗列表
        - particle_panel 特效列表
        - warrior_property

    - warror_mgr 管理战斗单位
        - target
            - o_scobj_actor
                - v_effect_spr
                - v_ceil_effect_spr
                    - SLOT_STATUS
                - actor_set_effect('misc', name, SLOT_STATUS, boolean) 加特效
        - new_warrior(name, lv)
        - new_particle(name)


world.lua
    - my_camp
    - my_session
    - island
        - FRAME_TIME
        - get_player_by_camp
        - get_enemy_player
    - myself
        - is_me
    - enemy
    - ai
    - fight_start(session, data)
        - data
            - start_time
            - island
                - players
                    - camp_id
                    - session

创建战斗单位：
```lua
global.scene_mgr:create_troop(2200, 2200, name, lv, 1)
unit.delay_appear()
```


通过事件触发一个添加战斗单位的行为：

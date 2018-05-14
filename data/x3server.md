# x3server

* roomkeeper 一个unique snax服务，随服务启动
    - rooms{}
    - response.apply()
    - accept.close()

* room 一个snax服务
    - 非阻塞
        - accept.timeout(session)   超时
    - 阻塞
        - response.join(agent, secret)

* udpserver 一个snax服务

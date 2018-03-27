# Puppet

master
agent

资源
目标状态


manifest -> catelog

type{'title':
    attribute1 => value1,
    attribute2 => value2,
    ...
    attributeN => valueN
}

资源
    type: 
        puppet describe -l 查看所有资源
        puppet describe type 查看资源属性
        package
        service
        cron
        user
        file
        exec
    属性
        name
    Providers
        
特殊属性
    namevar
    ensure
        present
        absent
        stopped/running (service)
    metaparameter
        require / before 次序链 ->
        notify / subscribe 通知链 ~>


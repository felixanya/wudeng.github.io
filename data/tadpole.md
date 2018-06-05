# tadpole


## log

对logger的封装。

* config {name = name, level=Log.DEBUG}
* Debug(...)
* Debug(format, ...)
* Info(...)
* Infof(fmt, ...)
* Notice(...)
* Noticef(format, ...)
* Warning(...)
* Warningf(format, ...)
* Error(...)
* Errorf(fmt, ...)
* Critical(...)
* Criticalf(format, ...)
* Alert(...)
* Alertf(format, ...)
* Emerg(...)
* Emergf(format, ...)
* Assert(v, message)
* SError(message, level)
* Tag(key, value)
* Untag(key)
* set_modname(name)
* config(t)
* gamelog(logtype, t, timestamp)
* syslog(level, msg, logid)
* hook_assert()
* hook_error()



# 03-Redis的安装.md

大家可以看视频或者参考这个:

[菜鸟教程 - Redis 安装](http://www.runoob.com/redis/redis-install.html "http://www.runoob.com/redis/redis-install.html")

*心疼用 Windows 装虚拟机安装的童鞋 ...😂*

## Mac 使用 homebrew 安装

1. `brew install redis`

```
CorningSunMac:~ corning$ brew install redis
Updating Homebrew...
==> Auto-updated Homebrew!
Updated 1 tap (homebrew/core).
==> Updated Formulae
vault

==> Downloading https://homebrew.bintray.com/bottles/redis-4.0.7.high_sierra.bottle.tar.gz
######################################################################## 100.0%
==> Pouring redis-4.0.7.high_sierra.bottle.tar.gz
==> Caveats
To have launchd start redis now and restart at login:
  brew services start redis
Or, if you don't want/need a background service you can just run:
  redis-server /usr/local/etc/redis.conf
==> Summary
🍺  /usr/local/Cellar/redis/4.0.7: 13 files, 2.8MB
```

2. `redis-server /usr/local/etc/redis.conf`

```
CorningSunMac:~ corning$ redis-server /usr/local/etc/redis.conf
11977:C 29 Jan 10:29:12.960 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
11977:C 29 Jan 10:29:12.960 # Redis version=4.0.7, bits=64, commit=00000000, modified=0, pid=11977, just started
11977:C 29 Jan 10:29:12.960 # Configuration loaded
11977:M 29 Jan 10:29:12.961 * Increased maximum number of open files to 10032 (it was originally set to 7168).
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 4.0.7 (00000000/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 11977
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

11977:M 29 Jan 10:29:12.967 # Server initialized
11977:M 29 Jan 10:29:12.967 * Ready to accept connections
```

## Redis命令

* 启动

`redis-server /usr/local/etc/redis.conf`

* 查看

`ps -ef | grep -i redis`

```
CorningSunMac:redis corning$ ps -ef | grep -i redis
  501 11977 10272   0 10:29上午 ttys000    0:14.09 redis-server 127.0.0.1:6379
  501 15286 12141   0  3:10下午 ttys002    0:00.00 grep -i redis
```

* 停止

`kill -9 11977 #(不建议)` 或 `redis-cli shutdown`

* 连接

`redis-cli`

```
CorningSunMac:~ corning$ redis-cli
127.0.0.1:6379>
```

* 使用 get set del

```
127.0.0.1:6379> set name imooc
OK
127.0.0.1:6379> get name
"imooc"
127.0.0.1:6379> del name
(integer) 1
127.0.0.1:6379> get name
(nil)
```
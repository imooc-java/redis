# 12-Redis 的持久化的概述.md

## 两种持久化方式

* RDB 方式
* AOF 方式

## 持久化使用的方式

* RDB 持久化

  > 在指定的时间间隔内生成数据集的时间点快照

* AOF 持久化

  > 记录服务器执行的所有写操作命令，并在服务器启动时，通过重新执行这些命令来还原数据集

* 无持久化
* 同时使用 RDB 和 AOF

## Redis 持久化及 RDB 和 AOF 的优缺点

http://redisdoc.com/topic/persistence.html#redis


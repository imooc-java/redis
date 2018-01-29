# 02-Redis的概述.md

## Redis的由来

REmote DIctionary Server(Redis) 是一个由Salvatore Sanfilippo写的key-value存储系统。

Redis是一个开源的使用ANSI C语言编写、遵守BSD协议、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。

## 支持的键值数据类型

* 字符串类型
* 列表类型
* 有序集合类型
* 散列类型
* 集合类型

## Redis的应用场景：

* 缓存
* 任务队列（秒杀，高并发）
* 应用排行榜
* 网站访问统计
* 数据过期处理
* 分布式集群架构中的 Session 分离


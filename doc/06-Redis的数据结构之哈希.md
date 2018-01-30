# 06-Redis的数据结构之哈希.md

## 存储Hash

* String Key 和 String Value 的 map 容器

## 存储Hash常用命令

* 赋值
* 取值
* 删除
* 增加数字
* 自学命令

## 示例

```python
# hset 设置值
127.0.0.1:6379> hset myhash username jack
(integer) 1
127.0.0.1:6379> hset myhash age 18
(integer) 1
# hmset 设置多个值
127.0.0.1:6379> hmset myhash2 username rose age 21
OK
# hget 获取值
127.0.0.1:6379> hget myhash username
"jack"
# hmget 获取多个值
127.0.0.1:6379> hmget myhash username age
1) "jack"
2) "18"
# hgetall 获取所有key和value
127.0.0.1:6379> hgetall myhash
1) "username"
2) "jack"
3) "age"
4) "18"
# 删除key
127.0.0.1:6379> hdel myhash2 username age
(integer) 2
127.0.0.1:6379> hgetall myhash2
(empty list or set)
# 删除不存在的 key 返回 0
127.0.0.1:6379> hdel myhash2 username
(integer) 0
127.0.0.1:6379> hmset myhash2 username rose age 21
OK
# del 可以直接删除所有
127.0.0.1:6379> del myhash2
(integer) 1
127.0.0.1:6379> hget myhash2 username
(nil)
# hincyby 数值自增
127.0.0.1:6379> hincrby myhash age 5
(integer) 23
127.0.0.1:6379> hget myhash age
"23"
# hexists 判断 key 是否存在
127.0.0.1:6379> hexists myhash username
(integer) 1
127.0.0.1:6379> hexists myhash password
(integer) 0
127.0.0.1:6379> hgetall myhash
1) "username"
2) "jack"
3) "age"
4) "23"
# hlen 获取长度
127.0.0.1:6379> hlen myhash
(integer) 2
# hkeys 获取所有 key
127.0.0.1:6379> hkeys myhash
1) "username"
2) "age"
# hvals 获取所有 value
127.0.0.1:6379> hvals myhash
1) "jack"
2) "23"
```

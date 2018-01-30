# 11-Redis的特性.md

## 相关特性

* 多数据库
* Redis事务

## 多数据库

> Redis默认是支持16个数据库，序号分别从0到15

```python
# select 切换数据库
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
(empty list or set)
127.0.0.1:6379[1]> select 0
OK
127.0.0.1:6379> keys *
 1) "mysort"
 2) "mya3"
 3) "num2"
 4) "mylist"
 5) "foo"
 6) "mylist4"
 7) "num4"
 8) "num3"
 9) "num"
10) "myhash"
11) "mylist2"
12) "mya1"
13) "mya2"
14) "mylist3"
15) "myb3"
16) "myb2"
17) "mylist6"
18) "myset"
19) "newcompany"
20) "mylist5"
21) "myb1"
# move 把key从当前数据库移到其他数据库
127.0.0.1:6379> move myset 1
(integer) 1
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> keys *
1) "myset"
```

## Redis事务

* redis-cli 1

```python
127.0.0.1:6379> set num 1
OK
127.0.0.1:6379> get num
"2"
# multi 开始事务
127.0.0.1:6379> multi
OK
# incr 给 num 自增
127.0.0.1:6379> incr num
QUEUED
127.0.0.1:6379> incr num
QUEUED
# exec 提交事务
127.0.0.1:6379> exec
1) (integer) 3
2) (integer) 4

127.0.0.1:6379> set user tom
OK
127.0.0.1:6379> get user
"tom"
127.0.0.1:6379> multi
OK
127.0.0.1:6379> set user jerry
QUEUED
# discard 回滚事务
127.0.0.1:6379> discard
OK
# 被回滚后，设置的 user 值没有改变
127.0.0.1:6379> get user
"tom"
```

* redis-cli 2

```python
127.0.0.1:6379> get num
"1"
127.0.0.1:6379> incr num
(integer) 2
127.0.0.1:6379> get num
"2"
127.0.0.1:6379> get num
"2"
# 另一个客户端的事务提交后，才能获取自增后的值
127.0.0.1:6379> get num
"4"
```
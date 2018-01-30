# 10-Redis的Keys的通用操作.md

```python
# keys 查看所有key
127.0.0.1:6379> keys *
 1) "mysort"
 2) "mya3"
 3) "num2"
 4) "my1"
 5) "mylist"
 6) "foo"
 7) "mylist4"
 8) "my3"
 9) "company"
10) "num4"
11) "num3"
12) "num"
13) "myhash"
14) "mylist2"
15) "mya1"
16) "mya2"
17) "mylist3"
18) "my2"
19) "myb3"
20) "myb2"
21) "mylist6"
22) "myset"
23) "mylist5"
24) "myb1"
# keys 支持模糊匹配
127.0.0.1:6379> keys my?
1) "my1"
2) "my3"
3) "my2"
# del 删除key
127.0.0.1:6379> del my1 my2 my3
(integer) 3
127.0.0.1:6379> keys my?
(empty list or set)
# exists 判断key是否存在，存在返回1，否则返回0
127.0.0.1:6379> exists my1
(integer) 0
127.0.0.1:6379> exists mya1
(integer) 1
127.0.0.1:6379> get company
"baidu"
# rename 给key重命名
127.0.0.1:6379> rename company newcompany
OK
127.0.0.1:6379> get company
(nil)
127.0.0.1:6379> get newcompany
"baidu"
# expire 给key设置超时时间（秒）
127.0.0.1:6379> expire newcompany 1000
(integer) 1
# ttl 查看key剩余的超时时间
127.0.0.1:6379> ttl newcompany
(integer) 993
# ttl 对于没有设置生存时间的，返回-1
127.0.0.1:6379> ttl mya1
(integer) -1
# type 可以查看key的类型
127.0.0.1:6379> type newcompany
string
127.0.0.1:6379> type mylist
list
127.0.0.1:6379> type myset
set
127.0.0.1:6379> type myhash
hash
127.0.0.1:6379> type mysort
zset
```

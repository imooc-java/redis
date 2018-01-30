# 08-Redis的数据结构之set.md

## 存储Set

* 和List类型不同的是，*Set集合中不允许出现重复的元素*
* Set可包含的最大元素数量是 429467295 (<a href="https://www.codecogs.com/eqnedit.php?latex=2^{32}-1" target="_blank"><img src="https://latex.codecogs.com/gif.latex?2^{32}-1" title="2^{32}-1" /></a>)

## 存储Set常用命令

* 添加/删除元素
* 获取集合中的元素
* 集合中的差集运算
* 集合中的交集运算
* 集合中的并集运算
* 扩展命令

## 示例

```
# sadd 新建key并添加值
127.0.0.1:6379> sadd myset a b c
(integer) 3
# sadd 对于已存在的值，无法添加
127.0.0.1:6379> sadd myset a
(integer) 0
127.0.0.1:6379> sadd myset 1 2 3
(integer) 3
# srem 删除值
127.0.0.1:6379> srem myset 1 2
(integer) 2
# smembers 查看值
127.0.0.1:6379> smembers myset
1) "c"
2) "a"
3) "b"
4) "3"
# sismember 判断值是否存在，存在返回1，否则返回0
127.0.0.1:6379> sismember myset a
(integer) 1
127.0.0.1:6379> sismember myset d
(integer) 0
127.0.0.1:6379> sadd mya1 a b c
(integer) 3
127.0.0.1:6379> sadd myb1 a c 1 2
(integer) 4
# sdiff 差集，和参数顺序有关联关系
127.0.0.1:6379> sdiff mya1 myb1
1) "b"
127.0.0.1:6379> sdiff myb1 mya1
1) "1"
2) "2"
127.0.0.1:6379> sadd mya2 a b c
(integer) 3
127.0.0.1:6379> sadd myb2 a c 1 2
(integer) 4
# sinter 交集
127.0.0.1:6379> sinter mya2 myb2
1) "a"
2) "c"
127.0.0.1:6379> sadd mya3 a b c
(integer) 3
127.0.0.1:6379> sadd myb3 a c 1 2
(integer) 4
# sunion 并集
127.0.0.1:6379> sunion mya3 myb3
1) "2"
2) "c"
3) "a"
4) "1"
5) "b"
127.0.0.1:6379> smembers myset
1) "c"
2) "a"
3) "b"
4) "3"
# scard 计算集合元素数量
127.0.0.1:6379> scard myset
(integer) 4
# srandmember 获取集合随机元素
127.0.0.1:6379> srandmember myset
"c"
127.0.0.1:6379> srandmember myset
"c"
127.0.0.1:6379> srandmember myset
"a"
# sdiffstore 计算差集并存储到 my1
127.0.0.1:6379> sdiffstore my1 mya1 myb1
(integer) 1
127.0.0.1:6379> smembers my1
1) "b"
# sinterstore 计算交集并存储到 my2
127.0.0.1:6379> sinterstore my2 mya1 myb1
(integer) 2
127.0.0.1:6379> smembers my2
1) "a"
2) "c"
# sunionstore 计算并集并存储到 my3
127.0.0.1:6379> sunionstore my3 mya3 myb3
(integer) 5
127.0.0.1:6379> smembers my3
1) "2"
2) "c"
3) "a"
4) "1"
5) "b"
```

## 存储Set使用场景

* 跟踪一些*唯一性数据*
* 用于维护数据对象之间的关联关系


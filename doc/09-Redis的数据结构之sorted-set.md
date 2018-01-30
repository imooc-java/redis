# 09-Redis的数据结构之sorted-set.md

## 存储Sorted-Set

* Sorted-Set和Set的区别
* Sorted-Set中的成员在集合中的位置是有序的

## 存储Sorted-Set常用命令

* 添加元素
* 获取元素
* 删除元素
* 范围查询
* 扩展命令

## 示例

```python
# zadd 创建集合，并设置值，返回新增元素数量
127.0.0.1:6379> zadd mysort 70 zs 80 ls 90 ww
(integer) 3
# zadd 已存在的key，会更新分数，集合元素数量不变
127.0.0.1:6379> zadd mysort 100 zs
(integer) 0
127.0.0.1:6379> zadd mysort 60 tom
(integer) 1
# zscore 获取key的分数
127.0.0.1:6379> zscore mysort zs
"100"
# zcard 返回集合元素数量
127.0.0.1:6379> zcard mysort
(integer) 4
# zrem 根据元素key删除
127.0.0.1:6379> zrem mysort tom ww
(integer) 2
127.0.0.1:6379> zcard mysort
(integer) 2
127.0.0.1:6379> zadd mysort 85 jack
(integer) 1
127.0.0.1:6379> zadd mysort 95 rose
(integer) 1
# zrange 0 -1 返回所有元素
127.0.0.1:6379> zrange mysort 0 -1
1) "ls"
2) "jack"
3) "rose"
4) "zs"
# zrange withscores 返回元素包含分数
127.0.0.1:6379> zrange mysort 0 -1 withscores
1) "ls"
2) "80"
3) "jack"
4) "85"
5) "rose"
6) "95"
7) "zs"
8) "100"
# zrevrange 逆序返回
127.0.0.1:6379> zrevrange mysort 0 -1 withscores
1) "zs"
2) "100"
3) "rose"
4) "95"
5) "jack"
6) "85"
7) "ls"
8) "80"
# zremrangebyrank 根据排序批量删除
127.0.0.1:6379> zremrangebyrank mysort 0 4
(integer) 4
127.0.0.1:6379> zrange mysort 0 -1 withscores
(empty list or set)
127.0.0.1:6379> zadd mysort 80 zs 90 ls 100 ww
(integer) 3
127.0.0.1:6379> zrange mysort 0 -1 withscores
1) "zs"
2) "80"
3) "ls"
4) "90"
5) "ww"
6) "100"
# zremrangebyscore 根据分数批量删除
127.0.0.1:6379> zremrangebyscore mysort 90 100
(integer) 2
127.0.0.1:6379> zrange mysort 0 -1 withscores
1) "zs"
2) "80"
127.0.0.1:6379> zrem mysort zs
(integer) 1
127.0.0.1:6379> zrange mysort 0 -1 withscores
(empty list or set)
127.0.0.1:6379> zadd mysort 70 zs 80 ls 90 ww
(integer) 3
127.0.0.1:6379> zrange mysort 0 -1 withscores
1) "zs"
2) "70"
3) "ls"
4) "80"
5) "ww"
6) "90"
# zrangebyscore 根据分数批量查询
127.0.0.1:6379> zrangebyscore mysort 0 100 withscores
1) "zs"
2) "70"
3) "ls"
4) "80"
5) "ww"
6) "90"
# zrangebyscore limit 限制返回的元素个数
127.0.0.1:6379> zrangebyscore mysort 0 100 withscores limit 0 2
1) "zs"
2) "70"
3) "ls"
4) "80"
# zincrby 给 ls 的分数增加 3
127.0.0.1:6379> zincrby mysort 3 ls
"83"
127.0.0.1:6379> zscore mysort ls
"83"
# zcount 根据分数统计元素个数
127.0.0.1:6379> zcount mysort 80 90
(integer) 2
```

## Sorted-Set使用场景

* 大型在线游戏积分排行榜
* 构建索引数据
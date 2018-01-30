# 07-Redis的数据结构之list.md

## 存储list

* ArrayList使用数组方式
* LinkedList使用双向链接方式
* 双向链表中增加数据
* 双向链表中删除数据

## 存储list常用命令

* 两端添加
* 查看列表
* 两端弹出
* 获取列表中元素个数
* 扩展命令

## 示例

```python
# lpush 左边插入
127.0.0.1:6379> lpush mylist a b c
(integer) 3
127.0.0.1:6379> lpush mylist 1 2 3
(integer) 6
# rpush 右边插入
127.0.0.1:6379> rpush mylist2 a b c
(integer) 3
127.0.0.1:6379> rpush mylist2 1 2 3
(integer) 6
# lrange 查看数组
127.0.0.1:6379> lrange mylist 0 5
1) "3"
2) "2"
3) "1"
4) "c"
5) "b"
6) "a"
127.0.0.1:6379> lrange mylist2 0 5
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
6) "3"
# lrange 0 -1 表示查看所有
127.0.0.1:6379> lrange mylist2 0 -1
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
6) "3"
# lrange 0 -2 表示查看到倒数第二
127.0.0.1:6379> lrange mylist2 0 -2
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
# lpop 从左边取出
127.0.0.1:6379> lpop mylist
"3"
127.0.0.1:6379> lrange mylist 0 -1
1) "2"
2) "1"
3) "c"
4) "b"
5) "a"
# rpop 从右边取出
127.0.0.1:6379> rpop mylist2
"3"
127.0.0.1:6379> lrange mylist2 0 -1
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
# llen 查看长度
127.0.0.1:6379> llen mylist
(integer) 5
127.0.0.1:6379> llen mylist2
(integer) 5
# llen 对于不存在 list 的长度为 0
127.0.0.1:6379> llen mylist3
(integer) 0
# lpushx 将一个值插入到已存在的列表头部，列表不存在时操作无效。
127.0.0.1:6379> lpushx mylist x
(integer) 6
127.0.0.1:6379> lrange mylist 0 -1
1) "x"
2) "2"
3) "1"
4) "c"
5) "b"
6) "a"
# lpushx 对于空 list 操作无效
127.0.0.1:6379> lpushx mylist3 x
(integer) 0
127.0.0.1:6379> lrange mylist3 0 -1
(empty list or set)
# rpushx 右边插入
127.0.0.1:6379> rpushx mylist2 y
(integer) 6
127.0.0.1:6379> lrange mylist2 0 -1
1) "a"
2) "b"
3) "c"
4) "1"
5) "2"
6) "y"
127.0.0.1:6379> lpush mylist3 1 2 3
(integer) 3
127.0.0.1:6379> lpush mylist3 1 2 3
(integer) 6
127.0.0.1:6379> lpush mylist3 1 2 3
(integer) 9
127.0.0.1:6379> lrange mylist3 0 -1
1) "3"
2) "2"
3) "1"
4) "3"
5) "2"
6) "1"
7) "3"
8) "2"
9) "1"
# lrem 从左边删除2个3
127.0.0.1:6379> lrem mylist3 2 3
(integer) 2
127.0.0.1:6379> lrange mylist3 0 -1
1) "2"
2) "1"
3) "2"
4) "1"
5) "3"
6) "2"
7) "1"
# lrem 从右边删除2个1
127.0.0.1:6379> lrem mylist3 -2 1
(integer) 2
127.0.0.1:6379> lrange mylist3 0 -1
1) "2"
2) "1"
3) "2"
4) "3"
5) "2"
# lrem 删除所有2
127.0.0.1:6379> lrem mylist3 0 2
(integer) 3
127.0.0.1:6379> lrange mylist3 0 -1
1) "1"
2) "3"
127.0.0.1:6379> lrange mylist 0 -1
1) "x"
2) "2"
3) "1"
4) "c"
5) "b"
6) "a"
# lset 在下标3插入 mmm
127.0.0.1:6379> lset mylist 3 mmm
OK
127.0.0.1:6379> lrange mylist 0 -1
1) "x"
2) "2"
3) "1"
4) "mmm"
5) "b"
6) "a"
127.0.0.1:6379> lpush mylist4 a b c
(integer) 3
127.0.0.1:6379> lpush mylist4 a b c
(integer) 6
127.0.0.1:6379> lrange mylist4 0 -1
1) "c"
2) "b"
3) "a"
4) "c"
5) "b"
6) "a"
# linsert before 在b前面插入11
127.0.0.1:6379> linsert mylist4 before b 11
(integer) 7
127.0.0.1:6379> lrange mylist4 0 -1
1) "c"
2) "11"
3) "b"
4) "a"
5) "c"
6) "b"
7) "a"
# linsert after 在b后面插入22
127.0.0.1:6379> linsert mylist4 after b 22
(integer) 8
127.0.0.1:6379> lrange mylist4 0 -1
1) "c"
2) "11"
3) "b"
4) "22"
5) "a"
6) "c"
7) "b"
8) "a"
127.0.0.1:6379> lpush mylist5 1 2 3
(integer) 3
127.0.0.1:6379> lpush mylist6 a b c
(integer) 3
127.0.0.1:6379> lrange mylist5 0 -1
1) "3"
2) "2"
3) "1"
127.0.0.1:6379> lrange mylist6 0 -1
1) "c"
2) "b"
3) "a"
# rpoplpush 从 mylist5 右边取出并从 mylist6 左边插入
127.0.0.1:6379> rpoplpush mylist5 mylist6
"1"
127.0.0.1:6379> lrange mylist5 0 -1
1) "3"
2) "2"
127.0.0.1:6379> lrange mylist6 0 -1
1) "1"
2) "c"
3) "b"
4) "a"
```

## rpoplpush 使用场景

![](https://ws3.sinaimg.cn/large/006tNc79ly1fnye9zg17dj314w0jy40p.jpg)

## 其它

* LPUSHX/RPUSHX与LPUSH/RPUSH的差别是，只有当key存在时，才会执行插入操作。
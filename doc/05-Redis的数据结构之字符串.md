# 05-Redis的数据结构之字符串.md

## 五种数据类型

* 字符串（String）
* 哈希（hash）
* 字符串列表（list）
* 字符串集合（set）
* 有序字符串集合（sorted set）

## Key定义的注意点

* 不要过长
* 不要太短
* 统一的命名规范

## 存储String

* 二进制安全的，*存入和获取的数据相同*
* Value 最多可以容纳的数据长度是 *512M*

## 存储String常用命令

* 赋值
* 取值
* 删除
* 数值增减
* 扩展命令

## 示例

```
# set 设置值
127.0.0.1:6379> set company imooc
OK
# get 获取值
127.0.0.1:6379> get company
"imooc"
# getset 先获取再设置值
127.0.0.1:6379> getset company baidu
"imooc"
127.0.0.1:6379> get company
"baidu"
127.0.0.1:6379> set person jack
OK
127.0.0.1:6379> get person
"jack"
# del 删除值
127.0.0.1:6379> del person
(integer) 1
127.0.0.1:6379> get person
(nil)
# incr 自增
127.0.0.1:6379> incr num
(integer) 1
127.0.0.1:6379> get num
"1"
127.0.0.1:6379> incr num
(integer) 2
127.0.0.1:6379> get num
"2"
# 非数值自增会出现异常
127.0.0.1:6379> incr company
(error) ERR value is not an integer or out of range
# decr 自减
127.0.0.1:6379> decr num
(integer) 1
127.0.0.1:6379> get num
"1"
# 对于不存在的值，自减会先设置默认值 0
127.0.0.1:6379> decr num2
(integer) -1
127.0.0.1:6379> get num2
"-1"
# incrby 自增并设置指定大小
127.0.0.1:6379> incrby num 5
(integer) 6
127.0.0.1:6379> get num
"6"
127.0.0.1:6379> incrby num3 5
(integer) 5
# decrby 自减设置指定大小
127.0.0.1:6379> decrby num3 5
(integer) 0
127.0.0.1:6379> decrby num4 3
(integer) -3
# append 拼接
127.0.0.1:6379> append num 5
(integer) 2
127.0.0.1:6379> get num
"65"
```
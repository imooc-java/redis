# 14-Redis的持久化的AOF的方式.md

## 优势

* 使用 AOF 持久化会让 Redis 变得非常耐久（much more durable）：你可以设置不同的 fsync 策略，比如无 fsync ，每秒钟一次 fsync ，或者每次执行写入命令时 fsync 。 AOF 的默认策略为每秒钟 fsync 一次，在这种配置下，Redis 仍然可以保持良好的性能，并且就算发生故障停机，也最多只会丢失一秒钟的数据（ fsync 会在后台线程执行，所以主线程可以继续努力地处理命令请求）。
* AOF 文件是一个只进行追加操作的日志文件（append only log）， 因此对 AOF 文件的写入不需要进行 seek ， 即使日志因为某些原因而包含了未写入完整的命令（比如写入时磁盘已满，写入中途停机，等等）， redis-check-aof 工具也可以轻易地修复这种问题。
* Redis 可以在 AOF 文件体积变得过大时，自动地在后台对 AOF 进行重写： 重写后的新 AOF 文件包含了恢复当前数据集所需的最小命令集合。 整个重写操作是绝对安全的，因为 Redis 在创建新 AOF 文件的过程中，会继续将命令追加到现有的 AOF 文件里面，即使重写过程中发生停机，现有的 AOF 文件也不会丢失。 而一旦新 AOF 文件创建完毕，Redis 就会从旧 AOF 文件切换到新 AOF 文件，并开始对新 AOF 文件进行追加操作。
* AOF 文件有序地保存了对数据库执行的所有写入操作， 这些写入操作以 Redis 协议的格式保存， 因此 AOF 文件的内容非常容易被人读懂， 对文件进行分析（parse）也很轻松。 导出（export） AOF 文件也非常简单： 举个例子， 如果你不小心执行了 FLUSHALL 命令， 但只要 AOF 文件未被重写， 那么只要停止服务器， 移除 AOF 文件末尾的 FLUSHALL 命令， 并重启 Redis ， 就可以将数据集恢复到 FLUSHALL 执行之前的状态。

## 劣势

* 对于相同的数据集来说，AOF 文件的体积通常要大于 RDB 文件的体积。
* 根据所使用的 fsync 策略，AOF 的速度可能会慢于 RDB 。 在一般情况下， 每秒 fsync 的性能依然非常高， 而关闭 fsync 可以让 AOF 的速度和 RDB 一样快， 即使在高负荷之下也是如此。 不过在处理巨大的写入载入时，RDB 可以提供更有保证的最大延迟时间（latency）。
* AOF 在过去曾经发生过这样的 bug ： 因为个别命令的原因，导致 AOF 文件在重新载入时，无法将数据集恢复成保存时的原样。 （举个例子，阻塞命令 BRPOPLPUSH 就曾经引起过这样的 bug 。） 测试套件里为这种情况添加了测试： 它们会自动生成随机的、复杂的数据集， 并通过重新载入这些数据来确保一切正常。 虽然这种 bug 在 AOF 文件中并不常见， 但是对比来说， RDB 几乎是不可能出现这种 bug 的。

## 配置

### Mac 下默认配置

* 查看配置文件

`vi /usr/local/etc/redis.conf`

```python
652 ############################## APPEND ONLY MODE ###############################
653
654 # By default Redis asynchronously dumps the dataset on disk. This mode is
655 # good enough in many applications, but an issue with the Redis process or
656 # a power outage may result into a few minutes of writes lost (depending on
657 # the configured save points).
658 #
659 # The Append Only File is an alternative persistence mode that provides
660 # much better durability. For instance using the default data fsync policy
661 # (see later in the config file) Redis can lose just one second of writes in a
662 # dramatic event like a server power outage, or a single write if something
663 # wrong with the Redis process itself happens, but the operating system is
664 # still running correctly.
665 #
666 # AOF and RDB persistence can be enabled at the same time without problems.
667 # If the AOF is enabled on startup Redis will load the AOF, that is the file
668 # with the better durability guarantees.
669 #
670 # Please check http://redis.io/topics/persistence for more information.
671
672 appendonly no # 默认为 no ，要开启的话，要改为 yes ，并重启 Redis 服务
673
674 # The name of the append only file (default: "appendonly.aof")
675
676 appendfilename "appendonly.aof" # aof 保存的文件名
677
678 # The fsync() call tells the Operating System to actually write data on disk
679 # instead of waiting for more data in the output buffer. Some OS will really flush
680 # data on disk, some other OS will just try to do it ASAP.
681 #
682 # Redis supports three different modes:
683 #
684 # no: don't fsync, just let the OS flush the data when it wants. Faster.
685 # always: fsync after every write to the append only log. Slow, Safest.
686 # everysec: fsync only one time every second. Compromise.
687 #
688 # The default is "everysec", as that's usually the right compromise between
689 # speed and data safety. It's up to you to understand if you can relax this to
690 # "no" that will let the operating system flush the output buffer when
691 # it wants, for better performances (but if you can live with the idea of
692 # some data loss consider the default persistence mode that's snapshotting),
693 # or on the contrary, use "always" that's very slow but a bit safer than
694 # everysec.
695 #
696 # More details please check the following article:
697 # http://antirez.com/post/redis-persistence-demystified.html
698 #
699 # If unsure, use "everysec".
700
    # 将 aof_buf 缓冲区中的所有内容写入并同步到 AOF 文件。
701 # appendfsync always
    # 将 aof_buf 缓冲区中的所有内容写入到 AOF 文件， 如果上次同步 AOF 文件的时间距离现在超过一秒钟，
    # 那么再次对 AOF 文件进行同步，并且这个同步操作是由一个线程专门负责执行的。
702 appendfsync everysec
    # 将 aof_buf 缓冲区中的所有内容写入到 AOF 文件， 但并不对 AOF 文件进行同步， 何时同步由操作系统来决定。
703 # appendfsync no
704
```

* 查看保存的aof文件

```python
CorningSunMac:~ corning$ ls /usr/local/var/db/redis/
appendonly.aof	dump.rdb
```

## 示例

1. 进行常规 Redis 操作，并清空数据

```python
# 进行常规的操作
127.0.0.1:6379> keys *
(empty list or set)
127.0.0.1:6379> set name jack
OK
127.0.0.1:6379> set num 10
OK
127.0.0.1:6379> set n1 1
OK
127.0.0.1:6379> set n2 2
OK
127.0.0.1:6379> keys *
1) "n2"
2) "num"
3) "name"
4) "n1"
# flushall 清空数据库
127.0.0.1:6379> flushall
127.0.0.1:6379> keys *
(empty list or set)
```

2. 停止 Redis 服务，并修改 `appendonly.aof` 文件，删除 `flushall` 命令
3. 重启 Redis 服务
4. 数据还原成功

```python
127.0.0.1:6379> keys *
1) "num"
2) "n2"
3) "name"
4) "n1"
```


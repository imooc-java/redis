# 13-Redis的持久化的RDB的方式.md

## 优势

* RDB 是一个非常紧凑（compact）的文件，它保存了 Redis 在某个时间点上的数据集。 这种文件非常适合用于进行备份： 比如说，你可以在最近的 24 小时内，每小时备份一次 RDB 文件，并且在每个月的每一天，也备份一个 RDB 文件。 这样的话，即使遇上问题，也可以随时将数据集还原到不同的版本。
* RDB 非常适用于灾难恢复（disaster recovery）：它只有一个文件，并且内容都非常紧凑，可以（在加密后）将它传送到别的数据中心，或者亚马逊 S3 中。
* RDB 可以最大化 Redis 的性能：父进程在保存 RDB 文件时唯一要做的就是 fork 出一个子进程，然后这个子进程就会处理接下来的所有保存工作，父进程无须执行任何磁盘 I/O 操作。
* RDB 在恢复大数据集时的速度比 AOF 的恢复速度要快

## 劣势

* 如果你需要尽量避免在服务器故障时丢失数据，那么 RDB 不适合你。 虽然 Redis 允许你设置不同的保存点（save point）来控制保存 RDB 文件的频率， 但是， 因为RDB 文件需要保存整个数据集的状态， 所以它并不是一个轻松的操作。 因此你可能会至少 5 分钟才保存一次 RDB 文件。 在这种情况下， 一旦发生故障停机， 你就可能会丢失好几分钟的数据。
* 每次保存 RDB 的时候，Redis 都要 fork() 出一个子进程，并由子进程来进行实际的持久化工作。 在数据集比较庞大时， fork() 可能会非常耗时，造成服务器在某某毫秒内停止处理客户端； 如果数据集非常巨大，并且 CPU 时间非常紧张的话，那么这种停止时间甚至可能会长达整整一秒。 虽然 AOF 重写也需要进行 fork() ，但无论 AOF 重写的执行间隔有多长，数据的耐久性都不会有任何损失。

## 配置

### Mac 下配置

* 查看配置文件

`vi /usr/local/etc/redis.conf`

```python
196 ################################ SNAPSHOTTING  ################################
197 #
198 # Save the DB on disk:
199 #
200 #   save <seconds> <changes>
201 #
202 #   Will save the DB if both the given number of seconds and the given
203 #   number of write operations against the DB occurred.
204 #
205 #   In the example below the behaviour will be to save:
206 #   after 900 sec (15 min) if at least 1 key changed
207 #   after 300 sec (5 min) if at least 10 keys changed
208 #   after 60 sec if at least 10000 keys changed
209 #
210 #   Note: you can disable saving completely by commenting out all "save" lines.
211 #
212 #   It is also possible to remove all the previously configured save
213 #   points by adding a save directive with a single empty string argument
214 #   like in the following example:
215 #
216 #   save ""
217
218 save 900 1
219 save 300 10
220 save 60 10000
221
222 # By default Redis will stop accepting writes if RDB snapshots are enabled
223 # (at least one save point) and the latest background save failed.
224 # This will make the user aware (in a hard way) that data is not persisting
225 # on disk properly, otherwise chances are that no one will notice and some
226 # disaster will happen.
227 #
228 # If the background saving process will start working again Redis will
229 # automatically allow writes again.
230 #
231 # However if you have setup your proper monitoring of the Redis server
232 # and persistence, you may want to disable this feature so that Redis will
233 # continue to work as usual even if there are problems with disk,
234 # permissions, and so forth.
235 stop-writes-on-bgsave-error yes
236
237 # Compress string objects using LZF when dump .rdb databases?
238 # For default that's set to 'yes' as it's almost always a win.
239 # If you want to save some CPU in the saving child set it to 'no' but
240 # the dataset will likely be bigger if you have compressible values or keys.
241 rdbcompression yes
242
243 # Since version 5 of RDB a CRC64 checksum is placed at the end of the file.
244 # This makes the format more resistant to corruption but there is a performance
245 # hit to pay (around 10%) when saving and loading RDB files, so you can disable it
246 # for maximum performances.
247 #
248 # RDB files created with checksum disabled have a checksum of zero that will
249 # tell the loading code to skip the check.
250 rdbchecksum yes
251
252 # The filename where to dump the DB
253 dbfilename dump.rdb
254
255 # The working directory.
256 #
257 # The DB will be written inside this directory, with the filename specified
258 # above using the 'dbfilename' configuration directive.
259 #
260 # The Append Only File will also be created inside this directory.
261 #
262 # Note that you must specify a directory here, not a file name.
263 dir /usr/local/var/db/redis/
```

* 查看保存的rdb文件

```python
CorningSunMac:var corning$ ls /usr/local/var/db/redis/
dump.rdb
```

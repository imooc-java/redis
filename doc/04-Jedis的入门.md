# 04-Jedis的入门.md

## 使用 Jedis 连接 Redis 服务

[JedisDemo1.java](../src/test/java/com/imooc/jedis/JedisDemo1.java)

```java
package com.imooc.jedis;

import org.junit.Before;
import org.junit.Test;
import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/**
 * Created by corning on 2018/1/29.
 */
public class JedisDemo1 {

    private String host;
    private int port;

    @Before
    public void setUp() {
        host = "127.0.0.1";
        port = 6379;
    }

    @Test
    public void demo1() {
        // 单实例测试

        // 设置IP和端口
        Jedis jedis = new Jedis(host, port);
        // jedis 测试
        jedisCommonTest(jedis);
        // 释放資源
        jedis.close();
    }

    private void jedisCommonTest(Jedis jedis) {
        // 保存数据
        System.out.println(jedis.set("name", "imooc"));
        // 获取数据并打印
        System.out.println(jedis.get("name"));
        // 删除数据
        System.out.println(jedis.del("name"));
    }

    @Test
    public void demo2() {
        // 连接池方式实现

        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(30);
        config.setMaxIdle(10);

        try (JedisPool jedisPool = new JedisPool(config, host, port);
             Jedis jedis = jedisPool.getResource()) {
            jedisCommonTest(jedis);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

}
```

## 运行结果

```
OK
imooc
1
```
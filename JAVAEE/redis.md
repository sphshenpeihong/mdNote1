# 一、redis

## 1、Nosql与Mysql

Q1：Nosql和Mysql的区别？

A：前者是非关系型 后者是关系型数据库

Q2：关系型与非关系型数据的区别？

- 数据存储在硬盘的文件上，数据之间有关联关系

- 数据库存储在内存中，存储以Key：value形式，键值对间无关系

Q3：缓存和Map的区别？

A：Map是只在单个服务器起作用，无法被其它服务器所使用，可以使用一台服务器做缓存服务器(redis、memched)，从而实现分布式缓存(多台服务器都可以访问)。



## 2、redis

### 2.1、5种基本数据类型

- 字符串类型 String
- 哈希类型 hash
- 列表类型 list
- 集合类型 set
- 有序集合类型 sortedset



### 2.2、redis的应用场景

- 缓存（数据查询、短连接、新闻内容、商品内容等等）
- 聊天室的在线好友列表
- 任务队列（秒杀、抢购、12306等等） //redis解决高并发问题
- 应用排行榜
- 网站访问统计
- 数据过期处理(可以精确到毫秒) //比如手机获取验证码 设置60秒有效
- 分布式集群架构中的session分离 / 共享



### 2.3、下载安装

- redis.windows.conf：配置文件

- redis-cli.exe：redis的客户端

- redis-server.exe：redis服务器端



### 2.4、命令操作

1、String  （缓存的key对应的value是 String类型）

- 添加： **set key value**
- 获取： **get key**
- 删除   **del key**



2、hashMap （缓存的key对应的value是 hashMap类型）

- 添加：**hset key field value**

  ​	例： hset myhash username lisi

- 获取：**hget key field**

  ​	例(获取key对应的key 的值)： hget myhash username

  ​	例（获取该key所有的值hashMap类型）：hgetall myhash

- 删除：**hdel key field**

  ​	例：hdel myhash username



3、list   （缓存的key对应的value是 list类型， list可以从头部(左边)或者尾部(右边)添加元素 ）

- 添加：lpush rpush

  ​	1. lpush key value：将元素加入列表左边

  ​	2. rpush key value：将元素加入列表右边

- 获取：按范围获取

  ​	lrange key start end ：按范围获取

- 删除：

  ​	lpop key：删除列表最左边的元素，并将元素返回

  ​	rpop key：删除列表最右边的元素，并将元素返回



4、set （缓存的key对应的value是 set类型 不允许重复元素）

- 添加：sadd key value

- 获取：smembers key：获取set集合中所有元素

- 删除：srem key value：删除set集合中的某个元素



5、sortedset （不允许重复元素，且元素有顺序） //排行榜

- 添加：zadd key score value; //添加需要添加个分数 到时候是按分数排序

- 获取：zrange key start end

- 删除：zrem key value

![image-20201222182658057](https://cdn.jsdelivr.net/gh/sphshenpeihong/javaPic/img/20201222182704.png)

6、geospatial(地理位置) key对应的值存的是 经纬度 + 位置名

- geoadd：将指定的地理空间位置（经纬度、名称）添加到指定的key中

```bash
127.0.0.1:6379> geoadd city 113.33 23.08 guangzhou 114.06 22.54 shenzhen 116.62 23.66 chaozhou 115.38 22.79 shanwei
(integer) 4
```

- geopos：从key中返回所有给定位置元素的位置（经、纬度）

```bash
127.0.0.1:6379> geopos city chaozhou
1) 1) "116.6199991106987"
   2) "23.660001068247716"
```

- geodist：返回两个给定位置之间的距离(直线距离)

```bash
127.0.0.1:6379> geodist city guangzhou chaozhou km
"342.0431"
```

- georadius：以给定的经纬度为中心，找出某一半径内的元素

```bash
127.0.0.1:6379> GEORADIUS city 114.50 22.80 100 km
1) "shenzhen"
2) "shanwei"
```

- georadiusbymember：找出位于指定范围内的元素，中心点是由给定的位置元素决定  (这个是直接给定某个元素，而georadius是给定坐标位置)

```bash
127.0.0.1:6379> GEORADIUSBYMEMBER city shanwei 200 km
1) "shenzhen"
2) "shanwei"
3) "chaozhou"
```

//使用redis客户端连接后，获取key的剩余存活时间(单位是秒)：ttl  username



### 2.5、持久化

1.redis是一个内存数据库，当redis服务器重启，获取电脑重启，数据会丢失，我们可以将redis内存中的数据持久化保存到硬盘的文件中。

2.redis持久化机制:

- RDB：在默认方式，不需要进行配置，默认就使用这种机制(即是：可能缓存中新增了1000个key后，才持久化(从内存到硬盘))

  ​	//编辑redis.windows.conf文件 save 900 10  900秒内有新加入超过10个key的话，就持久化到硬盘中一次

- AOF：日志记录的方式，可以记录每一条命令的操作。可以每一次命令操作后，持久化数据 ，也即是每一次缓存中有数据，都会去持久化到硬盘当中 (高频率使用i/o 会导致性能下降 因为占用了CPU的使用)



## 3、Jedis

### 3.1、Jedis简介

Jedis：一款java操作redis数据库的工具 //类似JDBC：导入相关jdbc的jar包 就可以用java代码操作mysql数据库

### 3.2、Jedis快速入门

#### 3.2.1、String

//缓存的value值为String类型

```java
    /**
     * 手动创建jedis对象，并且操作String类型数据
     */
    @RequestMapping("/test1.do")
    public void test1() {
        //下面我们将使用我们的Java代码访问redis服务器，需要先跟redis服务器取得连接，才能进一步对redis数据库进行操作
        //步骤和使用JDBC连接mysql数据库一样，需要先建立连接，建立完连接后，对数据库进行操作，最后释放连接
        //后面使用对象池，对连接对象进行管理，默认初始化几百个或几千个连接对象，不用每次都自己去使用构造方法创建连接对象
        //我们利用Jedis生成连接redis数据库的对象，需要知道URL和端口号
        //一般我们会定义一个jedis工具类，里面封装了使用jedis连接池生成jedis对象，提供getClinet方法，以后调用Jedis工具类去获取jedis对象
        Jedis jedis = new Jedis("localhost", 6379);
        //2、对redis数据库进行操作
        jedis.del("username1");
        jedis.set("username", "zhangsan");
        System.out.println(jedis.get("username"));
        jedis.setex("vertifyCode", 60, "675420");//key/value有效时间60秒
        //第一次运行代码的话 下面会打印null，因为还没有入redis库
        System.out.println(jedis.get("password"));
        jedis.set("password", "bugaosuni");
        //3、释放连接
        jedis.close();
    }
```

#### 3.2.2、hashMap

//缓存的value值为hashMap类型

```java
    /**
     * 缓存的value值为hashMap类型
     */
    @RequestMapping("/test2.do")
    public void test2() {
        Jedis jedis = new Jedis();
        jedis.hset("hashMap1", "username", "zhangsan");
        jedis.hset("hashMap1", "password", "123456");
        System.out.println(jedis.hget("hashMap1", "username"));
        Map<String, String> hashMap1 = jedis.hgetAll("hashMap1");//已获得redis数据库该key对应的Map类型
        //获得map中的所有key集合
        Set<String> keySet = hashMap1.keySet();
        //获得map中的每一对映射关系
        Set<Map.Entry<String, String>> entrySet = hashMap1.entrySet();
        for (Map.Entry<String, String> entry : entrySet) {
            System.out.println(entry.getKey());
            System.out.println(entry.getValue());
        }
        jedis.close();
    }
```

#### 3.2.3、list

//缓存的value值为list类型

```java
    /**
     * 缓存的value值为ArrayList类型
     */
    @RequestMapping("/test3.do")
    public void test3(){
        Jedis jedis = new Jedis();
        //类似于队列
        jedis.lpush("list1", "a");
        jedis.lpush("list1", "b");
        jedis.rpush("list1", "c");
        System.out.println(jedis.lrange("list1", 0, 2));
        System.out.println(jedis.lpop("list1"));//删除最左边一个元素
        System.out.println(jedis.lrange("list1", 0, -1));
        jedis.close();
    }
```

#### 3.2.4、set

//缓存的value值为set类型

```java
    /**
     * 缓存的value值为set类型
     */
    @RequestMapping("/test4.do")
    public void test4(){
        Jedis jedis = new Jedis();
        jedis.sadd("set1", "a");
        jedis.sadd("set1", "b");
        jedis.sadd("set1", "c");
        System.out.println(jedis);
        Set<String> set1 = jedis.smembers("set1");
        System.out.println(set1);
    }
```

#### 3.2.5、JedisPool

//使用Jedis连接池获取对象

```java
package com.sph.practice.test.markdown.redis.jedis;

import redis.clients.jedis.Jedis;
import redis.clients.jedis.JedisPool;
import redis.clients.jedis.JedisPoolConfig;

/**
 * Created by Shen Peihong on 2020/11/9 21:41
 * Description:初始化Jedis连接池
 *
 * @since 1.0.0
 */
public class JedisClient {

    //Jedis连接池对象,需要初始化，不然连连接哪个redis服务器都不知道
    private static JedisPool jedisPool;

    static {
        //双重校验锁---单例实例化jedis连接池
        if (jedisPool == null){
            synchronized (JedisClient.class){
                if (jedisPool == null){
                    init();
                }
            }
        }
    }

    //初始化jedis连接池对象
    private static void init(){
        JedisPoolConfig config = new JedisPoolConfig();
        //最大连接数 （长工+临时工）
        config.setMaxTotal(500);
        //最大空闲数 (长工)
        config.setMaxIdle(200);
        //等待可用连接最大时间，超过这个时间仍未获取到jedis实例的话，直接抛JedisConnectionException
        config.setMaxWaitMillis(5000);
        jedisPool = new JedisPool(config, "localhost", 6379, 1000);
    }

    //对外提供获取jedis连接池实例的方法
    public static Jedis getClient(){
        return jedisPool.getResource();
    }

}

```



## 4、RedisTemplate

### 4.1、直接方法

**1.判断key是否存在**

```java
Boolean hasKeyOfUsername = redisTemplate.hasKey("username");//判断key是否存在
```

**2.删除单个key**

```java
redisTemplate.delete("username");//删除redis中该key
```

3.删除多个key

```java
redisTemplate.delete(Lists.newArrayList("zhangsan","lisi"));//批量删除keys
```

4.指定key的有效时间

```java
redisTemplate.expire("username", 100, TimeUnit.MILLISECONDS);//指定该Key的有效时间为100毫秒
```

5.根据key获得过期时间

```java
Long expireTime = redisTemplate.getExpire("username");//获取该Key的过期时间，时间单位为秒，小于1秒的话直接值为0
```



### 4.2、String

**1.设置简单的键值对，并获取**

```java

```

**2.设置简单的键值对的同时并且设置过期时间**

```java

```

**3.设置键值对（当key不存在时，新增，若key存在，则不新增）**

```java

```

**4.设置键值对（当key不存在时，新增，若key存在，则不新增，同时设置过期时间）**

```java

```

**5.将一个map集合的K-V都放入缓存里**

```java

```

**6.将一个map集合的K-V都放入缓存里（若map中某个key在缓存中已存在，则添加不成功）**

```java

```

**7.参数是一个列表（keys），获取一批key所对应的值，返回值也是一个List（坐标一一映射）**

```java

```

**8.获取缓存中key所对应的值，同时给该key赋予新的值**

```java

```

**9.将一个缓存的值进行自增，返回值是自增后的结果**

```java

```





























### 4.3、List



### 4.4、HashMap



### 4.5、HashSet



### 4.6、HashSet(带分数)



### 4.7、Geospatial



### 4.8、Hyperloglog







## 5、服务器一些常用命令

> ttl

- (integer)   -1 ：当前key是永久的
- (integer)   -2 ：当前key不存在



> get

- (nil)：当前key不存在








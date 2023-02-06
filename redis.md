# 启动

`cd redis-6.2.4/src`

以自己设置的配置文件启动redis-server

`./redis-server ../redis.conf` 

`./redis-cli`

# Redis Key

## keys * : 查看当前库中所有key

![image-20210720101336973](typora-user-images\image-20210720101336973.png)

## exists : 判断当前库中是否存在哪个key

![image-20210720101419645](typora-user-images\image-20210720101419645.png)

## type key : 查看你的key是什么类型

![image-20210720101451937](typora-user-images\image-20210720101451937.png)

## del和unlink : 删除key

![image-20210720101603255](typora-user-images\image-20210720101603255.png)

- del:删除指定的key数据
- unlink:根据value选择非阻塞删除，仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作。

## expire key 时间:设置key过期时间 单位为秒

## ttl key:查看还有多少秒过期

### -1代表永不过期

### -2代表已过期

 ## select: 切换数据库

## dbsize: 查看当前数据库的key的数量

## flushdb: 清空当前库

# 五大数据类型

- 字符串（String）
- 列表（List）
- 集合（Set）
- 哈希（Hash）
- 有序集合（Zset）

## String

String类型是二进制安全的。意味着Redis的String可以包含任何数据，比如jpg图片或者序列化的对象。String是Redis中最基本的数据类型，一个String的value最多可以是512M。

### set和get

`set key value`

`get key`

![image-20210720103533486](typora-user-images\image-20210720103533486.png)

如果再set相同的key，会覆盖掉原来的值。

### append

`append key value`

![image-20210720103644726](typora-user-images\image-20210720103644726.png)

### strlen

`strlen key`

![image-20210720103703327](typora-user-images\image-20210720103703327.png)

### setnx

`setnx key value`

只有key不存在时 设置key的值

### incr和decr

当存入的是数字时，可以通过incr和decr实现+1和-1。

`incr key`

`decr key`

![image-20210720104203595](typora-user-images\image-20210720104203595.png)

### incrby和decrby

`incrby key value`

`decrby key value`

让key实现+value或者-value。

![image-20210720104312697](typora-user-images\image-20210720104312697.png)

### mset

`mset k1 v1 k2 v2 ...`

![image-20210720105044969](typora-user-images\image-20210720105044969.png)

### mget

`mget k1 k2 ...`

![image-20210720105112985](typora-user-images\image-20210720105112985.png)

### msetnx

`mset k1 v1 k2 v2 ...`

当且仅当k1，k2，……全都不存在才执行。

### getrange

`getrange key start end`

左闭右闭

![image-20210720105509125](typora-user-images\image-20210720105509125.png)

### setrange

`setrange key start value`

从start位置开始用value覆盖

![image-20210720105652227](typora-user-images\image-20210720105652227.png)

### setex

`setex key seconds value`

在设置值的时候设置过期时间

相当于`set key value`+`expire key seconds`

### getset

`getset key value`

得到旧值，并设置新值

![image-20210720105939595](typora-user-images\image-20210720105939595.png)

## List

单键多值。

Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表头部或尾部，底层是个双向链表，对两端操作性能很高，通过索引下标的操作中间节点性能较差。

List的数据结构为快速链表quickList。

### lpush/rpush

lpush从左边插入 rpush从右边插入

`lpush/rpush key v1 v2 v3 ...`

![image-20210720110754897](typora-user-images\image-20210720110754897.png)

从左到右依次是v3 v2 v1。

![image-20210720110834106](typora-user-images\image-20210720110834106.png)

从左到右依次是v1 v2 v3

### lpop/rpop

从左边或右边取出一个值。**值在键在，值光键亡。**

`lpop/rpop key`

![image-20210720111341597](typora-user-images\image-20210720111341597.png)

### rpoplpush

`rpoplpush k1 k2`

从k1列表右边吐出一个值 插入到k2列表左边

### lrange

`lrange key start end`

从start取到end 0是第一个 -1是最后一个 所以lrange key 0 -1取所有值

### lindex

`lindex key index`

按照索引下标获得元素 从左到右

### llen

`llen key`

获得列表长度

### linsert

`linsert key before value newvalue`

在value的前面（左面）插入newvalue值。

### lrem

`lrem key n value`

从左边删除n个value

![image-20210720112342914](typora-user-images\image-20210720112342914.png)

2）和4）的newv11会被删掉

### lset

`lset key index value`将列表key下标为index的值替换为value

## Set

Java中的HashSet的内部实现用的HashMap，只不过所有的value都指向同一个对象。Redis的set结构也是一样的，它的内部也是hash结构，所有的value都指向同一个内部值。

### sadd

`sadd key v1 v2 ...`

将一个或多个member存入集合key中 已经存在的member会被忽略

### smembers

`smembers key`

取出集合的所有值

### sismember

`sismember key value`

判断key是否含有value 包含为1 不包含为0

### scard

`scard key`

返回集合的元素个数

### srem

`srem key v1 v2...`

删除集合的某个元素

### spop

`spop key`

随机从集合中弹出一个值

### srandmember

`srandmember key n`

随即从key集合取出n个值 不会从集合中删除

### smove

`smove src dst value`

把集合中的一个值从一个集合移到另一个集合

### sinter

`sinter k1 k2`

返回两个集合的交集

### sunion

`sunion k1 k2`

返回两个集合的并集

### sdiff

`sdiff k1 k2`

返回两个集合的差集（K1 - K2）

## Hash

Redis hash是一个键值对集合，一个string类型的`field`和`value`得到映射表，特别适合用于存储对象，类似于Java的Map<String,Object>。

![image-20210720160449012](typora-user-images\image-20210720160449012.png)

Hash类型对应的数据结构有两种：zipList和hashTable。当field-value长度较短且个数较少时，使用前者，否则使用后者。

### hset

`hset key field value`

给key集合的field键赋值value

### hget

`hget key1 field` 

从集合key1的field中取出value

### hmset

`hmset k1 field1 value1 field2 value2 ...`

批量设置hash的值

### hexists

`hexists k1 field` 

查看集合k1中 给定field是否存在

### hkeys

`hkeys k1`

列出k1的所有field

### hvals

`hvals k1`

列出k1的所有的value

### hincrby

`hincrby key field increment`

为集合key中的域field的值加上增量

### hsetnx

`hsetnx key field value`

将集合key中的域field中的值设为value，当且仅当field不存在。



## Zset(Sorted Set)

Zset和Set很相似，没有重复元素。不同之处是Zset的每个成员都关联了一个评分（score），这个评分被用来按照从最低分到最高分的方式排序集合中的成员，集合的成员是唯一的，但是评分可以重复。

Zset提供一个非常特别的数据结构，一方面它等价于Java的Map<String,Double>，可以为每一个元素value赋予一个权重score，另一方面它又类似于TreeSet，内部的元素会按照权重score排序。

Zset底层使用了两个数据结构：

- hash

hash的作用是关联元素value和权重score，保障元素value的唯一性，并且可以通过元素value找到相应的score值。

- 跳跃表

目的在于给value排序，根据score的范围获取元素列表。

### zadd

`zadd key s1 v1 s2 v2...`

将member元素及其score插入到有序集合key中

![image-20210720161840749](typora-user-images\image-20210720161840749.png)

### zrange

`zrange key start end [withscores]`

返回有序集合key，下标在start和end之间的元素，带withscores可以让分数一起和值返回。

![image-20210720161845376](typora-user-images\image-20210720161845376.png)

### zrangebyscore

`zrangebyscore key mins maxs [withscores]`

返回分数在mins和max之间的结果，从小到大排列。

### zincrby

`zincrby key increment value`

为元素的score加上增量。

![image-20210720162230397](typora-user-images\image-20210720162230397.png)

### zrem

`zrem key value`

删除该集合下指定值的元素

### zcount

`zcount key min max`

返回该集合分数区间内的元素个数

### zrank

`zrank key value`

返回该值在集合中的排名，**从0开始**。

# 配置文件

## Units单位

配置大小的单位，只支持bytes，不支持bit，大小写不敏感。

## INCLUDES

有公共部分，进行包含

## NETWORK

### bind

如果想通过其他电脑远程连接，要把第75行的`bind 127.0.0.1`注释掉。

![image-20210720163134315](typora-user-images\image-20210720163134315.png)

### protected-mode

将本机访问的保护模式设置no，使之支持远程访问

![image-20210720163125140](typora-user-images\image-20210720163125140.png)

### tcp-backlog

![image-20210720163221555](typora-user-images\image-20210720163221555.png)

![image-20210720163206257](typora-user-images\image-20210720163206257.png)

## GENERAL

### daemonize

![image-20210720163332357](typora-user-images\image-20210720163332357.png)

设为yes，守护进程，后台启动

### pidfile

存放pid文件的位置

### loglevel

日志的级别。

### databases

`databases 16`

redis默认16个库，我们用的0号库。

## SECURITY

### 设置密码

redis默认没有密码，

## LIMITS

### maxclients

![image-20210720163644316](typora-user-images\image-20210720163644316.png)

### maxmemory

![image-20210720163659097](typora-user-images\image-20210720163659097.png)

![image-20210720164129767](typora-user-images\image-20210720164129767.png)

# 发布和订阅

Redis发布订阅（pub/sub）是一种消息通信模式：发送者（pub）发送消息，订阅者（sub）接收消息。

Redis客户端可以订阅任意数量的频道。

- 打开一个客户端订阅channel1

![image-20210720165157077](typora-user-images\image-20210720165157077.png)

- 打开另一个客户端，给channel1发布消息hello

![image-20210720165208118](typora-user-images\image-20210720165208118.png)

- 打开第一个客户端可以看到发送的消息

![image-20210720165223940](typora-user-images\image-20210720165223940.png)

# Redis6的新数据类型

## Bitmaps

- Bitmaps本身不是一种数据类型，实际上它就是字符串（key-value），但是它可以对字符串的位进行操作，提高内存使用率和开发效率。
- Bitmaps单独提供了一套命令，所以在Redis中使用Bitmaps和使用字符串方法不太相同。可以把Bitmaps想象成一个以位为单位的数组，数组的每个单元只能存储0和1，数组的下标在Bitmaps中叫做偏移量。

### setbit

`setbit key offset value`

设置Bitmaps中某个偏移量的值，offset从0开始。

![image-20210720170016917](typora-user-images\image-20210720170016917.png)

![image-20210720170030707](typora-user-images\image-20210720170030707.png)

![image-20210720170107397](typora-user-images\image-20210720170107397.png)

### getbit

`getbit key offset`

获取Bitmaps中某个偏移量的值

![image-20210720170202797](typora-user-images\image-20210720170202797.png)

### bitcount

统计字符串被设置为1的bit数，一般情况下，给定的整个字符串都会被进行计数，通过指定额外的start或end参数，可以只让计数在特定的位上进行，start和end可以为负，比如-1代表最后一位。start和end，两者皆包含。**注意start和end代表的是字节数，也就是8位**！

`bitcount key [start end]`

![image-20210720170610205](typora-user-images\image-20210720170610205.png)

> 注意：redis的setbit设置的是bit的位置，而bitcount计算的是byte的位置。

### bitop

`bitop and(or/not/xor) <destKey> [key...]`

bitop是一个复合操作，它可以做多个Bitmaps的and，or，not，xor操作并将结果保存在destKey中。

## HyperLogLog

在工作当中，我们经常会遇到与统计相关的功能需求，比如统计网站的PV(Page View，页面访问量)，可以使用Redis的incr，incrby轻松实现。

但像UV(UniqueVisitor，独立访客)、独立IP数，搜索记录数等需要去重和计数的问题如何解决，这种求集合中不重复元素个数的问题称为基数问题。

Redis HyperLogLog是用来做基数统计的算法，优点是在输入元素的数量或者体积非常大时，计算基数的空间总是固定的，而且是很小的。在Redis里面，每个HyperLogLog键只需要花费12KB的内存，就可以计算接近2^64个不同元素的基数。

但是因为HyperLogLog只会根据输入元素来计算基数，而不会储存输入元素本身，所以HyperLogLog不能像集合那样，返回输入的各个元素。



### pfadd

`pfadd key element [element...]`

添加指定元素到HyperLogLog中，如果执行命令后估计的近似基数发生变化，则返回1，否则返回0。

![image-20210720171625794](typora-user-images\image-20210720171625794.png)

### pfcount

`pfcount key [key...]`

计算近似基数，可以计算多个。比如用HyperLogLog存储每天的UV，计算一周的UV可以用七天的UV合并计算。

![image-20210720171726756](typora-user-images\image-20210720171726756.png)

### pfmerge

`pfmerge destKey srcKey [srcKey...]`

将一个或多个HyperLogLog合并后的结果存储到另一个HyperLogLog中。

## Geospatial

Redis3.2中增加了GEO类型的支持，GEO是地理信息，即元素的二维坐标，Redis基于该类型，提供了经纬度设置，查询，范围查询，距离查询，经纬度Hash等常见操作。

### geoadd

`geoadd key longitude latitude member [longitude latitude member...]`

- 两极无法直接添加，一般会下载城市数据，直接通过Java程序一次性导入。
- 有效的经度从-180°到180°。有效的纬度从-85.05112878°到85.05112878°。
- 当坐标位置超出指定范围时，该命令返回一个错误。
- 已经添加的数据，是无法再次往里面添加的。

![image-20210720172906114](typora-user-images\image-20210720172906114.png)

### geopos

`geopos key member [member...]`

![image-20210720173001295](typora-user-images\image-20210720173001295.png)

### geodist

`geodist key mem1 mem2 [m|km|ft|mi]`

获取两个位置之间的直线距离，默认单位为m。

![image-20210720173119537](typora-user-images\image-20210720173119537.png)

### georadius

`georadius key longitude latitude radius m|km|ft|mi`

以给定经纬度为中心，找出某一半径内的元素。

# Jedis

## 导入依赖

```xml
<dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>3.6.1</version>
</dependency>
```

## 创建Jedis对象

```java
Jedis jedis = new Jedis("192.168.56.141", 6379);
```

构造器第一个参数为redis数据库所在主机的ip，第二个参数为redis端口，默认6379.

## 测试连接

```java
String value = jedis.ping();
System.out.println(value);
```

> SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".报错解决

maven添加依赖

```xml
<dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-simple</artifactId>
            <version>1.7.31</version>
            <scope>compile</scope>
</dependency>
```

![image-20210720195705932](typora-user-images\image-20210720195705932.png)

## 测试方法

```java
@Test
    public void test(){
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        jedis.set("k1","v1");
        jedis.set("k2","v2");
        jedis.set("k3","v3");
        Set<String> keys = jedis.keys("*");
        for (String key:keys){
            System.out.println(key);
        }
    }
```

![image-20210720200111961](typora-user-images\image-20210720200111961.png)

除了set，还有get，ttl等方法。

还有mset方法同时加入多个键值对。

## List操作

```java
public class JedisDemo2 {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        jedis.lpush("key1","v1","v2","v3");
        List<String> key1 = jedis.lrange("key1", 0, -1);
        for (String s : key1) {
            System.out.println(s);
        }
    }
}

```

![image-20210720200543312](typora-user-images\image-20210720200543312.png)

## Set操作

```java
public class JedisDemo3 {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        jedis.sadd("name","n1","n2","n3");
        Set<String> name = jedis.smembers("name");
        for (String s : name) {
            System.out.println(s);
        }
    }
}
```

![image-20210720200745095](typora-user-images\image-20210720200745095.png)

## Hash操作

```java
public class JedisDemo4 {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        jedis.hset("user","age","20");
        String s = jedis.hget("user", "age");
        System.out.println(s);
        Map<String, String> map = new HashMap<>();
        map.put("name","hawkeye");
        map.put("age","20");
        jedis.hset("user2",map);
        String name = jedis.hget("user2", "name");
        String age = jedis.hget("user2","age");
        System.out.println(name+" "+age);
    }
}
```

除了传统的hset方法还有重载的hset方法，参数是将field和value改为`Map<String,String>`

## Zset操作

```java
public class JedisDemo5 {
    public static void main(String[] args) {
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        jedis.zadd("china",100,"shanghai");
        jedis.zadd("china",200,"beijing");
        jedis.zadd("china",300,"shenzhen");
        Set<String> china = jedis.zrange("china", 0, -1);
        for (String s : china) {
            System.out.println(s);
        }

    }
}

```

![image-20210720201508571](typora-user-images\image-20210720201508571.png)

## 实例：模拟手机验证码

- 输入手机号，点击发送后随机生成6位数字码，两分钟有效。
- 输入验证码点击验证，返回成功或失败。
- 每个手机号每天只能输入三次

大致分为三个模块

> 生成验证码模块

```java
 /**
     * @return 6位随机验证码
     */
    public static String getCode(){
        Random random = new Random();
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < 6; i++) {
            int r = random.nextInt(10);
            sb.append(r);
        }
        return sb.toString();
    }
```

> 处理数据库模块

```java
 public static boolean verifyCode(String phoneNumber,String code){
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        //redis中的手机发送验证码的次数
        String countKey = "VerifyCode" + phoneNumber + ":count";
        //redis中手机号对应的验证码
        String codeKey = "VerifyCode" + phoneNumber + ":code";
        String count = jedis.get(countKey);
        if (count == null){
            //第一次发送 设置发送次数为1
            jedis.setex(countKey,24*60*60,"1");
        }else if (Integer.parseInt(count) <= 2){
            jedis.incr(countKey);
        }else if (Integer.parseInt(count) > 2){
            System.out.println("今天发送次数已经用尽");
            jedis.close();
            return false;
        }
        jedis.setex(codeKey,120,code);
        jedis.close();
        return true;
    }
```

这里注意`else if (Integer.parseInt(count) <= 2)`是<=2，因为发第三个的时候次数是2，要发第三个，如果改成3则会发四次。

> 判断输入的验证码和数据库中对应的验证码是否相同的模块

```java
public static boolean isValid(String phoneNumber,String code){
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        String codeKey = "VerifyCode" + phoneNumber + ":code";
        String codeData = jedis.get(codeKey);
        if (code != null){
            if (code.equals(codeData)){
                return true;
            }else{
                return false;
            }
        }else{
            return false;
        }
    }
```

> 最后的代码

```java
package com.xzc.jedis;

import redis.clients.jedis.Jedis;

import java.util.Random;
import java.util.Scanner;
import java.util.regex.Pattern;

public class PhoneCode {
    public static boolean isNumeric(String str){
        final Pattern pattern = Pattern.compile("[0-9]*");
        return pattern.matcher(str).matches();
    }

    public static void main(String[] args) {
        String code = getCode();
        System.out.println(code);
        boolean isSuccessful = verifyCode("12345678901", code);
        if (isSuccessful) {
            Scanner sc = new Scanner(System.in);
            String s = sc.nextLine();
            if (s.length() == 6 && isNumeric(s)) {
                if (isValid("12345678901", s)) {
                    System.out.println("输入正确！");
                } else {
                    System.out.println("输入错误！");
                }
            } else {
                System.out.println("输入有误！");
            }
        }

    }

    /**
     * @return 6位随机验证码
     */
    public static String getCode(){
        Random random = new Random();
        StringBuffer sb = new StringBuffer();
        for (int i = 0; i < 6; i++) {
            int r = random.nextInt(10);
            sb.append(r);
        }
        return sb.toString();
    }

    public static boolean verifyCode(String phoneNumber,String code){
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        //redis中的手机发送验证码的次数
        String countKey = "VerifyCode" + phoneNumber + ":count";
        //redis中手机号对应的验证码
        String codeKey = "VerifyCode" + phoneNumber + ":code";
        String count = jedis.get(countKey);
        if (count == null){
            //第一次发送 设置发送次数为1
            jedis.setex(countKey,24*60*60,"1");
        }else if (Integer.parseInt(count) <= 2){
            jedis.incr(countKey);
        }else if (Integer.parseInt(count) > 2){
            System.out.println("今天发送次数已经用尽");
            jedis.close();
            return false;
        }
        jedis.setex(codeKey,120,code);
        jedis.close();
        return true;
    }

    public static boolean isValid(String phoneNumber,String code){
        Jedis jedis = new Jedis("192.168.56.141", 6379);
        String codeKey = "VerifyCode" + phoneNumber + ":code";
        String codeData = jedis.get(codeKey);
        if (code != null){
            if (code.equals(codeData)){
                return true;
            }else{
                return false;
            }
        }else{
            return false;
        }
    }
}

```

测试最后是成功的。

# SpringBoot整合Redis

## maven配置

```xml
 		<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-redis</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.commons</groupId>
            <artifactId>commons-pool2</artifactId>
            <version>2.9.0</version>
        </dependency>
```

```yaml
spring:
  redis:
    host: 192.168.56.141
    port: 6379
    database: 0
    timeout: 1800000
    lettuce:
      pool:
        max-active: 20
        max-wait: -1
        max-idle: 5
        min-idle: 0

```

## 配置类

```java

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheConfiguration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;
import org.springframework.data.redis.serializer.RedisSerializationContext;
import org.springframework.data.redis.serializer.RedisSerializer;
import org.springframework.data.redis.serializer.StringRedisSerializer;

import java.time.Duration;


//开启缓存
@EnableCaching
@Configuration
public class RedisConfig  extends CachingConfigurerSupport {
    @Bean
    public RedisTemplate<String,Object> redisTemplate(RedisConnectionFactory factory){
        RedisTemplate<String,Object> template = new RedisTemplate<>();
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        template.setConnectionFactory(factory);
        template.setKeySerializer(redisSerializer);
        template.setValueSerializer(jackson2JsonRedisSerializer);
        template.setHashKeySerializer(jackson2JsonRedisSerializer);
        return template;
    }
    @Bean
    public CacheManager cacheManager(RedisConnectionFactory factory){
        RedisSerializer<String> redisSerializer = new StringRedisSerializer();
        Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
        ObjectMapper om = new ObjectMapper();
        om.setVisibility(PropertyAccessor.ALL,JsonAutoDetect.Visibility.ANY);
        om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
        jackson2JsonRedisSerializer.setObjectMapper(om);
        RedisCacheConfiguration config = RedisCacheConfiguration.defaultCacheConfig()
                .entryTtl(Duration.ofSeconds(600))
                .serializeKeysWith(RedisSerializationContext.SerializationPair.fromSerializer(redisSerializer))
                .serializeValuesWith(RedisSerializationContext.SerializationPair.fromSerializer(jackson2JsonRedisSerializer))
                .disableCachingNullValues();
        RedisCacheManager cacheManager = RedisCacheManager.builder(factory)
                .cacheDefaults(config)
                .build();
        return cacheManager;
    }

}

```

## Controller

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/redisTest")
public class RedisTestController {


    @Autowired
    private RedisTemplate redisTemplate;
    @GetMapping
    public String testRedis(){
        //设置值到redis
        redisTemplate.opsForValue().set("key","value");
        //从redis获取值
        String v = (String)redisTemplate.opsForValue().get("key");
        return v;
    }
}
```

![image-20210720232453783](typora-user-images\image-20210720232453783.png)

注意以下几点：

- redis配置类是固定写法
- springboot整合redis后用`@autowired`自动注入`RedisTemplate`
- 用`RedisTemplate`对象的`opsForValue`方法来进行set和get



# 事务和锁基本操作

## 事务定义

Redis事务是一种单独的隔离操作，事务中的所有命令都会序列化，按顺序地执行，事务在执行过程中，不会被其他客户端发送来的命令请求所打断。

Redis事务的主要作用就是串联多个命令防止别的命令插队。

## Multi、Exec、discard

从输入Multi命令开始，输入的命令都会依次进入命令队列中，但不会执行，直到输入Exec后，redis会将之前的命令队列中的命令依次执行，组队的过程中可以通过discard来放弃组队。

![image-20210721160338116](typora-user-images\image-20210721160338116.png)

![image-20210721160216981](typora-user-images\image-20210721160216981.png)

![image-20210721160327446](typora-user-images\image-20210721160327446.png)

## 事务的错误处理

组队中的某个命令出现了报告错误，执行时整个所有队列都会被取消。

执行阶段的某个命令报出了错误，则只有报错的命令不会执行，其他命令正常执行，不会回滚。



# 事务冲突

![image-20210721161045951](typora-user-images\image-20210721161045951.png)

## 悲观锁

![image-20210721161338296](typora-user-images\image-20210721161338296.png)

每次操作前先上锁，别人就不能操作，自己操作完再释放锁。

悲观锁（Pessimistic Lock），每次拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会block直到他拿到锁。传统的关系型数据库里面就用到了很多这种锁机制。

## 乐观锁

![image-20210721162254980](typora-user-images\image-20210721162254980.png)

乐观锁（Optimistic Lock），每次拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。乐观锁适用于多读的数据类型，这样可以提高吞吐量，Redis就是利用这种check-and-set机制实现事务的。

## Watch

在执行multi之前，先执行`watch key1 [key2 ...]`可以监视一个或多个key，如果在事务执行之前这个或这些key被其他命令所改动，那么事务将被打断。

![image-20210721163123692](typora-user-images\image-20210721163123692.png)

两边同时watch这个key，一个执行的时候，另一个虽然能添加事务，但是不能执行。这个过程就是被添加了乐观锁。

## Redis事务三特性

### 单独的隔离操作

事务中的所有命令都会序列化，按顺序执行，事务在执行过程中，不会被其他客户端发来的命令请求所打断。

### 没有隔离级别的概念

队列中的命令没有提交之前都不会被实际执行，因为事务提交前任何指令都不会被实际执行。

### 不保证原子性

事务中如果有一条命令执行失败，其后的命令仍然会被执行，没有回滚。

# 持久化

## RDB（Redis DataBase）

### RDB过程

在指定的`时间间隔`内将内存中的数据集`快照`写入磁盘，它恢复时将快照文件直接读到内存里。

Redis会单独创建（fork）一个子进程来进行持久化，会先将数据写入一个`临时文件`中，待持久化过程都结束了，再用这个临时文件替换上次持久化好的文件，整个过程中，主进程是不进行任何IO操作的，这就确保了极高的性能，如果需要进行大规模数据的回复，且对于数据回复的完整性不是非常敏感，那RDB方式要比AOF方式更加高效。`RDB的缺点是最后一次持久化后的数据可能丢失`。因为redis最后一次，持久化一部分时，redis突然挂掉，那么数据会丢失。



在哪个目录启动redis-server，就在哪生成dump.rdb文件。

可以在配置文件内用save设置多少s内有几个key发生变化就会有持久化操作。

假设30s内10个key发生变化就会有持久化操作，我set12个key，前10个会被持久化，第11，12会被重新计数。

- `save`：save时只管保存，去其他不管，全部阻塞，手动保存，不建议。
- `bgsave`：redis会在后台异步进行快照操作，快照同时还可以响应客户端需求。

bgsave 命令执行之后立即返回 OK ，然后 Redis fork 出一个新子进程，原来的 Redis 进程(父进程)继续处理客户端请求，而子进程则负责将数据保存到磁盘，然后退出，推荐使用。

可以通过`lastsave`命令获取最后一次快照时间。

### RDB的恢复

- 先通过config det dir查询rdb文件目录
- 将*.rdb的文件拷贝到别的的地方
- 关闭Redis
- 先把备份的文件拷贝到工作目录下 `cp dump2.rdb dump.rdb`
- 启动Redis，备份数据会直接加载

### RDB优势

- 适合大规模的数据恢复
- 对数据的完整性和移植性要求不高更适合使用
- 节省磁盘空间
- 恢复速度快

### RDB劣势

- Fork的时候，内存中的数据被克隆了一份，大致2倍的膨胀性需要考虑
- 虽然Redis在fork时使用了写时拷贝技术，但是如果数据庞大时还是比较消耗性能。
- 在备份周期在一定时间间隔做一次备份，如果Redis意外down掉的话，就会丢失最后一次快照的所有修改。



## AOF（Append Only File）

### AOF简介

以日志的形式来记录每个写操作（增量保存），将Redis执行过的所有写指令记录下来（读操作不记录），只许追加文件但不可以改写文件，redis启动之初会读取该文件重新构建数据，redis重启的话就根据日志文件的内容将写指令从前到后执行一次以完成数据的恢复工作。

### 持久化流程

- 客户端的请求写命令会被append追加到AOF缓冲区内
- AOF缓冲区根据AOF持久化策略（always，everysec，no）将操作sync同步到磁盘的AOF文件中。
- AOF文件大小超过重写策略或手动重写时，会对AOF文件rewrite重写，压缩AOF文件容量。
- Redis服务重启时，会重新load加载AOF文件中的写操作达到数据恢复的目的。

### AOF默认不开启

![image-20210721171130694](typora-user-images\image-20210721171130694.png)

可以redis.conf中配置文件名称，默认appendonly.aof，保存路径同RDB路径。

如果AOF和RDB同时开启，系统默认取AOF数据。

将配置文件的appendonly设为yes 重启redis 发现目录下有该文件。

![image-20210721171337195](typora-user-images\image-20210721171337195.png)

### 异常恢复

如果遇到AOF文件损坏，通过命令`redis-check-aof --fix appendonly.aof`进行恢复

备份被写坏的AOF文件，重启redis，重新加载。

![image-20210721171831615](typora-user-images\image-20210721171831615.png)

### 同步频率设置

- appendfsync always

始终同步，每次Redis写入都会立刻记入日志，性能差但是数据完整性好。

- appendfsync everysec

每秒同步，每秒记入日志一次，如果宕机，本秒数据可能丢失。

- appendfsync no

redis不主动同步，把同步时机交给操作系统。

### Rewrite压缩

AOF采用文件追加方式，文件越来越大，为避免出现这种情况，新增了重写机制，当AOF文件大小超过设定的阈值时，Redis就会启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集，可以使用命令·`bgrewriteaof`

![image-20210721172236778](typora-user-images\image-20210721172236778.png)

![image-20210721172252164](typora-user-images\image-20210721172252164.png)

重写流程：

![image-20210721172306517](typora-user-images\image-20210721172306517.png)

### AOF优势

- 备份机制更稳健，丢失数据概率低
- 可读的日志文本，通过操作AOF文件，可以处理错误操作

### AOF劣势

- 比起RDB占用更多的磁盘空间
- 恢复备份速度慢
- 每次读写都同步的话，有一定性能压力
- 存在个别BUG，造成不能恢复

## 用哪个

官方推荐两个都启用。如果对数据不敏感，可以单独用RDB，不建议单独AOF，因为可能出现Bug。




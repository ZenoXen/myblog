---
title: Spring与Redis整合
date: 2021-03-01 20:18:30
tags: [Spring, 缓存, redis]
categories: [Spring, Cache]
description: 学了Redis却不知道如何在项目中有效地使用它？来看看这篇文章，之后你会对Redis在Spring项目中的应用有一个大致的了解
---
如果你学过Redis，应该知道它是一个键值对的内存数据库应用，一般是作为服务器后端中的数据库或者是缓存来使用（国内大环境下用作缓存更多）。

# Redis作为数据库使用

这里主要介绍两个比较常见的Redis操作库，Jedis和SpringDataRedis

## Jedis

Jedis是一个小巧、简单、易用的Redis client，Java导入Jedis依赖后，可以直接对Redis进行连接，并直接操作各种数据结构。

可以使用下面的pom依赖导入Jedis。

```xml
<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.5.2</version>
    <type>jar</type>
    <scope>compile</scope>
</dependency>
```

在Spring环境下，Jedis可以类比成一个JDBC的Connection，你可以通过这个Connection对Redis进行各种操作，那么就很容易写出这样的代码。

首先进行配置，将Jedis配置为Bean，在构造对象时，提供redis的服务器地址和端口号。

![1](1.png)

使用的时候，注入Jedis的bean即可，随后就可以通过jedis对象操作redis了。

![2](2.png)

## SpringDataRedis

SpringDataRedis是SpringData这个大项目中的一个子项目，与Spring高度集成，是我们在Spring项目中整合Redis的不二之选。

使用SpringDataRedis，我们可以至少可以获得以下好处。

1. 自由选择Redis连接器，比如jedis、jredis、lettuce
2. 通过预先配置的RedisTemplate，直接进行Redis操作，免去了创建、释放连接的过程，与JdbcTemplate作用类似
3. 可以自由选择数据序列化的方式
4. 提供了对SpringCache整合Redis的支持，后面会讲到

那么Jedis和SpringDataRedis的关系也就明朗了，SpringDataRedis底层使用的是redis client，比如jedis、jredis、lettuce这样的，即SpringDataRedis是对jedis这种连接器的一种封装。

### SpringDataRedis的依赖

SpringDataRedis可以通过以下的SpringBoot起步依赖引入。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

SpringDataRedis的官方文档上介绍的引入方式是这种。

```xml
<dependency>
  <groupId>org.springframework.data</groupId>
  <artifactId>spring-data-redis</artifactId>
  <version>2.3.6.RELEASE</version>
</dependency>
```

这两种方式有什么不一样？很简单，看一下spring-boot-starter-data-redis是怎么构成的。

```xml
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>2.3.7.RELEASE</version>
    <scope>compile</scope>
  </dependency>
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>2.3.6.RELEASE</version>
    <scope>compile</scope>
  </dependency>
  <dependency>
    <groupId>io.lettuce</groupId>
    <artifactId>lettuce-core</artifactId>
    <version>5.3.5.RELEASE</version>
    <scope>compile</scope>
  </dependency>
</dependencies>
```

实际上就是spring-boot-starter、spring-data-redis和lettuce-core这三个依赖组成的，同时也可以发现，SpringDataRedis默认使用的是lettuce作为连接器。

### SpringDataRedis的使用

#### 连接到Redis

与Jedis类似，我们需要提供一个预设的连接配置。在SpringDataRedis中，负责连接到Redis的主要有两个类，RedisConnection和RedisConnectionFactory，从名字就可以看出，我们实际上是对RedisConnectionFactory提供配置，这个Factory根据我们提供的Redis连接配置，生产RedisConnection。

SpringDataRedis默认选择的Redis连接器是Lettuce，这里需要注意一下，虽然Java有多种Redis连接器，但并非所有的连接器都支持Redis的所有特性，具体可以参考SpringDataRedis官方文档的[连接](https://docs.spring.io/spring-data/redis/docs/2.4.6/reference/html/#redis:connectors:connection)。

配置一个Lettuce实现的RedisConnectionFactory，如下所示。

```java
@Configuration
class RedisConfig {

  @Bean
  public LettuceConnectionFactory redisConnectionFactory() {

    return new LettuceConnectionFactory(new RedisStandaloneConfiguration("localhost", 6379));
  }
}
```

#### 使用RedisTemplate

RedisTemplate是SpringDataRedis的核心组件，它高度封装的特性可以让我们不需要关心序列化的过程而直接将Java数据类型写入Redis。

虽然RedisConnection也可以操作Redis，但相比与RedisTemplate，它们操作数据的层次不一样，RedisConnection提供偏向底层的Redis操作，即直接操作二进制数据；而RedisTemplate处于一个高层的位置，序列化Java对象后调用RedisConnection写入Redis。

![3](3.png)

可以看到，上面用了RedisTempalte的opsForValue方法，实际上，它还能使用其他的operation，以下摘自SpringDataRedis官方文档。

| Operation               | 描述                                    |
| ----------------------- | --------------------------------------- |
| `GeoOperations`         | 操作geo类型, 比如`GEOADD`, `GEORADIUS`  |
| `HashOperations`        | 操作hash类型                            |
| `HyperLogLogOperations` | 操作HyperLogLog, 比如`PFADD`, `PFCOUNT` |
| `ListOperations`        | 操作list类型                            |
| `SetOperations`         | 操作set类型                             |
| `ValueOperations`       | 操作一般的键值对类型，即字符串          |
| `ZSetOperations`        | 操作zset类型                            |

除了以上这些Operation，为了方便使用，还可以将一个特定的key绑定到Operation上，比如下面这样，如此一来，这个Operation专门就是为mykey这个键进行服务的了。

![4](4.png)

#### RedisTemplate的序列化机制

使用RedisTemplate时，默认使用的是jdk序列化，即如果你想存一个自定义对象，那么应该让其实现Serializable接口，否则jdk是无法进行序列化的。

如果不想使用SpringBoot预配置的RedisTemplate，那么我们可以自己决定序列化方式。

下面是几种常见的序列化器。

* JdkSerializationRedisSerializer：基于Jdk的序列化器，RedisTemplate默认使用的
* StringRedisSerializer：将key或者value序列化成字符串，存进redis时是人可读的
* Jackson2JsonRedisSerializer：将对象序列化为json格式

在配置RedisTemplate时，可以自行设置序列化方式。

![5](5.png)

上面的例子中，设置了普通的键和hash类型的键使用StringRedisSerializer，普通的value和hash的value使用Jackson2JsonRedisSerializer，上面这个例子中对json序列化过程也进行了一定的定制，即设置ObjectMapper，设置对象映射可见性为所有，其他的取值可以参照下图。

![6](6.png)

activateDefaultTyping方法则是设置了序列化过程中，将对象中的所有字段类型也一并作为信息写入。

RedisTemplate的afterPropertiesSet可以不调用，如果对其源码感兴趣可以自行阅读，这里就直接说了，它可以检查RedisTemplate中的key和value的序列化器是否都已设置妥当，若没有设置，则启用默认的序列化器，即Jdk序列化器。

# Redis作为缓存使用

施工中

# 参考文章

[spring-data-redis官方文档]()

[spring-boot官方文档--caching](https://docs.spring.io/spring-boot/docs/2.3.7.RELEASE/reference/htmlsingle/#boot-features-caching)

[spring-framework官方文档--cache abstraction](https://docs.spring.io/spring-framework/docs/current/reference/html/integration.html#cache)

[spring-data-redis与jedis的区别](https://blog.csdn.net/houpeibin2012/article/details/104364826)


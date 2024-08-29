# Spring Data Redis

在 java 程序中操作 Redis 需要使用 Redis 的 Java 客户端，就如同 Java 操作 MySQL 需要使用 JDBC。

Redis 的 Java 客户端常用有以下几种：

- Jedis
- Lettuce
- Spring Data Redis

Spring 对 Redis 客户端进行了整合，提供了 Spring Data Redis，在 Spring Boot 项目中还提供了对应的 Starter，即 spring-boot-starter-data-redis 。

Spring Data Redis中提供了一个高度封装的类：**RedisTemplate**，对相关 api 进行了归类封装，将同一类型操作封装为 operation 接口，具体分类如下：

- ValueOperations：string 数据操作
- SetOperations：set 类型数据操作
- ZSetOperations：zset 类型数据操作
- HashOperations：hash 类型的数据操作
- ListOperations：list 类型的数据操作

## 环境搭建

1. Starter 的 maven 坐标：

```xml
<dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

2. 配置 Redis 数据源

在 application.yml 中设置

```yaml
sky:   # 苍穹外卖项目名
  redis:
    host: localhost
    port: 6379
    password: 123456
    database: 10
```

其中：

database 指定使用 Redis 的哪个数据库。

Redis 服务启动后默认有 16 个数据库，编号从 0 到 15 。

3. 编写配置类，创建 RedisTemplate 对象

```java
package com.sky.config;

@Configuration
@Slf4j
public class RedisConfiguration {

    @Bean
    public RedisTemplate redisTemplate(RedisConnectionFactory redisConnectionFactory){
        log.info("开始创建redis模板对象...");
        RedisTemplate redisTemplate = new RedisTemplate();
        //设置redis的连接工厂对象
        redisTemplate.setConnectionFactory(redisConnectionFactory);
        //设置redis key的序列化器
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        return redisTemplate;
    }
}
```

注意：

- 当前配置类不是必须的，因为 Spring Boot 框架会自动装配 RedisTemplate 对象。
- 但是默认的 key 序列化器为 JdkSerializationRedisSerializer，导致存到 Redis 后的数据和原始数据有差别，故设置为 StringRedisSerializer 序列化器。

## 操作方式

- 通用指令操作

	注入 RedisTemplate 对象即可操作

```java
    @Autowired
    private RedisTemplate redisTemplate;
```

- 针对各数据类型的操作

	获取对应的操作类（Operations），然后使用操作类的函数即可。

```java
//string数据操作
ValueOperations valueOperations = redisTemplate.opsForValue();

//hash类型的数据操作
HashOperations hashOperations = redisTemplate.opsForHash();

//list类型的数据操作
ListOperations listOperations = redisTemplate.opsForList();

//set类型数据操作
SetOperations setOperations = redisTemplate.opsForSet();

//zset类型数据操作
ZSetOperations zSetOperations = redisTemplate.opsForZSet();
```


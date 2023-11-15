# Spring 中使用 Redis

## Spring Data Redis

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

### 环境搭建

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
- 但是默认的 key 序列化器为 JdkSerializationRedisSerializer，导致我们存到 Redis 后的数据和原始数据有差别，故设置为 StringRedisSerializer 序列化器。

### 操作方式

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

## Spring Cache

Spring Cache 是一个框架，实现了基于注解的缓存功能。

Spring Cache 提供了一层抽象，底层可以切换不同的缓存实现，例如：

- EHCache
- Caffeine
- Redis(常用)

### 起步依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>  		            		       	 <version>2.7.3</version> 
</dependency>
```

### 基本注解

| **注解**       | **说明**                                                     |
| -------------- | ------------------------------------------------------------ |
| @EnableCaching | 开启缓存注解功能，通常加在启动类上                           |
| @CachePut      | 将方法的返回值放到缓存中                                     |
| @Cacheable     | 在方法执行前先查询缓存中是否有数据，如果有数据，则直接返回缓存数据；如果没有缓存数据，调用方法并将方法返回值放到缓存中 |
| @CacheEvict    | 方法执行完后清理缓存，将一条或多条数据从缓存中删除           |

注解含有以下属性：

- cacheNames

	指定在 redis 中的 key 的前缀。

- key

	指定在 redis 中的 key 的后缀。（支持 SpEL 语法）
	
	- `#variable.attr`
	
		表示某个形参的 attr 属性。
	
	- `#result.attr`
	
		表示方法返回值的 attr 属性。
	
	- `p0.attr`
	
		表示方法第一个参数的 attr 属性。
	
	- `a0.attr`
	
		表示方法第一个参数的 attr 属性。
	
	- `#root.args[0].attr`
	
		表示方法第一个参数的 attr 属性。

### 示例

#### @CachePut

将方法的返回值，放入缓存。

```java

	/**
	* CachePut：将方法返回值放入缓存
	* cacheNames：缓存的名称，每个缓存名称下面可以有多个 key
	* key：缓存的 key
	*/
	@PostMapping
    @CachePut(cacheNames = "userCache", key = "#user.id")
						// id = 1 时生成的 redis 中的 key 为：userCache::1
						// 会自动获取生成的主键值
    public User save(@RequestBody User user){
        userMapper.insert(user);
        return user;
    }
```

#### @Cacheable

在方法执行前，先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，调用方法并将方法返回值放到缓存中。

```java
	/**
	* Cacheable：在方法执行前spring先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，	  *调用方法并将方法返回值放到缓存中
	* value：缓存的名称，每个缓存名称下面可以有多个key
	* key：缓存的key
	*/
	@GetMapping
    @Cacheable(cacheNames = "userCache",key="#id")
    public User getById(Long id){
        User user = userMapper.getById(id);
        return user;
    }
```

#### @CacheEvict

清理指定缓存。

```java
	@DeleteMapping
    @CacheEvict(cacheNames = "userCache",key = "#id")// 删除 userCache::id 对应的缓存数据
    public void deleteById(Long id){
        userMapper.deleteById(id);
    }

	@DeleteMapping("/delAll")
    @CacheEvict(cacheNames = "userCache",allEntries = true)// 删除 userCache 下所有的缓存数据
    public void deleteAll(){
        userMapper.deleteAll();
    }
```


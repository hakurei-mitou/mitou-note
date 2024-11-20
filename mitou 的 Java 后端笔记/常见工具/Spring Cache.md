# Spring Cache

Spring Cache 是一个框架，实现了基于注解的缓存功能。

Spring Cache 提供了一层抽象，底层可以切换不同的缓存实现，例如：

- EHCache
- Caffeine
- Redis(常用)

## 起步依赖

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-cache</artifactId>  		            		       	 <version>2.7.3</version> 
</dependency>
```

## 基本注解

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

## 示例

### @CachePut

将方法的返回值，放入缓存。

```java
	/**
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

### @Cacheable

在方法执行前，先查看缓存中是否有数据，如果有数据，则直接返回缓存数据；若没有数据，调用方法并将方法返回值放到缓存中。

```java
	/**
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

### @CacheEvict

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


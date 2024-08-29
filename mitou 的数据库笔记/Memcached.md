# Memcached

Memcached 是基于内存的 key-value 存储形式的分布式对象缓存系统。

## 与 Redis 的对比

数据操作：

- Memcached 只支持简单的 key-value 存储，不支持枚举，不支持持久化和复制等功能。
- Redis 拥有更多的数据结构和并支持更丰富的数据操作，支持 list、set、sorted set、hash 等数据结构，还提供了持久化和复制等功能。

内存管理机制：

- Memcached 的数据一直存储在内存中。
- Redis 会将一些很久没用到的 value 交换到磁盘。（但全部的 key 都在内存中）

持久性：

- Memcache不支持数据持久存储。
- Redis 支持持久化存储，可以将内存中的数据保持在磁盘中，重启的时候可以再次加载进行使用。

分布式：

- Memcache 可以手动使用一致性 hash 做分布式。
- Redis 支持 master-slave 复制模式。


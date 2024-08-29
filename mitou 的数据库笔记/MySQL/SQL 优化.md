# SQL 优化

## SQL 性能分析

### SQL 执行频率

可根据增删改查的执行频率来决定优化。

- 查看当前数据库的各指令访问频率

	`show [globe | session] status like 'Com_______';`，like 后是模糊匹配，Com 后有七个下划线，匹配七个字符。

### 慢查询日志

慢查询日志记录了所有**执行时间超过指定参数**( long_query_time，单位:秒，默认10秒）的所有 SQL 语句的日志。
MySQL 的慢查询日志**默认关闭**，需要在 MySQL 的配置文件 (/etc/my.cnf）中配置如下信息：

```sql
# 开启 MySQL 慢日志查询开关
slow_query_log = 1

# 设置慢日志的时间为 2 秒，SQL 语句执行时间超过 2 秒，就会视为慢查询，记录慢查询日志
long_query_time = 2

# 设置完毕需重启
```

查看慢日志文件：/var/lib/mysql/localhost-slow.log

### profile 详情

profile 详情展示数据库语句的时间耗费情况。默认关闭。

- 查看当前 MySQL 是否支持 profile 操作

	`select @@have_profiling;`

- 开启 profiling

	`set [globe | session] profiling = 1;`

```sql
# 查看每一条 SQl 的耗时情况
show profiles;

# 查看指定 id 的 SQL 语句各个阶段的耗时情况
show profile for query query_id;

# 查看指定 id 的 SQL 语句的 CPU 使用情况
show profile cpu for query query_id;
```

### explain 执行计划

获取 MySQL 如何执行 select 语句的信息，包括查询过程中表如何连接以及顺序等信息。

```sql
# 直接在 select 语句之前加上关键字 explain 或 desc
explain select 字段列表 from 表名 where 条件;
```

展示结果：

- id

	表示 select 子句的执行顺序

	- 若 id 值不同，值越大，越先执行；
	- 若 id 值相同，则从上往下执行。

- type

	表示连接类型，性能由好到差为：NULL、system、const、eq_ref、ref、range、index、all 。

- key_len

	涉及到的各类索引长度的总长度。

- filtered

	表示返回结果的行数占需读取行数的百分比，filtered 的值越大越好。

##   语句优化

除了建立合适的索引外，还要尽量编写能够命中索引的 SQL 语句。

### insert 优化

- 批量插入

	每条 insert 都要重新与数据库建立连接。建议每条 insert 可以插入 500 ~ 600 条数据。

- 手动提交事务

- 主键顺序插入

	见页分裂。

- 大批量数据插入

	大批量数据 insert 语句的性能较低，建议使用 `load` 指令。

	```sql
	# 1.客户端连接服务端时，加上参数 --local-infile
	mysql --local-infile -u root -p
	
	# 2.设置全局参数 local_infile 为 1，开启从本地加载文件导入数据的开关
	set global local_infile = 1;
	
	# 3. 执行load指令将准备好的数据，加载到表结构中
	load data local infile '/root/data.txt' into table tb_user fields terminated by ',' lines terminated by '\n';
	# 上为字段间以逗号分隔，行间以换行符分隔。
	```

### 主键优化

- 数据组织方式

	InnoDB 中表数据按主键顺序存放，这种存储方式叫索引组织表（index organized table，IOT）

- 页分裂

	主键乱序插入会导致存储 B-Tree 结点数据的页分裂，使插入效率降低。

- 叶合并

	删除导致的 B-Tree 的结点合并，可节约空间。

- 主键设计原则

	- 尽量减少主键长度。
	- 插入数据时，尽量顺序插入，选择使用 `auto_increment` 自增主键。
	- 尽量不要使用通用唯一识别码（(Universally unique identifier)， UUID）和自然主键做主键，如身份证号。（过长，也容易乱序插入）
	- 业务操作时，避免对主键的修改。

### order by 优化

- using filesort 方式

	通过索引或全表扫描读取行，在排序缓冲区排序。

- using index 方式

	直接通过有序索引返回有序数据，效率高。对要排序的字段建立联合索引（索引字段升序或降序可以指定），并采用覆盖索引访问。

### group by 优化

- 通过建立联合索引优化。
- 使用时要满足最左前缀法则。

### limit 优化

- 若在需排序情况下使用 limit，且 limit 指定的偏移量较大，效率非常低。

	创建覆盖索引，先查询所需数据的主键，然后用该主键作为子查询得到最终数据。

	```sql
	explain select * from tb_sku t, (select id from tb_sku order by id limit 2000000,10) a where t.id = a.id;
	```

### count 优化

- 还没有较好的优化方式，自行设置（如在 redis 中）变量记录数量，并维护。

- count 的用法

	- `count(主键)`

		InnoDB 遍历整张表，取出主键值，返回给服务层，然后直接计数（主键不可能为 `null`，不必判断）。（属于 `count(字段)`方式）

	- `count(字段)`

		若没有 `not null` 约束，则取出字段返回服务层后，判断再计数；若有 `not null` 约束，则不判断。

	- `count(1)`

		InnoDB 遍历整张表，不取值，服务层按字段值为 `1` 直接计数。（注意，字段计数只与空或非空有关，`count(2)` 结果与 `count(1)` 也是一致的）

	- `count(*)`

		InnoDB 不取值，系统专门做了优化，服务层直接按行计数，效率最高，但仍然要遍历整张表。

### update 优化

- 使用 update 时尽量依据索引字段更新。

	- 有索引

		系统添加行锁，其余行仍可被其余进程修改。

	- 无索引

		系统添加表锁，整张表锁住。

## 重构查询方式

- 切分大查询

	一个大查询如果一次性执行的话，可能一次锁住很多数据、占满整个事务日志、耗尽系统资源、阻塞很多小的但重要的查询。可以拆分成多次。

- 分解大连接查询

	将一个大连接查询分解成对每个表进行一次单表查询，然后将结果在应用程序中进行关联。

	- 让缓存更高效。对于连接查询，如果其中一个表发生变化，那么整个查询缓存就无法使用。而分解后的多个查询，即使其中一个表发生变化，对其它表的查询缓存依然可以使用。
	- 分解成多个单表查询，这些单表查询的缓存结果更可能被其它查询使用到，从而减少冗余记录的查询。
	- 减少锁竞争。
	- 在应用层进行连接，可以更容易对数据库进行拆分，从而更容易做到高性能和可伸缩。
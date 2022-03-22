# SQLserver 系统内容

微软官方文档：https://docs.microsoft.com/zh-cn/sql/?view=sql-server-ver15

## 系统数据库

| 系统数据库                                                   | 描述                                                         |
| :----------------------------------------------------------- | ------------------------------------------------------------ |
| [master 数据库](https://docs.microsoft.com/zh-cn/sql/relational-databases/databases/master-database?view=sql-server-ver15) | 记录 SQL Server 实例的所有系统级信息。                       |
| [msdb 数据库](https://docs.microsoft.com/zh-cn/sql/relational-databases/databases/msdb-database?view=sql-server-ver15) | 用于 SQL Server 代理计划警报和作业。                         |
| [model 数据库](https://docs.microsoft.com/zh-cn/sql/relational-databases/databases/model-database?view=sql-server-ver15) | 用作 SQL Server 实例上创建的所有数据库的模板。 对 **model** 数据库进行的修改（如数据库大小、排序规则、恢复模式和其他数据库选项）将应用于以后创建的所有数据库。 |
| [Resource 数据库](https://docs.microsoft.com/zh-cn/sql/relational-databases/databases/resource-database?view=sql-server-ver15) | 一个只读数据库，包含 SQL Server 包括的系统对象。 系统对象在物理上保留在 **Resource** 数据库中，但在逻辑上显示在每个数据库的 **sys** 架构中。 |
| [tempdb 数据库](https://docs.microsoft.com/zh-cn/sql/relational-databases/databases/tempdb-database?view=sql-server-ver15) | 一个工作空间，用于保存临时对象或中间结果集。                 |

## 数据页

SQL server 将页作为数据存储的基本单位

页的大小为 8 KB

- 页头
	每页开头是 96B 的页头，用于存储页的存储信息，其中有页码，页类型，页的可用空间以及拥有该页的对象的分配单元 id。

- 行偏移数组
	行偏移数组占用 36B，记录了每一页的行偏移量，同时记录了数据行的逻辑顺序

一个数据页实际存储数据量为：
1024 x 8 - 96(页头) - 36(行偏移数组) = 8060 B

![img](images/SQL Server 系统内容/clipboard.png)

数据页有多种类型，此处省略。

## DBCC 命令

一组 SQL server 提供的控制台命令。
一般不常用，以下仅列出常用命令。

### 帮助类命令

- dbcc help('?')
查询所有的 dbcc 命令

- dbcc help（‘< 命令 >’）
查询指定命令的语法说明

- dbcc useroptions
返回当前连接的活动（设置）的 set 选项

### 检查和验证类命令

- dbcc checkallog（'< 数据库名 >'）
检查指定数据库的磁盘空间分配结构的一致性

- dbcc checkcatalog（'< 数据库名 >'）
检查指定数据库的系统表内和系统表间的一致性

- dbcc checkconstaints（'< 表名 >'）
检查指定表上的指定约束或所有约束的完整性

- dbcc checkdb
检查数据库中所有对象的分配和结构完整性

- dbcc checkfilegroup 
检查指定文件组中所有表在当前数据库中的分配和结构完整性

- dbcc checktable
检查指定表或索引视图的数据、索引及 test、ntest 和 image 页的完整性

### 维护类命令

- dbcc dbreindex
重建在指定数据库的一个或多个索引

- dbcc indexdefrag
对表和视图上的索引和非聚集索引进行碎片处理

- dbcc pintable( 数据库 id，表 id )
将表数据驻留在内存中

- dbcc unpintable( 数据库 id，表 id )
撤销驻留在内存中的表

### 性能调节类命令

- dbcc dropcleanbuffers
从缓冲池中删除所有缓冲区

- dbcc freeproccache
从过程缓冲区删除所有元素

- dbcc inputbuffer
显示从客户机发送到服务器的最后一个语句

- dbcc show_statistics
显示指定表上的指定目标的当前统计分布信息

- dbcc showcontig
显示指定表的索引和碎片信息

## 官方使用DBCC的建议

1. 在系统使用率较低时运行 checkdb。
2. 请确保未同时执行其它磁盘 I/O 操作，例如磁盘备份。
3. 将 tempdb 放到单独的磁盘系统或快速磁盘子系统中。
4. 允许 tempdb 在驱动器上有足够的扩展空间。 使用带有 ESTIMATE ONLY 的 DBCC 估计 tempdb 将需要多少空间。
5. 避免运行占用大量 CPU 的查询或批处理作业。
6. 在 DBCC 命令运行时，减少活动事务。
7. 使用 NO_INFOMSGS 选项减少一些信息的输出。
8. 考虑使用带有 PHYSICAL_ONLY 选项的 dbcc checkdb 来检查页和记录的物理结构。
PHYSICAL_ONLY 选项：
只检查物理错误，不检查逻辑错误
物理错误比逻辑错误更严重，因为物理错误一般 SQLSERVER 都不能修复的，而逻辑错误大部分SQLSERVER 都可以修复
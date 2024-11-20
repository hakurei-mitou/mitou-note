# PageHelper

一个基于 Mybatis 的第三方分页查询插件。

统一抽象了分页查询操作，可以简化分页查询代码开发。

以分页查询员工列表为例。

1、在  pom.xml 引入依赖

```xml
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
    <version>1.4.2</version>
</dependency>
```

2、EmpMapper

```java
@Mapper
public interface EmpMapper {
    //获取当前页的结果列表
    @Select("select * from emp")
    public Page<Emp> page();
}
```

3、EmpServiceImpl

```java
@Override
public PageBean page(Integer page, Integer pageSize) {
    // 设置分页参数
    PageHelper.startPage(page, pageSize); 
    
    // 执行分页查询，获取分页结果
    Page<Emp> p = empMapper.page();
    
    // 封装 PageBean 类（一个 pojo 类，用来装数据）返回
    PageBean pageBean = new PageBean(p.getTotal(), p.getResult()); 
    return pageBean;
}
```

其中，在执行 `empMapper.page()` 方法时，只需要执行：`select  *  from  emp`语句。

分页插件会自动完成以下操作：

1. 先获取到要执行的SQL语句：`select  *  from  emp`
2. 把SQL语句中的字段列表，变为：`count(*)`
3. 执行SQL语句：`select  count(*)  from  emp`   ，获取到总记录数。
	- 用于校验查询参数是否符合数据状况。
	- 通常需要返回总条数或总页数信息。
4. 再对要执行的 SQL 语句：`select  *  from  emp` 进行改造，在末尾添加 `limit ? , ?` 生成 SQL 模版。
5. 执行生成的 SQL 模版：`select  *  from  emp  limit  ? , ? `

以上操作完成了两个功能：

- 获取总的数量条目数（`count(*)`）
- 查询指定分页（`limit`）。

若要指定某些字段，而不是全部字段（`* `），指定字段即可：

```java
@Mapper
public interface EmpMapper {
    //获取当前页的结果列表
    @Select("select name, gender from emp")   // 指定 name, gender
    public Page<Emp> page();
}
```


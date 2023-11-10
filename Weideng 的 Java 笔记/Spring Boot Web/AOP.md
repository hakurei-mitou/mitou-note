# AOP

## 概念

 AOP 是 spring 框架的第二大核心。

（Aspect Oriented Programming，AOP，面向切面编程，面向方面编程）

本质上就是面向特定方法编程。

假设要统计一些方法的运行时间：

![image-20231012171643273](images/AOP/image-20231012171643273.png)

如果在每个业务方法的开头结尾都添加代码会很麻烦，且难以维护：

![image-20231012171720113](images/AOP/image-20231012171720113.png)

AOP 可以在不改动这些原始方法的基础上，针对特定的方法进行功能的增强（无侵入性: 解耦）。

使用切面可以优雅地完成这个功能：

![image-20231012171815668](images/AOP/image-20231012171815668.png)

AOP 是 Spring 框架的高级技术，底层使用动态代理机制，实现在管理 bean 对象的过程中，对特定的方法进行编程(功能增强)。

AOP 常见的应用场景如下：

- 统计分析方法运行时间
- 记录系统的操作日志
- 权限控制
- 事务管理
	- Spring 事务管理的底层是通过 AOP 实现的。
	- 只要添加 @Transactional 注解之后，AOP 程序自动会在原始方法运行前开启事务，在原始方法运行完毕之后提交或回滚事务。

AOP 面向切面编程的一些优势：

- 代码无侵入

	没有修改原始的业务方法，就已经对原始的业务方法进行了功能的增强或者是功能的改变

- 减少了重复代码

- 提高开发效率

- 维护方便

AOP 是一种代码设计思想，可以动态代理、过滤器，拦截器等技术实现。

## 基本使用

导入依赖：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

定义统计方法时间的切面：

```java
@Component
@Aspect //当前类为切面类
@Slf4j
public class TimeAspect {

    @Around("execution(* com.itheima.service.*.*(..))") 
    public Object recordTime(ProceedingJoinPoint pjp) throws Throwable {
        //记录方法执行开始时间
        long begin = System.currentTimeMillis();

        //执行原始方法
        Object result = pjp.proceed();

        //记录方法执行结束时间
        long end = System.currentTimeMillis();

        //计算方法执行耗时
        log.info(pjp.getSignature()+"执行耗时: {}毫秒",end-begin);

        return result;
    }
}
```

## 核心概念

- JoinPoint（连接点）

	连接点指的是可以被 AOP 控制的方法，同时也封装了连接点方法在执行时的相关信息。

![image-20231012190540939](images/AOP/image-20231012190540939.png)

- PointCut（切入点）

	连接点的匹配条件。

![image-20231012190754459](images/AOP/image-20231012190754459.png)

- Advice（通知）

	AOP 中的那部分每个连接点上施加的重复的逻辑，也就是施加的共性的功能。

	- 通知仅会在切入点匹配的方法执行时被应用。

![image-20231012190559482](images/AOP/image-20231012190559482.png)

- Aspect（切面）

	描述通知与切入点的对应关系（通知+切入点）。

	- 通过切面就能够描述当前 aop 程序需要针对于哪个原始方法，在什么时候执行什么样的操作。
	- 切面所在的类，我们一般称为**切面类**（添加 @Aspect 注解的类）

![image-20231012190901886](images/AOP/image-20231012190901886.png)

- Target（目标对象）

	目标对象即通知所应用的对象。

Spring 的 AOP 基于动态代理技术实现，程序运行时会自动基于动态代理技术为目标对象生成一个对应的代理对象，在代理对象当中就会对目标对象当中的原始方法进行功能的增强：

![image-20231012191300076](images/AOP/image-20231012191300076.png)

## 通知类型

| 注解            | 名称       | 执行时刻                                                     |
| --------------- | ---------- | ------------------------------------------------------------ |
| @Around         | 环绕通知   | 此注解标注的通知方法在目标方法前、后都被执行                 |
| @Before         | 前置通知   | 此注解标注的通知方法在目标方法前被执行                       |
| @After          | 后置通知   | 此注解标注的通知方法在目标方法后被执行，无论是否有异常都会执行 |
| @AfterReturning | 返回后通知 | 此注解标注的通知方法在目标方法后被执行，有异常不会执行       |
| @AfterThrowing  | 异常后通知 | 此注解标注的通知方法发生异常后执行                           |

注意：

- @Around 环绕通知需要自己调用 ProceedingJoinPoint.proceed() 来让原始方法执行，其他通知不需要考虑目标方法执行
- @Around 环绕通知方法的返回值，必须指定为 Object，来接收原始方法的返回值，否则原始方法执行完毕，是获取不到返回值的。

注意：

- 考虑通知类型时要先分析需求，如果要求用到方法返回值，那么是在原始方法执行后才能获取到的。

通知示例代码：

```java
@Slf4j
@Component
@Aspect
public class MyAspect1 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(JoinPoint joinPoint){
        log.info("before ...");

    }

    //环绕通知
    @Around("execution(* com.itheima.service.*.*(..))")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        log.info("around before ...");

        //调用目标对象的原始方法执行
        Object result = proceedingJoinPoint.proceed();
        
        //原始方法如果执行时有异常，环绕通知中的后置代码不会在执行了
        
        log.info("around after ...");
        return result;
    }

    //后置通知
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(JoinPoint joinPoint){
        log.info("after ...");
    }

    //返回后通知（程序在正常执行的情况下，会执行的后置通知）
    @AfterReturning("execution(* com.itheima.service.*.*(..))")
    public void afterReturning(JoinPoint joinPoint){
        log.info("afterReturning ...");
    }

    //异常通知（程序在出现异常的情况下，执行的后置通知）
    @AfterThrowing("execution(* com.itheima.service.*.*(..))")
    public void afterThrowing(JoinPoint joinPoint){
        log.info("afterThrowing ...");
    }
}
```

其中，每一个注解里面都指定了切入点表达式，而且这些切入点表达式都一模一样。

存在了大量的重复性的切入点表达式时，可以使用抽取功能。

## 抽取

Spring 提供了 @PointCut 注解，作用是将公共的切入点表达式抽取出来，需要用到时引用该切入点表达式即可：

```java
@Slf4j
@Component
@Aspect
public class MyAspect1 {

    //切入点方法（公共的切入点表达式）
    @Pointcut("execution(* com.itheima.service.*.*(..))")
    private void pt(){

    }

    //前置通知（引用切入点）
    @Before("pt()")
    public void before(JoinPoint joinPoint){
        log.info("before ...");

    }

    //环绕通知
    @Around("pt()")
    public Object around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable {
        log.info("around before ...");

        //调用目标对象的原始方法执行
        Object result = proceedingJoinPoint.proceed();
        //原始方法在执行时：发生异常
        //后续代码不在执行

        log.info("around after ...");
        return result;
    }

    //后置通知
    @After("pt()")
    public void after(JoinPoint joinPoint){
        log.info("after ...");
    }

    //返回后通知（程序在正常执行的情况下，会执行的后置通知）
    @AfterReturning("pt()")
    public void afterReturning(JoinPoint joinPoint){
        log.info("afterReturning ...");
    }

    //异常通知（程序在出现异常的情况下，执行的后置通知）
    @AfterThrowing("pt()")
    public void afterThrowing(JoinPoint joinPoint){
        log.info("afterThrowing ...");
    }
}
```

其中，抽取相当于使用了一个额外的通知，即 `pt()` ，包装统一切入点信息，然后让其它通知方法匹配这个通知（`pt()`）。

注意：

- 当切入点方法使用 private 修饰时，仅能在当前切面类中引用该表达式。
- 当外部其他切面类中也要引用当前类中的切入点表达式，就需要把 private 改为 public 。

外部引用时的语法为：

```java
@Slf4j
@Component
@Aspect
public class MyAspect2 {
    //引用MyAspect1切面类中的切入点表达式
    @Before("com.itheima.aspect.MyAspect1.pt()")
    public void before(){
        log.info("MyAspect2 -> before ...");
    }
}
```

## 通知顺序

如果定义了多个切面类，而多个切面类中多个切入点都匹配到了同一个目标方法。

此时当目标方法在运行的时候，这多个切面类当中的这些通知方法都会运行。

它们的顺序依据：

- 默认依**切面类的类名**的字典序升序**嵌套执行**。
- 可以使用 Spring 提供的 @Order 注解控制通知执行的顺序。

@Order 使用示例：

```java
@Slf4j
@Component
@Aspect
@Order(2)  //切面类的执行顺序（前置通知：数字越小先执行; 后置通知：数字越小越后执行）
public class MyAspect2 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(){
        log.info("MyAspect2 -> before ...");
    }

    //后置通知 
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(){
        log.info("MyAspect2 -> after ...");
    }
}
```

```java
@Slf4j
@Component
@Aspect
@Order(3)  //切面类的执行顺序（前置通知：数字越小先执行; 后置通知：数字越小越后执行）
public class MyAspect3 {
    //前置通知
    @Before("execution(* com.itheima.service.*.*(..))")
    public void before(){
        log.info("MyAspect3 -> before ...");
    }

    //后置通知
    @After("execution(* com.itheima.service.*.*(..))")
    public void after(){
        log.info("MyAspect3 ->  after ...");
    }
}
```

## 切入点表达式

常见形式：

- execution(……)：根据方法的签名来匹配

![image-20231012200211448](images/AOP/image-20231012200211448.png)

- @annotation(……) ：根据注解匹配

![image-20231012200219579](images/AOP/image-20231012200219579.png)

### execution

execution 主要根据方法的返回值、包名、类名、方法名、方法参数等信息来匹配，语法为：

```
execution(访问修饰符  返回值  包名.类名.方法名(方法参数) throws 异常)
```

其中：

- 访问修饰符

	可省略（比如: public、protected）

- 包名.类名

	可省略

- throws 异常

	可省略（注意是方法上声明抛出的异常，不是实际抛出的异常）

可以使用通配符：

- `*` ：单个独立的任意符号，可以通配任意返回值、包名、类名、方法名、任意类型的一个参数，也可以通配包、类、方法名的一部分

- `..` ：多个连续的任意符号，可以通配任意层级的包，或任意类型、任意个数的参数

#### 示例

- 省略方法的修饰符号 

	~~~java
	execution(void com.itheima.service.impl.DeptServiceImpl.delete(java.lang.Integer))
	~~~

- 使用`*`代替返回值类型

	~~~java
	execution(* com.itheima.service.impl.DeptServiceImpl.delete(java.lang.Integer))
	~~~

- 使用`*`代替包名（一层包使用一个`*`）

	~~~java
	execution(* com.itheima.*.*.DeptServiceImpl.delete(java.lang.Integer))
	~~~

- 使用`..`省略包名

	~~~java
	execution(* com..DeptServiceImpl.delete(java.lang.Integer))    
	~~~

- 使用`*`代替类名

	~~~java
	execution(* com..*.delete(java.lang.Integer))   
	~~~

- 使用`*`代替方法名

	~~~java
	execution(* com..*.*(java.lang.Integer))   
	~~~

- 使用 `*` 代替参数

	```java
	execution(* com.itheima.service.impl.DeptServiceImpl.delete(*))
	```

- 使用`..`省略参数

	~~~java
	execution(* com..*.*(..))
	~~~

- 根据业务需要，可以使用 且（&&）、或（||）、非（!） 来组合比较复杂的切入点表达式。

	```java
	execution(* com.itheima.service.DeptService.list(..)) || execution(* com.itheima.service.DeptService.delete(..))
	```


#### 书写建议

- 所有业务方法名在命名时尽量规范，方便切入点表达式快速匹配。

	如：查询类方法都是 find 开头，更新类方法都是update开头

	~~~java
	//业务类
	@Service
	public class DeptServiceImpl implements DeptService {
	    
	    public List<Dept> findAllDept() {
	       //省略代码...
	    }
	    
	    public Dept findDeptById(Integer id) {
	       //省略代码...
	    }
	    
	    public void updateDeptById(Integer id) {
	       //省略代码...
	    }
	    
	    public void updateDeptByMoreCondition(Dept dept) {
	       //省略代码...
	    }
	    //其他代码...
	}
	~~~

	~~~java
	//匹配DeptServiceImpl类中以find开头的方法
	execution(* com.itheima.service.impl.DeptServiceImpl.find*(..))
	~~~

- 描述切入点方法通常基于接口描述，而不是直接描述实现类，增强拓展性

	~~~java
	execution(* com.itheima.service.DeptService.*(..))
	~~~

- 在满足业务需要的前提下，尽量缩小切入点的匹配范围。

	如：包名匹配尽量不使用 ..，使用 * 匹配单个包

	~~~java
	execution(* com.itheima.*.*.DeptServiceImpl.find*(..))
	~~~


### @annotation

如果要匹配多个无规则的方法，比如：list() 和 delete() 这两个方法，execution 就不方便了。

可以使用 @annotation 进行匹配。

两种用法：

- 匹配到被 @annotation 指定的注解标注的方法（标注为类则是类中的全部方法）。
- 自定义注解，然后用 @annotation 。

#### 示例

自定义注解：MyLog

~~~java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
public @interface MyLog {
}
~~~

将 MyLog 标注到指定方法上。

切面类：

```java
@Slf4j
@Component
@Aspect
public class MyAspect6 {
    //针对list方法、delete方法进行前置通知和后置通知

    //前置通知
    @Before("@annotation(com.itheima.anno.MyLog)")
    public void before(){
        log.info("MyAspect6 -> before ...");
    }

    //后置通知
    @After("@annotation(com.itheima.anno.MyLog)")
    public void after(){
        log.info("MyAspect6 -> after ...");
    }
}
```

## 连接点

在 Spring 中用 JoinPoint 抽象了连接点，用它可以获得方法执行时的相关信息，如目标类名、方法名、方法参数等。

- 对于@Around通知，获取连接点信息只能使用 ProceedingJoinPoint 类型。

- 对于其他四种通知，获取连接点信息只能使用 JoinPoint，它是 ProceedingJoinPoint 的父类型。


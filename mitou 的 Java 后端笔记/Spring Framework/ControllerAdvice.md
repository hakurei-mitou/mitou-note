# ControllerAdvice

@ControllerAdvice（Controller 增强器）用于为 Controller 添加 Advice，即增强 Controller 的功能。

ControllerAdvice 的 Advice 不是使用 AOP 织入业务逻辑，而是在原生编码上提供了 Advice 支持。

## 基本功能

对 controller中被 @RequestMapping 注解的方法进行一些功能增强。

对类注解了 @ControllerAdvice 后，其方法可以使用如下注解：

- @ExceptionHandler

  全局异常处理，统一处理 Controller 抛出的异常。

- @ModelAttribute

  全局数据绑定，绑定键值对到 Model 中，全局的 @RequestMapping 都能获得在此处设置的键值对 。

- @InitBinder

	全局数据预处理，控制 WebDataBinder 绑定前台请求参数到 Model 中的行为。

## 全局异常处理

当没有做任何的异常处理时，三层架构处理异常的方案：

- Mapper 接口在操作数据库的时候出错了，此时异常会往上抛(谁调用Mapper就抛给谁)，会抛给 service。 
- service 中也存在异常了，会抛给 controller 。
- 而在 controller 当中，如果也没有做任何的异常处理，异常会再往上抛。最终抛给框架之后，框架就会返回一个 JSON 格式的数据，里面封装的就是错误的信息，但是框架返回的 JSON 格式的数据并不符合我们的开发规范。

那么在三层构架项目中，出现了异常，该如何处理?

- 方案一

	在所有 Controller 的所有方法中进行 try…catch 处理

	- 缺点：代码臃肿（不推荐）

- 方案二

	全局异常处理器

	- 好处：简单、优雅（推荐）

![image-20231012102247296](images/ControllerAdvice/image-20231012102247296.png)

定义全局异常处理器：

```java
@RestControllerAdvice
public class GlobalExceptionHandlerByControllerAdvicer {

    //处理异常
    @ExceptionHandler(Exception.class) //指定能够处理的异常类型
    public Result processException(Exception e){
        e.printStackTrace();//打印堆栈中的异常信息

        //捕获到异常之后，响应一个标准的Result
        return Result.error("对不起,操作失败,请联系管理员");
    }
}
```

其中：

- 类上 @RestControllerAdvice 注解

  表示定义了一个 ControllerAdvice，使系统异常可被抛入到此。

- 方法上加 @ExceptionHandler 注解

  通过 @ExceptionHandler 注解的 value 属性指定需要捕获的异常类型。

- @RestControllerAdvice = @ControllerAdvice + @ResponseBody

  处理异常的方法的返回值会转换为 json 后再响应给前端

## 全局数据绑定

全局数据绑定可以定义一些公共的数据，这些数据可以在所有 Controller 类的标注了 RequestMapping 的方法中使用。

@ModelAttribute 标注的方法的返回值会被作为全局数据存储在 Model 类中，默认情况下，它的 key 就是返回的变量名，value 就是方法返回值。

定义方式：

```java
@ControllerAdvice
public class MyGlobalDataHandler {
    
    @ModelAttribute(name = "md")
    public Map<String,Object> getGlobalData() {
        HashMap<String, Object> map = new HashMap<>();
        map.put("age", 99);
        map.put("gender", "男");
        return map;
    }
}
```

获取方式：

```java
@RestController
public class ControllerAdviceController {

    @GetMapping("/global-data")
    public String showGlobalData(Model model) {   // 使用 Model 类
        Map<String, Object> map = model.asMap();
        return map.toString();
    }
    
    @GetMapping("/global-data2")
    public String showGlobalData(@ModelAttribute("key") Value value) {   // 直接指定参数 key
        
        return value.toString();
    }
    
}
```

实际上，Model 类的数据也可以直接在代码中使用 Model 类的方法进行添加。

## 全局数据预处理

当有两个实体类：

```java
public class Book {
    private String name;
    private Long price;
}

public class Author {
    private String name;
    private Long age;
}
```

有一个数据添加功能的接口：

```java
@PostMapping("/book")
public void addBook(Book book, Author author) {
    System.out.println(book);
    System.out.println(author);
}
```

其中，两个实体类都有一个 name 属性，从前端传递时 ，会无法区分。

服务端通过 Request 的 getParameter 方法取得字符串形式的参数。

spring 使用 WebDataBinder 将这些字符串形式的参数转换为真正需要的类型。

可以修改这个转换过程：

1. 给接口中的变量取别名:

```java
@PostMapping("/book")
public void addBook(@ModelAttribute("b") Book book, @ModelAttribute("a") Author author) {   // 指定参数解析时的前缀
    System.out.println(book);
    System.out.println(author);
}
```

2. 进行请求数据预处理，在 @ControllerAdvice 标记的类中添加如下代码:

```java
@InitBinder("b")   // 对带有前缀 b 的参数使用下列操作
public void addPrefixb(WebDataBinder binder) {
    binder.setFieldDefaultPrefix("b.");
}

@InitBinder("a")   // 对带有前缀 a 的参数使用下列操作
public void addPrefixa(WebDataBinder binder) {
    binder.setFieldDefaultPrefix("a.");
}
```

3. 发送的请求需要指定前缀用于区分

```java
http://localhost:8080/book?b.name=xxx&b.price=23&a.name=yyy&a.age=18
```

注意：该前缀和后缀只在前端参数转换过程中生效，在进入后端实体类后，字段名并没有前缀。

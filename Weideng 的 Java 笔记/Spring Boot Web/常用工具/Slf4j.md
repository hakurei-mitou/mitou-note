# Slf4j

java 的一个日志框架。

程序入口类：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

/**
 * slf4j demo
 */
public class Demo1 {
    final static Logger logger = LoggerFactory.getLogger(Demo1.class);

    public static void main(String[] args) {
        logger.info("Hello world!");
    }
}
```

如果在类上添加 @slf4j 注解，会自动生成 Logger 对象，对象名：`log`，从而可以直接在方法体内使用 `log.` 调用方法：

```java
import lombok.extern.slf4j.Slf4j;

/**
 * @slf4j demo
 */
@Slf4j
public class Demo1 {

    public static void main(String[] args) {
        log.info("Hello world!");
    }
}

```


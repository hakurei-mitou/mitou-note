# Spring Validation

Java API 规范（JSR303）定义了 Bean 校验的标准 validation-api，但没有提供实现。

hibernate validation 实现了该规范，并增加了一些校验注解。

Spring Validation 是对 hibernate validation 的二次封装，用于支持 spring mvc 参数自动校验。

## 依赖

如果`spring-boot`版本小于`2.3.x`，`spring-boot-starter-web`会自动传入`hibernate-validator`依赖。

如果`spring-boot`版本大于`2.3.x`，则需要手动引入依赖：

```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
</dependency>
```

## 基本使用

Web 服务一般需要在 Controller 层对请求的参数做非法校验，请求的参数主要分为两种情况：

- `POST`、`PUT`请求，使用`requestBody`传递参数；
- `GET`请求，使用`requestParam/PathVariable`传递参数。

### requestBody 参数校验

一般见于 POST 和 PUT 请求。

需要：

1. 在 DTO 的字段声明校验注解。
2. 在方法参数声明校验注解。（这种情况下，使用`@Valid`和 `@Validated` 都可以）

如果校验失败，会抛出`MethodArgumentNotValidException`异常，`Spring`默认会将其转为`400（Bad Request）`请求。

```java
// DTO

@Data
public class UserDTO {

    private Long userId;

    @NotNull
    @Length(min = 2, max = 10)
    private String userName;

    @NotNull
    @Length(min = 6, max = 20)
    private String account;

    @NotNull
    @Length(min = 6, max = 20)
    private String password;
}

// 方法

@PostMapping("/save")
public Result saveUser(@RequestBody @Validated UserDTO userDTO) {
    // 校验通过，才会执行业务逻辑处理
    return Result.ok();
}
```

### requestParam/PathVariable 参数校验

一般见于 GET 请求。

需要：

1. 在类上标注 `@Validated` 注解。
2. 在方法入参上声明校验注解。

如果校验失败，会抛出`ConstraintViolationException`异常。

```java
@RequestMapping("/api/user")
@RestController
@Validated   // 类上标注
public class UserController {
    // 路径变量
    @GetMapping("{userId}")
    public Result detail(@PathVariable("userId") @Min(10000000000000000L) Long userId) {  // 方法入参上标注
        // 校验通过，才会执行业务逻辑处理
        return Result.ok(userDTO);
    }

    // 查询参数
    @GetMapping("getByAccount")
    public Result getByAccount(@Length(min = 6, max = 20) @NotNull String  account) {
        // 校验通过，才会执行业务逻辑处理
        return Result.ok(userDTO);
    }
}
```

### 统一异常处理

如果校验失败，会抛出`MethodArgumentNotValidException`或者`ConstraintViolationException`异常。

在实际项目开发中，通常会用统一异常处理来返回一个更友好的提示。

假设系统要求无论发送什么异常，`http`的状态码必须返回`200`，由业务码去区分系统的异常情况：

```java
@RestControllerAdvice
public class CommonExceptionHandler {

    @ExceptionHandler({MethodArgumentNotValidException.class})   // MethodArgumentNotValidException
    @ResponseStatus(HttpStatus.OK)   // 状态码 200
    @ResponseBody
    public Result handleMethodArgumentNotValidException(MethodArgumentNotValidException ex) {
        BindingResult bindingResult = ex.getBindingResult();
        StringBuilder sb = new StringBuilder("校验失败:");
        for (FieldError fieldError : bindingResult.getFieldErrors()) {
            sb.append(fieldError.getField()).append("：").append(fieldError.getDefaultMessage()).append(", ");
        }
        String msg = sb.toString();
        // 业务码
       return Result.fail(BusinessCode.参数校验失败, msg);
    }

    
    @ExceptionHandler({ConstraintViolationException.class})   // ConstraintViolationException
    @ResponseStatus(HttpStatus.OK)   // 状态码 200
    @ResponseBody
    public Result handleConstraintViolationException(ConstraintViolationException ex) {
        // 业务码
        return Result.fail(BusinessCode.参数校验失败, ex.getMessage());
    }
}
```

### 校验注解一览

Spring Validation 预制可用的校验注解有：

| 注解             | 校验功能                           |
| ---------------- | ---------------------------------- |
| @AssertFalse     | 必须是false                        |
| @AssertTrue      | 必须是true                         |
| @DecimalMax      | 小于等于给定的值                   |
| @DecimalMin      | 大于等于给定的值                   |
| @Digits          | 可设定最大整数位数和最大小数位数   |
| @Email           | 校验是否符合Email格式              |
| @Future          | 必须是将来的时间                   |
| @FutureOrPresent | 当前或将来时间                     |
| @Max             | 最大值                             |
| @Min             | 最小值                             |
| @Negative        | 负数（不包括0）                    |
| @NegativeOrZero  | 负数或0                            |
| @NotBlank        | 不为null并且包含至少一个非空白字符 |
| @NotEmpty        | 不为null并且不为空                 |
| @NotNull         | 不为null                           |
| @Null            | 为null                             |
| @Past            | 必须是过去的时间                   |
| @PastOrPresent   | 必须是过去的时间，包含现在         |
| @Pattern         | 必须满足正则表达式                 |
| @PositiveOrZero  | 正数或0                            |
| @Size            | 校验容器的元素个数                 |

### 分组校验

有时，一个 DTO 在不同方法中需要的校验规则是不一样的，可以使用分组校验。

步骤：

1. 定义分组（使用接口表示分组）。
2. 校验注解上声明 group 。
3. @Validated 注解上指定分组。

定义分组：

```java
import jakarta.validation.groups.Default;   // 继承该类后、，在 DTO 中指定所有组时可以不用写 groups 属性。

public class ValidationGroup {

    // 定义 Update 分组
    public interface Update extends Default {

    }

    // 定义 Save 分组
    public interface Save extends Default {

    }

}
```

DTO：

```java
// DTO 内直接导入接口类，这样可以不用重复声明 ValidationGroup
import com.example.learn_spring.Group.ValidationGroup.Save;
import com.example.learn_spring.Group.ValidationGroup.Update;

public class UserDTO {

    @NotNull()   // 默认所有分组
    @Length(min = 2, max = 7, groups = {Save.class, Update.class})  // 指定多个分组
    private String name;

    @NotNull(groups = Save.class) // 指定一个分组
    @Length(min = 6, max = 12, groups = Update.class)
    private String password;
}
```

方法：

```java
import com.example.learn_spring.Group.ValidationGroup;

// 指定分组

@PostMapping("/save")
public Result saveUser(@RequestBody @Validated(ValidationGroup.Save.class) UserDTO userDTO) {
    // 校验通过，才会执行业务逻辑处理
    return Result.ok();
}

@PostMapping("/update")
public Result updateUser(@RequestBody @Validated(ValidationGroup.Update.class) UserDTO userDTO) {
    // 校验通过，才会执行业务逻辑处理
    return Result.ok();
}
```

### 递归校验

如果 DTO 中某个字段是个对象，可以使用递归校验去校验该对象内部的字段值。

```java
@Data
public class UserDTO {

    @NotNull(groups = {Save.class, Update.class})
    @Length(min = 2, max = 10, groups = {Save.class, Update.class})
    private String userName;

    @NotNull(groups = {Save.class, Update.class})
    @Valid   // 在字段对象上使用 @Valid 进行递归校验
    private Job job;   // 字段为一个对象

    @Data
    public static class Job {

        @Min(value = 1, groups = Update.class)
        private Long jobId;

        @NotNull(groups = {Save.class, Update.class})
        @Length(min = 2, max = 10, groups = {Save.class, Update.class})
        private String jobName;
    }
}
```

### 集合校验

如果参数有集合，可以对集合元素使用校验注解：

```java
public class DTO {

    private List<@Min(value = 1) Integer> arr;

}
```

### @Valid 和 @Validated 区别

| 区别         | @Valid                                          | @Validated              |
| ------------ | ----------------------------------------------- | ----------------------- |
| 提供者       | JSR-303规范                                     | Spring                  |
| 是否支持分组 | 不支持                                          | 支持                    |
| 标注位置     | METHOD, FIELD, CONSTRUCTOR, PARAMETER, TYPE_USE | TYPE, METHOD, PARAMETER |
| 嵌套校验     | 支持                                            | 不支持                  |

## 进阶使用

### 自定义校验

假设自定义加密 id 校验，要求：由数字或者 `a-f` 的字母组成，`32-256` 长度。

需要两步：

1. 自定义校验注解。

```java
@Target({METHOD, FIELD, ANNOTATION_TYPE, CONSTRUCTOR, PARAMETER})
@Retention(RUNTIME)
@Documented
@Constraint(validatedBy = {EncryptIdValidator.class})
public @interface EncryptId {

    // 默认错误消息
    String message() default "加密id格式错误";

    // 分组
    Class<?>[] groups() default {};

    // 负载
    Class<? extends Payload>[] payload() default {};
}
```

2. 实现 ConstraintValidator 接口，编写校验器。

```java
public class EncryptIdValidator implements ConstraintValidator<EncryptId, String> {

    private static final Pattern PATTERN = Pattern.compile("^[a-f\\d]{32,256}$");

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        // 不为null才进行校验
        if (value != null) {
            Matcher matcher = PATTERN.matcher(value);
            return matcher.find();
        }
        return true;
    }
}
```

### 编程式校验

需要在代码中调用校验注解的逻辑时，可以注入 `javax.validation.Validator` 对象，然后再调用其方法。

```java
@Autowired
private javax.validation.Validator globalValidator;

// 编程式校验
@PostMapping("/saveWithCodingValidate")
public Result saveWithCodingValidate(@RequestBody UserDTO userDTO) {
    
    // 调用校验逻辑
    Set<ConstraintViolation<UserDTO>> validate = globalValidator.validate(userDTO, ValidationGroup.Save.class);

    // 如果校验通过，validate为空；否则，validate包含未校验通过项
    if (validate.isEmpty()) {
        // 校验通过，才会执行业务逻辑处理

    } else {
        for (ConstraintViolation<UserDTO> userDTOConstraintViolation : validate) {
            // 校验失败，做其它逻辑
            System.out.println(userDTOConstraintViolation);
        }
    }
    return Result.ok();
}
```

### 快速失败（Fail Fast）

Spring Validation 默认会校验完所有字段，然后才抛出异常。

可以配置开启 Fail Fast 模式，一旦校验失败就立即返回。

```java
// 在配置类中

@Bean
public Validator validator() {
    ValidatorFactory validatorFactory = Validation.byProvider(HibernateValidator.class)
            .configure()
            .failFast(true)   // 开启快速失败模式
            .buildValidatorFactory();
    return validatorFactory.getValidator();
}
```

## 实现原理

### requestBody 校验原理

在`spring-mvc`中，`RequestResponseBodyMethodProcessor` 用于解析 `@RequestBody`标注的参数以及处理`@ResponseBody`标注方法的返回值，执行参数校验的逻辑就在解析参数的方法`resolveArgument()`中：

```java
public class RequestResponseBodyMethodProcessor extends AbstractMessageConverterMethodProcessor {
    @Override
    public Object resolveArgument(MethodParameter parameter, @Nullable ModelAndViewContainer mavContainer,
                                  NativeWebRequest webRequest, @Nullable WebDataBinderFactory binderFactory) throws Exception {

        parameter = parameter.nestedIfOptional();
        //将请求数据封装到DTO对象中
        Object arg = readWithMessageConverters(webRequest, parameter, parameter.getNestedGenericParameterType());
        String name = Conventions.getVariableNameForParameter(parameter);

        if (binderFactory != null) {
            WebDataBinder binder = binderFactory.createBinder(webRequest, arg, name);
            if (arg != null) {
                // 执行数据校验
                validateIfApplicable(binder, parameter);
                if (binder.getBindingResult().hasErrors() && isBindExceptionRequired(binder, parameter)) {
                    throw new MethodArgumentNotValidException(parameter, binder.getBindingResult());
                }
            }
            if (mavContainer != null) {
                mavContainer.addAttribute(BindingResult.MODEL_KEY_PREFIX + name, binder.getBindingResult());
            }
        }
        return adaptArgumentIfNecessary(arg, parameter);
    }
}
```

其中，`resolveArgument()`调用了`validateIfApplicable()`进行参数校验：

```java
protected void validateIfApplicable(WebDataBinder binder, MethodParameter parameter) {
    // 获取参数注解，比如@RequestBody、@Valid、@Validated
    Annotation[] annotations = parameter.getParameterAnnotations();
    for (Annotation ann : annotations) {
        // 先尝试获取@Validated注解
        Validated validatedAnn = AnnotationUtils.getAnnotation(ann, Validated.class);
        //如果直接标注了@Validated，那么直接开启校验。
        //如果没有，那么判断参数前是否有Valid起头的注解。
        if (validatedAnn != null || ann.annotationType().getSimpleName().startsWith("Valid")) {
            Object hints = (validatedAnn != null ? validatedAnn.value() : AnnotationUtils.getValue(ann));
            Object[] validationHints = (hints instanceof Object[] ? (Object[]) hints : new Object[] {hints});
            //执行校验
            binder.validate(validationHints);
            break;
        }
    }
}
```

其中，因为 @validated 和以 Valid 开头的注解都可以， 所以对 ResponseBody 校验时，@Validated、@Valid 两个注解可以混用。

对于 `WebDataBinder.validate()`  ，最终还是调用了 Hibernate Validator 进行校验处理：

```java
@Override
public void validate(Object target, Errors errors, Object... validationHints) {
    if (this.targetValidator != null) {
        processConstraintViolations(
            //此处调用Hibernate Validator执行真正的校验
            this.targetValidator.validate(target, asValidationGroups(validationHints)), errors);
    }
}
```

### 方法形参校验原理

可以在方法形参前使用校验注解，这种方式可用于任何 Bean 的方法上。

其基于 AOP 实现，通过`MethodValidationPostProcessor`动态注册`AOP`切面，然后使用`MethodValidationInterceptor`对切点方法织入增强。

```java
public class MethodValidationPostProcessor extends AbstractBeanFactoryAwareAdvisingPostProcessorimplements InitializingBean {
    @Override
    public void afterPropertiesSet() {
        //为所有`@Validated`标注的Bean创建切面
        Pointcut pointcut = new AnnotationMatchingPointcut(this.validatedAnnotationType, true);
        //创建Advisor进行增强
        this.advisor = new DefaultPointcutAdvisor(pointcut, createMethodValidationAdvice(this.validator));
    }

    //创建Advice，本质就是一个方法拦截器
    protected Advice createMethodValidationAdvice(@Nullable Validator validator) {
        return (validator != null ? new MethodValidationInterceptor(validator) : new MethodValidationInterceptor());
    }
}
```

MethodValidationInterceptor ：

```java
public class MethodValidationInterceptor implements MethodInterceptor {
    @Override
    public Object invoke(MethodInvocation invocation) throws Throwable {
        //无需增强的方法，直接跳过
        if (isFactoryBeanMetadataMethod(invocation.getMethod())) {
            return invocation.proceed();
        }
        //获取分组信息
        Class<?>[] groups = determineValidationGroups(invocation);
        ExecutableValidator execVal = this.validator.forExecutables();
        Method methodToValidate = invocation.getMethod();
        Set<ConstraintViolation<Object>> result;
        try {
            //方法入参校验，最终还是委托给Hibernate Validator来校验
            result = execVal.validateParameters(
                invocation.getThis(), methodToValidate, invocation.getArguments(), groups);
        }
        catch (IllegalArgumentException ex) {
            ...
        }
        //有异常直接抛出
        if (!result.isEmpty()) {
            throw new ConstraintViolationException(result);
        }
        //真正的方法调用
        Object returnValue = invocation.proceed();
        //对返回值做校验，最终还是委托给Hibernate Validator来校验
        result = execVal.validateReturnValue(invocation.getThis(), methodToValidate, returnValue, groups);
        //有异常直接抛出
        if (!result.isEmpty()) {
            throw new ConstraintViolationException(result);
        }
        return returnValue;
    }
}
```

最终仍然是调用 Hibernate Validator 进行校验处理。

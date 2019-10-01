# 01.SpringBoot Web开发

### 1.SpringBoot对静态资源的支持

静态资源:Js、CSS、image、html...

```java
//对资源进行处理代码
public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                //判断请求中是否包含webjars路径
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

                String staticPathPattern = this.mvcProperties.getStaticPathPattern();
                if (!registry.hasMappingForPattern(staticPathPattern)) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{staticPathPattern}).addResourceLocations(WebMvcAutoConfiguration.getResourceLocations(this.resourceProperties.getStaticLocations())).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }

            }
        }

```

> 1.使用webjars方式对静态资源文件支持

webjars:以jar包的方式添加静态资源

对于/webjars/**请求，将会到"classpath:/META-INF/resources/webjars/"路径下查找静态资源文件

http://localhost:8088/webjars/jquery/3.4.1/jquery.js



> 2.对于/** 请求的处理

对于其他的任意请求，将会到以下四个位置查找资源

```
"classpath:/META-INF/resources/"
"classpath:/resources/"
"classpath:/static/"
"classpath:/public/"
```

### 2.ThymeLeaf模板引擎

#### 2.1 模板引擎

模板引擎：Jsp、Freemarker、Thymeleaf...

效率：Jsp>Freemarker>Thymeleaf

#### 2.2 Thymeleaf模板引擎

> 入门案例

* 1.添加ThymeLeaf依赖

```xml
<!--添加ThymeLeaf依赖-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
```

* 准备模板引擎

```html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <p th:text="${msg}" th:id="${id}" th:class="${class}" id="mid" class="mclass">原本内容</p>
</body>
</html>
```

* 准备数据（Controller）

```java
@Controller
public class WelcomeController {
    @RequestMapping("/welcome")
    public String welcome(Model model){
        model.addAttribute("msg","新的内容");
        model.addAttribute("id","nid");
        model.addAttribute("class","nclass");
        return "welcome";
    }
}
```



问题：

return "welcome"; => template/welcome.html

原因：SpringBoot提供了默认配置（自动配置好了前缀以及后缀）

```java
@ConfigurationProperties(
    prefix = "spring.thymeleaf"
)
public class ThymeleafProperties {
    private static final Charset DEFAULT_ENCODING;
    public static final String DEFAULT_PREFIX = "classpath:/templates/";
    public static final String DEFAULT_SUFFIX = ".html";
    private boolean checkTemplate = true;
    private boolean checkTemplateLocation = true;
    private String prefix = "classpath:/templates/";
    private String suffix = ".html";
    private String mode = "HTML";
```



> Thymeleafd语法

* Thymeleaf属性

| Order | Feature                         | Attributes                                 |
| ----- | ------------------------------- | ------------------------------------------ |
| 1     | Fragment inclusion              | `th:insert` `th:replace`                   |
| 2     | Fragment iteration              | `th:each`                                  |
| 3     | Conditional evaluation          | `th:if` `th:unless` `th:switch` `th:case`  |
| 4     | Local variable definition       | `th:object` `th:with`                      |
| 5     | General attribute modification  | `th:attr` `th:attrprepend` `th:attrappend` |
| 6     | Specific attribute modification | `th:value` `th:href` `th:src` `...`        |
| 7     | Text (tag body modification)    | `th:text` `th:utext`                       |
| 8     | Fragment specification          | `th:fragment`                              |
| 9     | Fragment removal                | `th:remove`                                |



* Thymeleaf表达式

```java
Simple expressions:
    Variable Expressions: ${...} 变量表达式
    Selection Variable Expressions: *{...}
    Message Expressions: #{...}
    Link URL Expressions: @{...}
    Fragment Expressions: ~{...}
Literals
    Text literals: 'one text', 'Another one!',…
    Number literals: 0, 34, 3.0, 12.3,…
    Boolean literals: true, false
    Null literal: null
    Literal tokens: one, sometext, main,…
Text operations:
    String concatenation: +
    Literal substitutions: |The name is ${name}|
Arithmetic operations:
    Binary operators: +, -, *, /, %
    Minus sign (unary operator): -
Boolean operations:
    Binary operators: and, or
    Boolean negation (unary operator): !, not
Comparisons and equality:
    Comparators: >, <, >=, <= (gt, lt, ge, le)
    Equality operators: ==, != (eq, ne)
Conditional operators:
    If-then: (if) ? (then)
    If-then-else: (if) ? (then) : (else)
    Default: (value) ?: (defaultvalue)
Special tokens:
	No-Operation: _
```



* 工具类



- `#execInfo`: information about the template being processed.
- `#messages`: methods for obtaining externalized messages inside variables expressions, in the same way as they would be obtained using #{…} syntax.
- `#uris`: methods for escaping parts of URLs/URIs
- `#conversions`: methods for executing the configured *conversion service* (if any).
- `#dates`: methods for `java.util.Date` objects: formatting, component extraction, etc.
- `#calendars`: analogous to `#dates`, but for `java.util.Calendar` objects.
- `#numbers`: methods for formatting numeric objects.
- `#strings`: methods for `String` objects: contains, startsWith, prepending/appending, etc.
- `#objects`: methods for objects in general.
- `#bools`: methods for boolean evaluation.
- `#arrays`: methods for arrays.
- `#lists`: methods for lists.
- `#sets`: methods for sets.
- `#maps`: methods for maps.
- `#aggregates`: methods for creating aggregates on arrays or collections.
- `#ids`: methods for dealing with id attributes that might be repeated (for example, as a result of an iteration).

#### 2.3 EL+JSTL 使用ThymeLeaf替换



# 02.SpringBoot整合MyBatis


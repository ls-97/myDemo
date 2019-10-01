# SpringBoot

## 1.SpringBoot概述

SpringBoot是Spring的一个模块，用于简化Spring开发

**约定大于配置**

特点：

（1）可以创建独立的[Spring](https://baike.baidu.com/item/Spring/85061)应用程序，并且基于其Maven或Gradle插件，可以创建可执行的JARs和WARs；

（2）内嵌Tomcat或Jetty等Servlet容器；

（3）提供自动配置的“starter”项目对象模型（POMS）以简化[Maven](https://baike.baidu.com/item/Maven/6094909)配置；

（4）尽可能自动配置Spring容器；

（5）提供准备好的特性，如指标、健康检查和外部化配置；

（6）绝对没有代码生成，不需要XML配置。 [1][2]



SpringBoot不建议提供配置文件=>配置类来代替配置文件



## 2.SpringCloud概述（微服务的落地技术）

> 微服务

微服务**架构风格**是一种将一个单一应用程序开发为**一组小型服务**的方法，每个服务运行在自己的进程中，服务间通信采用轻量级通信机制(通常用HTTP资源API)。这些服务围绕业务能力构建并且可通过全自动部署机制独立部署。这些服务共用一个最小型的集中式的管理，服务可用不同的语言开发，使用不同的数据存储技术。 

微服务中的每个服务节点都是可以独立替换或者独立升级的单元



## 3.SpringBoot入门

- About 15 minutes
- A favorite text editor or IDE
- [JDK 1.8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) or later
- [Gradle 4+](http://www.gradle.org/downloads) or [Maven 3.2+](https://maven.apache.org/download.cgi)
- You can also import the code straight into your IDE:
  - [Spring Tool Suite (STS)](https://spring.io/guides/gs/sts)
  - [IntelliJ IDEA](https://spring.io/guides/gs/intellij-idea/)



> 1.编写pom.xml,添加父工程以及依赖

```xml
  <!--指定父工程-->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.6.RELEASE</version>
  </parent>

  <!--添加依赖-->
  <dependencies>
    <!--Web启动器-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
  </dependencies>
```



> 2.编写主程序类

```java
@SpringBootApplication  //声明当前类是一个SpringBoot应用的主程序类，如果需要启动SpringBoot应用，那么只需要执行主程序类中的main方法即可
public class MyApplication {

    public static void main(String[] args) {
        SpringApplication.run(MyApplication.class, args);
    }
}
```



> 3.编写controller（注意：**controller所在的包一定需要与主程序类同级**）

```java
@RestController
public class GreettingController {
    @RequestMapping("/greeting")
    public Map<String,String> greeting(){
        Map<String ,String> map = new HashMap<>();
        map.put("id","1");
        map.put("message","Hello World");
        return map;
    }
}
```



> 4.端口修改（一般不会出现）

application.properties(SpringBoot默认配置文件)

```properties
server.port=8086
```



> 5.执行主程序类中的main方法，启动SpringBoot应用，并测试

<http://localhost:8086/greeting> 



## 4.分析SpringBoot入门案例

### 4.1 分析pom.xml

```xml
<!--指定父工程-->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.6.RELEASE</version>
  </parent>

  <!--添加依赖-->
  <dependencies>
    <!--Web启动器-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
  </dependencies>
```

> 1.父工程

```xml
<!--指定父工程-->
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.6.RELEASE</version>
  </parent>

<!-- 父工程的父工程 -->
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-dependencies</artifactId>
    <version>2.1.6.RELEASE</version>
    <relativePath>../../spring-boot-dependencies</relativePath>
  </parent>
```

作用：提供了 版本仲裁中心对日常开发中的一些依赖进行版本锁定，那么一旦指定父工程，在项目中添加依赖时就不需要指定版本号了（如果对于版本仲裁中心中没有锁定的依赖，还是需要添加版本号的 ）



> 2.启动器

```xml
 <!--Web启动器-->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
```



spring-boot-starter-* SpringBoot提供的启动器，SpringBoot对日常开发场景做了相对应的封装，那么开发过程中可以针对相对应的场景，选择对应的启动器

比如：spring-boot-starter-web 是springboot对web开发场景进行了封装，包含了web开发中常用的所有依赖



SpringBoot提供的启动器：

<https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/using-boot-build-systems.html#using-boot-starter> 



### 4.2 分析主程序类

```java
@SpringBootApplication
public class MyApplication {

    public static void main(String[] args) {
        //格式固定
        SpringApplication.run(MyApplication.class, args);
    }
}
```

@SpringBootApplication:

声明当前类是一个SpringBoot应用的主程序类，如果需要启动SpringBoot应用，那么只需要执行主程序类(@SpringBootApplication修饰的类)中的main方法即可



@SpringBootApplication剖析

* @SpringBootConfiguration：SpringBoot的配置类

   * @Configuration  Spring配置类注解

     ```java
     /*
     	配置类：等价于配置文件
     	Spring开发过程中，会存在spring的相关配置文件，（applicationContext.xml）
     	但是在SpringBoot是不推荐编写配置文件的，建议使用配置类来代替
     	<bean id="userService" class="com.igeekhome.springboot.service.UserService">
     */
     ```

* @EnableAutoConfiguration  开启自动配置

   * @AutoConfigurationPackage 自动配置包

     	* @Import({Registrar.class}) 向容器中导入组件

     ```java
     public void registerBeanDefinitions(AnnotationMetadata metadata, BeanDefinitionRegistry registry) {
         //向容器中注册Bean（(new AutoConfigurationPackages.PackageImport(metadata)).getPackageName()包下的bean、组件）
         //扫描包
                 AutoConfigurationPackages.register(registry, (new AutoConfigurationPackages.PackageImport(metadata)).getPackageName());
             }
     
      (new AutoConfigurationPackages.PackageImport(metadata)).getPackageName()=>
     com.igeekhome.springboot
     扫描@SpringBootApplication修饰的（主程序类）类所在的包，将该包下的所有组件扫描到Spring容器中
     ```

   * @Import({AutoConfigurationImportSelector.class})  向容器中导入组件

     ```java
     public String[] selectImports(AnnotationMetadata annotationMetadata) {
             if (!this.isEnabled(annotationMetadata)) {
                 return NO_IMPORTS;
             } else {
                 AutoConfigurationMetadata autoConfigurationMetadata = AutoConfigurationMetadataLoader.loadMetadata(this.beanClassLoader);
                 AutoConfigurationImportSelector.AutoConfigurationEntry autoConfigurationEntry = this.getAutoConfigurationEntry(autoConfigurationMetadata, annotationMetadata);
                 return StringUtils.toStringArray(autoConfigurationEntry.getConfigurations());
             }
         }
     ```

     ![](E:\66营\框架\就业阶段\02.SpringBoot\day01\课件\assets\autoConfiguration.PNG)

     

  22个

  固定格式：

  xxxAutoConfiguration   自动配置

  Spring开发中需要手动配置很多组件，比如视图解析器、转换器、文件上传解析器、处理器映射器...

  SpringBoot默认会根据当前使用的组件或者依赖从META-INF/spring.factories给我们进行了很多自动配置（这些组件），直接使用即可



## 5.SpringBoot应用的执行与部署

> 1.执行

运行主程序类的main方法即可

> 2.部署（创建可执行的jar包）

```xml
<build>
    <plugins>
      <!--用于创建可执行jar包-->
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
```



package打包



java -jar xxx.jar



默认使用内嵌的tomcat（缺点：不支持jsp模板引擎、推荐使用thymeleaf模板引擎）



> 3.使用常规方式希望部署到tomcat，并且希望支持jsp(不太推荐)







## 6.SpringBoot快速初始化（[Spring Initializr](https://start.spring.io/) ）

前提：保证联网

resources:默认

* static:存放静态资源（css、js、image...）
* templates:存放页面模板引擎（JSP、Freemarker、Thymeleaf....）
* application.properties（默认命名） springboot默认配置文件，springboot应用启动时自动加载该配置文件（修改springboot的默认配置）



## 7.SpringBoot的默认配置文件

SpringBoot应用在启动时默认会自动加载**application.properties**、**application.yml**配置文件



作用：修改springboot的默认配置



### 7.1 YAML配置文件

以数据做为中心 ，更适合作为配置文件

```xml
<server>
	<port>8086</port>
</server>
```

```yaml
server:
	port: 8080
	cc: dd
aa: bb
```

YAML是使用键值对来配置数据的，并且键值是大小写敏感的

格式：

键:[空格]值

YAML使用缩进来控制层级关系，左对齐的数据都是属于同一级别的



YAML值的写法：

> 1.字面量（常量）

即使是字符串类型也不需要添加双引号以及单引号

```yaml
person:
  name: 张三
  age: 20
  isActive: true
```

> 2.对象（Map）

```yaml
person:
  name: 张三
  age: 20
  isActive: true
  pet:
    name: 旺财
    age: 2
```

行内写法：

```yaml
pet: {name: 旺财,age: 2}
```

> 3.数组

```yaml
person:
  name: 张三
  age: 20
  isActive: true
  pet:
    name: 旺财
    age: 2
  hobby:
    - 篮球
    - 足球
    - 羽毛球
```

行内写法：

```yaml
hobby: [篮球,足球,羽毛球]
```

### 7.2 配置文件绑定对象

```java
/*
前提：绑定的对象必须是容器中组件
@ConfigurationProperties
将配置文件中的指定的配置数据绑定到对象的属性上
prefix:将配置文件中哪个数据进行绑定
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person {
```



```xml
<!--解决配置文件提示问题-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```



| 对比            | @Value       | @ConfigurationProperties |
| --------------- | ------------ | ------------------------ |
| 功能            | 绑定单个属性 | 批量绑定                 |
| 松散绑定        | 不支持       | 支持                     |
| spEL表达式      | 支持         | 不支持                   |
| 复杂对象        | 不支持       | 支持                     |
| JSR-303数据校验 | 不支持       | 支持                     |



### 7.3 自定义配置文件绑定

```java
@Component
@PropertySource("classpath:person.properties") //指定自定义属性配置文件的位置
@ConfigurationProperties(prefix = "person")
```

注意：自定义属性配置文件绑定@PropertySource不支持YAML文件格式



### 7.4 定义spring配置文件以及加载（不推荐）

SpringBoot是不推荐使用配置文件的（applicationContext.xml）,推荐使用配置类代替配置文件

但是也可以去编写

> 1.编写spring配置文件

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <bean id="userService" class="com.igeekhome.springboot_qs02.service.UserService"></bean>
</beans>
```

> 2.因为SpringBoot应用启动时不会主动加载该spring配置文件，需要在主程序类上显式指定

```java
//指定加载spring配置文件
@ImportResource(locations = "classpath:spring/applicationContext.xml")
```


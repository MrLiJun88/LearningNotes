# SpringBoot入门

## 1.SpringBoot 简介

> 简化Spring应用开发的一种框架
>
> 整个Spring技术栈的一个大整合
>
> J2EE开法的一站式解决方案

## 2.导入SpringBoot依赖

````xml 
 <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
</parent>
 <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
</dependencies>
````

## 3.简化部署

> 将应用打成一个可执行的 jar 包

```xml
           <!-- 该插件可以将应用打包成一个可执行的 jar 包 -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
```

## 4.POM文件解读

### 1.父项目

```xml
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>1.5.9.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
上面的父项目是
    <parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-dependencies</artifactId>
		<version>1.5.9.RELEASE</version>
		<relativePath>../../spring-boot-dependencies</relativePath>
	</parent>
他是真正管理SpringBoot应用里面依赖的版本
```

### 2.导入的依赖

```xml
       <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
       </dependency>
```

> spring-boot-starter-web:场景启动器，帮我们导入Web模块需要的依赖组件

## 5.主程序入口

### 1.SpringBoot基础注解

```java
@SpringBootApplication
public class SpringbootDemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(SpringbootDemoApplication.class, args);
    }
```

- @SpringBootApplication:该注解标注在哪个类上，则说明该类就是SpringBoot的启动类

- @SpringBootConfiguration:标注在哪个类上，说明该类是SpringBoot的配置类

- @EnableAutoConfiguration：开启自动配置功能

- @AutoConfigurationPackage:自动扫描包

- @Import(AutoConfigurationPackages.Registrar.class):将启动类(被@SpringBootApplication标注的类)所在的包以及下面所有的子包里面所有的组件扫描到springboot容器中

- @Import(EnableAutoConfigurationImportSelector.class):导入哪些组件的选择器,将所需要导入的组件以全类名的方法返回，会给容器导入非常多的自动类型类(xxxxxAutoConfiguration),就是给容器所处理的场景导入需要的组件，并自动配置好。如下图：

![自动配置](C:\Users\Administrator\Desktop\SpringBoot笔记\images\自动配置.JPG)

### 2.两个配置文件

> 配置文件的名字是固定的

* application.properties
* application.yml

### 3.YML语法

````yaml
k:(空格) v : 表示一对键值对，空格必须要有，以空格的缩进来控制层级关系,区分大小写
如：
server: 
    port: 8081
    path: hello/
````

### 4.YML值的写法

#### 1.字面量:(数值，字符串，布尔)

````yaml
k: v: 字面值直接写，字符串默认不用双引号括起来
````

#### 2.对象，Map，List(数组)

````java
//对象
customer: 
    name: lijun
    age: 25
//对象的行内写法
customer: {name: lijun,age: 25,address: nanjing}
//List,数组
arrays: 
 - aaa
 - bbb
 - ccc
 //集合，数组的行内写法
 arrays: [aaa,bbb,ccc]
//Map
map: {K: V,K: V} 如：  map: {address: nanjing,phone: 110}
//对象类型 如下：
dog :
  dogName: wangcai
  dogAge: 7
````

#### 3.配置文件注入到JavaBean中

> 在实体类中配置 @Component,@ConfigurationProperties两个注解

````java
//将实体类加入到SpringBoot容器中
@Component
//perfix="person" 对应 yam 文件中的person 名称
@ConfigurationProperties(prefix = "person")
public class Person {
    private String name;
````

> 在POM文件中导入配置处理器

```xml
      <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
       </dependency>
```

### 5.在 .properties配置文件中注入Bean

````properties
person.name=wmy
person.age=22
person.sex=false
<!-- 集合，数组类型-->
person.list=111,222,333
<!-- map类型 -->
person.map.address=nanjing
person.map.phone=119
person.map.work=java
<!-- dog对象类型-->
person.dog.dogName=wangcai
person.dao.dogAge=7
````

### 6.@Value与@ConfigurationProperties获取值的区别

|                | @ConfigurationProperties | @Value |
| :------------: | :----------------------: | :----: |
|      注值      |         批量注入         |  单个  |
|    松散语法    |           支持           | 不支持 |
|      SpEl      |          不支持          |  支持  |
| JSR303数据校验 |           支持           | 不支持 |
|  注入复杂类型  |           支持           | 不支持 |

> SpringBoot的简单数据类型：8个原生类型+String+Date,如果只是需要在某个业务中获取一个配置文件中的某个单独的值，建议使用@Value注解获取;而如果是将配置文件与某个实体类各个属性赋值，使用@ConfigurationProperties更方便。

### 7.@PropertySource与ImportResource

> @PropertySource：加载指定的配置文件,默认是加载application.properties|application.yml中的配置
>
> ```java
> @Component
> @ConfigurationProperties(prefix = "person")
> @PropertySource(value = "classpath:person.properties")
> public class Person {
>     private String name;
> ```
>
> @ImportResource:springboot默认是不支持xml文件中的<bean>配置，如果要编写自己的spring配置文件，能被springboot识别，需要在springboot的启动类上声明@ImportResource

````java
@ImportResource(locations = {"classpath:spring.xml"})
@SpringBootApplication
public class SpringbootDemoApplication {
````

> springboot并不推荐上述做法，而是应该通过注解形式来配置，如下

### 8.使用配置类来代理xml配置文件

> 使用@Configuration 声明该类是一个配置类，@Bean 声明是一个bean配置，id即为方法名

```java
/**声明该类是一个配置类，相当于spring的配置文件如：applicationContext.xml*/
@Configuration
public class MyConfig {
    /**
     *将方法的返回值添加到容器中，id即为方法名
     */
    @Bean
    public MyService myService(){
        return new MyService();
    }
```

### 9.全局配置文件占位符

|       ${random.uuid}       |      uuid      |
| :------------------------: | :------------: |
|      ${random.value}       |   随机字符串   |
|       ${random.int}        |    随机整数    |
|       ${random.int}        |   随机长整数   |
|     ${random.int(10)}      |  10以内的整数  |
| ${random.int[1024,655365]} | 指定随机数范围 |

> yml配置文件可以与properties配置文件的值相互引用，如在设置中引用properties中配置的值

````properties
stuName: ${persion.name}
````

### 10.多开发环境的切换(profile)

#### 1.多profile文件

> 通过properties文件来切换，默认springboot是读取application.properties文件，如果有多个，则定义名称： application-环境名.properties.如：application-dev.properties,application-test.properties.
>
> 要使用哪个环境需要在application.properties 中指定，如下：

````properties
spring.profiles.active=dev
````

#### 2.yml多文档块方式

````yaml
server:
  port: 8085
spring:
  profiles:
  #需要使用哪个环境，就在active后面声明激活哪个环境
    active: test  
---
server:
  port: 8081
spring:
  profiles: dev
---
server:
  port: 8082
spring:
  profiles: test
````

#### 4.命令行动态切换

- 通过命令行方式：--spring.profiles.active=dev
- 将项目打成jar包，再在cmd命令中追加 --spring.profiles.active=dev
- 通过设置虚拟机参数：-Dspring.profiles.active=dev

### 11.配置文件加载路径 

#### 1.在指定路径下写配置文件

> properties和 .yml中的配置，相互补充，如果冲突，则 .properties 的优先级高。
>
> spingboot默认能够读取的两个配置文件：application.propertes和 application.yml这两个文件。他们可以存入在以下目录中，并且他们的优先级从上往下变低，高优先级的配置会覆盖低优先级的配置。如果不冲突则互补，结合使用。

|   file:项目根目录/config/   |
| :-------------------------: |
|      file:项目根目录/       |
| classpath:项目根目录/config |
|    classpath:项目根目录/    |

#### 2.通过命令指定运行哪个配置文件

> 项目打包好以后，可以使用命令行参数的形式，启动项目的时候来指定配置文件的新位置，指定的配置文件和默认加载的配置文件共同作用形成互补配置。

````shell
java -jar 项目名.jar --spring.config.location=D:/applicaiton.properties
````

#### 3.SpringBoot自动加载配置过程

1. @SpringBootApplication注解 内包含了 @EnableAutoConfiguration注解，这是一个自动配置注解，内包含了 @Import(EnableAutoConfigurationImportSelector.class),EnableAutoConfigurationImportSelector的父类AutoConfigurationImportSelector实现了自动加载默认配置。

   ````java
   @Deprecated
   public class EnableAutoConfigurationImportSelector
   		extends AutoConfigurationImportSelector {
   ````

2. SpringBoot会自动加载 "META-INF/spring.factories" 标注为 spring.boot.enableautoconfiguration 下的配置类,如下图

![自动配置类](C:\Users\Administrator\Desktop\SpringBoot笔记\images\自动配置类.JPG)

![自动加载](C:\Users\Administrator\Desktop\SpringBoot笔记\images\自动加载.JPG)

## 6.SpringBoot与日志

> SpringBoot默认支持的日志是：slf4j并不是具体的日志实现类，而是定义了一种抽象接口，统一了实现的方法,logback是slf4j的一种具体实现。在实际开发中，日志记录方法的调用 ，不应该来直接调用日志的实现类，而是调用日志抽象层里面的方法。在应用中导入slf4j的jar和logback的实现包如下：

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HelloWorld {
  public static void main(String[] args) {
    Logger logger = LoggerFactory.getLogger(HelloWorld.class);
    logger.info("Hello World");
  }
}
```

> 每一个日志的实现框架都有自己的配置文件，使用slf4j后，配置文件还是写成日志实现框架的配置文件，图示：

###### https://www.slf4j.org/manual.html

![日志](C:\Users\Administrator\Desktop\SpringBoot笔记\images\日志.jpg)

### 1.遗留问题的解决

> 如果项目中使用的是slf4j+logback,而spring使用的是Commons logging ,mybatis使用的是log4j,
>
> 统一日志记录，即使是别的框架也统一使用slf4j进行

###### https://www.slf4j.org/images/legacy.png

![日志2](C:\Users\Administrator\Desktop\SpringBoot笔记\images\日志2.JPG)



### 2.如何让系统中所有的日志都统一到slf4j

1. 将系统中其他日志框架先排除出去
2. 用中间包替换原有的日志框架
3. 导入slf4j针对于其他日志框架的代替包

### 3.SpringBoot日志底层依赖关系

![日志 3](C:\Users\Administrator\Desktop\SpringBoot笔记\images\日志 3.jpg)

> 总结：SpringBoot也是使用 lsf4j+logback的日志使用方式进行记录，同时也把其他的日志框架(log4j,jcl,jul)用替代包的形式实现用 lsf4j统一进行管理；如果我们要引拉其他日志框架，一定要把SprintBoot默认依赖日志框架移除掉。
>
> ###### SpringBoot能自动适配所有的日志，而且底层使用slf4j+logback的方式记录日志，引入其他框架的时候，只需要把这个框架依赖的日志框架排除掉即可。
>
> 例如：spring默认使用的日志框架是commons-logging,但在SpringBoot项目中要先排序这个默认日志框架，才可以使用SpringBoot自己的日志框架。如：

![日志4](C:\Users\Administrator\Desktop\SpringBoot笔记\images\日志4.JPG)

### 4.日志的使用

> SpringBoot已经默认配置好了日志,SpringBoot默认的日志级别是 info ,即只输出 info 及比它更高级别的日志信息。

#### 1.日志级别

>  **TRACE < DEBUGE < INFO < WARN < ERROR < FATAL < OFF**	

#### 2.修改默认日志级别

````properties
logging.levle.包名=日志级别
#例如：将日志级别改成 trace 级别
logging.level.com.springboot=trace
````

#### 3.批量日志输出位置

````properties
#将日志信息输出在当前项目下生成 springboot.log日志文件,也可以指定完整的路径
logging.file=sprintboot.log
````

````properties
#将日志信息输出到E盘下，默认名为 spring.log
logging.path=E:/
````

````properties
# 指定在控制台输出的日志格式
logging.pattern.console=%d{yyyy-MM-dd} [%thread] %-5level %logger{50} - %msg%n
#解析
%d: 日期时间
%thread:线程名
%-5level:显示日志级别 -5：从左往右显示5个字符宽度
%logger{50}:设置日志长度
%msg:日志信息
%n:回车，换行
````

```properties
#修改日志写入到文件中的格式
logging.pattern.file=%d{yyyy-MM-dd} ** [%thread] ** %-5level ** %logger{50}** %msg%n
```

#### 4.指定自己的日志配置文件

> 在 resources 下指定特定的文件名
>
> logback.xml:可以直接被日志框架识别启动
>
> logback-spring.xml:不直接加载日志的配置项，由SpringBoot解析日志配置，可以使用到SpringBoot的高级springProfile功能，指定在哪种特定环境下使用什么日志信息输出。

```properties
<springProfile name="staging">
	<!-- configuration to be enabled when the "staging" profile is active -->
</springProfile>

<springProfile name="dev | staging">
	<!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
</springProfile>

<springProfile name="!production">
	<!-- configuration to be enabled when the "production" profile is not active -->
</springProfile>
```

![日志5](C:\Users\Administrator\Desktop\SpringBoot笔记\images\日志5.JPG)

#### 5.切换日志框架

> 将logback日志换成使用 log4j2日志框架

1. 将原先的logback排除
2. 导入log4j2

## 7.SpringBoot与Web开发

 ### 1.SpringBoot对静态资源映射的规则

> springboot默认将静态请求映射到 classpath:/META-INF/resources/webjars/**路径 下

````java
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
            if (!this.resourceProperties.isAddMappings()) {
                logger.debug("Default resource handling disabled");
            } else {
                Duration cachePeriod = this.resourceProperties.getCache().getPeriod();
                CacheControl cacheControl = this.resourceProperties.getCache().getCachecontrol().toHttpCacheControl();
                if (!registry.hasMappingForPattern("/webjars/**")) {
                    this.customizeResourceHandlerRegistration(registry.addResourceHandler(new String[]{"/webjars/**"}).addResourceLocations(new String[]{"classpath:/META-INF/resources/webjars/"}).setCachePeriod(this.getSeconds(cachePeriod)).setCacheControl(cacheControl));
                }
````

![路径 ](C:\Users\Administrator\Desktop\SpringBoot笔记\images\路径 .JPG)

### 2.SpringBoot静态资源文件夹

> 1.classpath:/META-INF/resources/
>
> 2.classpath:/resources/
>
> 3.classpath:/static/
>
> 4.classpath:/public/

````java
@ConfigurationProperties(
    prefix = "spring.resources",
    ignoreUnknownFields = false
)
public class ResourceProperties {
    private static final String[] CLASSPATH_RESOURCE_LOCATIONS = new String[]{"classpath:/META-INF/resources/", "classpath:/resources/", "classpath:/static/", "classpath:/public/"};
    private String[] staticLocations;
````

### 3.欢迎页的设置

> 欢迎页设置在静态资源文件夹的 index.html页面；被"/**" 映射
>
> 图标：在静态资源文件夹的 favicon.ico 下

````java
private Resource getIndexHtml(String location) {
            return this.resourceLoader.getResource(location + "index.html");
        }
````

```java
public SimpleUrlHandlerMapping faviconHandlerMapping() {
                SimpleUrlHandlerMapping mapping = new SimpleUrlHandlerMapping();
                mapping.setOrder(-2147483647);
                mapping.setUrlMap(Collections.singletonMap("**/favicon.ico", this.faviconRequestHandler()));
                return mapping;
            }
```

### 4.模版引擎(Thymeleaf)

> ######  Thymeleaf官方使用手册: https://www.thymeleaf.org/doc/tutorials/3.0/usingthymeleaf.html#variables

#### 1.引入模版引擎

````xml
<!-- 引入 Thymeleaf 引擎-->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
````

#### 2.Thymeleaf使用&语法

> 将页面放在内路径下的templates文件夹下，以 .html结尾，springboot就会自动识别到

````java
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
````

> 在页面中导入Thymeleaf

````html
<!DOCTYPE html>
<html lang="en" xmlns:th="http://www.thymeleaf.org">
````

##### 1.Thymeleaf语法

![魔板](C:\Users\Administrator\Desktop\SpringBoot笔记\images\魔板.JPG)

##### 2.表达式语法

```properties

    Simple expressions:(表达式语法)
        Variable Expressions: ${...}：获取变量值
          1):获取对象的属性，调用方法
          2):使用内置的基本对象
              #ctx: the context object.
              #vars: the context variables.
              #locale: the context locale.
              #request: (only in Web Contexts) the HttpServletRequest object.
              #response: (only in Web Contexts) the HttpServletResponse object.
              #session: (only in Web Contexts) the HttpSession object.
              #servletContext: (only in Web Contexts) the ServletContext object
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

### 5.SpringBoot对Web应用的自动配置

> SpringBoot在Spring默认设置的基础上添加了以下功能：
>
> - Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.
>
>   **自动配置了ViewResovler（视图解析器：根据方法的返回值得到视图对象(View),视图对象决定如何处理(渲染|转发|重定向?)）**
>
>   `ContentNegotiatingViewResolver`:**自动组合所有的视图解析器** 
>
> - Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-static-content))).
>
>   ​    **静态资源文件夹路径，webjars**
>
> - Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.
>
>   **`Converter`:类型转换器**
>
>   **`Formatter` :格式转换器 2019.9.7 --> Date类型**
>
> - Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-message-converters)).
>
>   **`HttpMessageConverters` ：SpringMVC用来转换http请求和响应的消息转换器，对象 -> json**
>
> - Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-message-codes)).
>
>   **定义错误代码生成规则**
>
> - Static `index.html` support. **静态首页访问**
>
> - Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-favicon)). **图标：favicon.ico**
>
> - Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/2.1.8.RELEASE/reference/html/boot-features-developing-web-applications.html#boot-features-spring-mvc-web-binding-initializer)).
>
>   **初始化Web数据绑定器的 ，如：请求数据绑定到 JavaBean中**

#### 1.扩展SpringMVC

> 如何在SpringBoot对mvc的支持上再扩展自己的功能，springboot官方手册给了如下方法：
>
>  **如果您希望保留spring boot mvc功能，并且希望添加其他mvc配置（拦截器、格式化程序、视图控制器和其他功能），则可以添加自己的@Configuration类，类型为WebMvcConfigurer，但不添加@EnableWebMvc。如果希望提供RequestMappingHandlerMapping、RequestMappingHandlerAdapter或ExceptionHandlerExceptionResolver的自定义实例，则可以声明WebMVCregistrationAdapter实例来提供此类组件。** 例如：

````java
/**既保留了原有配置也扩展了我们自己的配置*/
@Configuration
public class MyConfig implements WebMvcConfigurer {
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        /**当发送/hi请求也到达hello.html页面*/
        registry.addViewController("/hi").setViewName("hello");
    }
````

#### 2.全面接管SpringMVC

>  **如果您想完全控制SpringMVC，可以添加自己的@Configuration，并用@EnableWebMvc进行注释。** 

````java
/**在SpringBoot对Web的自动配置类上，有下面一个注解
@ConditionalOnMissingBean({WebMvcConfigurationSupport.class):
   如果不包含WebMvcConfigurationSupport.class，则springboot自动配置类生效，
   否则springboot将不会对进行web自动配置
*/
@ConditionalOnMissingBean({WebMvcConfigurationSupport.class})
public class WebMvcAutoConfiguration {
````

````java
/**
而为什么加上了@EnableWebMvc注解，springboot就不会对web进行自动配置呢？因为这句话
@Import({DelegatingWebMvcConfiguration.class}，DelegatingWebMvcConfiguration类是
继承了WebMvcConfigurationSupport，所以一旦加上 @EnableWebMvc注解，springboot就不会对web
进行任何自动配置，即失去了web控制权.
*/
@Import({DelegatingWebMvcConfiguration.class})
public @interface EnableWebMvc {
````

![配置](C:\Users\Administrator\Desktop\SpringBoot笔记\images\配置.JPG)

#### 3.如何修改SpringBoot默认配置

> SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的(@Bean,@Component).如果有就用用户配置的，如果没有，才会自动配置默认的；如果有些组件有多个(ViewResolver),将用户配置和SpringBoot默认配置组合起来使用。

### 6.国际化设置

#### 1.编写国际化配置文件

![国际化](C:\Users\Administrator\Desktop\SpringBoot笔记\images\国际化.JPG)

> 在properties文件中设置包地址
>
> ```properties
> #设置配置国际化文件包地址
> spring.messages.basename=i18n.login
> ```

#### 2.SpringBoot自动配置好了管理国际化资源文件的组件

````java
@ConfigurationProperties(prefix = "spring.messages")
public class MessageSourceAutoConfiguration {
    
    /**
	 * Comma-separated list of basenames (essentially a fully-qualified classpath
	 * location), each following the ResourceBundle convention with relaxed support for
	 * slash based locations. If it doesn't contain a package qualifier (such as
	 * "org.mypackage"), it will be resolved from the classpath root.
	 */
	private String basename = "messages";  
    //我们的配置文件可以直接放在类路径下叫messages.properties；
    
    @Bean
	public MessageSource messageSource() {
		ResourceBundleMessageSource messageSource = new ResourceBundleMessageSource();
		if (StringUtils.hasText(this.basename)) {
            //设置国际化资源文件的基础名（去掉语言国家代码的）
			messageSource.setBasenames(StringUtils.commaDelimitedListToStringArray(
					StringUtils.trimAllWhitespace(this.basename)));
		}
		if (this.encoding != null) {
			messageSource.setDefaultEncoding(this.encoding.name());
		}
		messageSource.setFallbackToSystemLocale(this.fallbackToSystemLocale);
		messageSource.setCacheSeconds(this.cacheSeconds);
		messageSource.setAlwaysUseMessageFormat(this.alwaysUseMessageFormat);
		return messageSource;
	}
````

```java
		@Bean
		@ConditionalOnMissingBean
		@ConditionalOnProperty(prefix = "spring.mvc", name = "locale")
		public LocaleResolver localeResolver() {
			if (this.mvcProperties
					.getLocaleResolver() == WebMvcProperties.LocaleResolver.FIXED) {
				return new FixedLocaleResolver(this.mvcProperties.getLocale());
			}
			AcceptHeaderLocaleResolver localeResolver = new AcceptHeaderLocaleResolver();
			localeResolver.setDefaultLocale(this.mvcProperties.getLocale());
			return localeResolver;
		}
默认的就是根据请求头带来的区域信息获取Locale进行国际化
```

#### 3.设置自己的国际化获取方式

````java
/**
 * 定义自己的区域信息解析器
 * 可以在链接上携带区域信息
 */
public class MyLocalResolver implements LocaleResolver {
    @Override
    public Locale resolveLocale(HttpServletRequest httpServletRequest) {
        String lang = httpServletRequest.getParameter("lang");
        Locale locale = Locale.getDefault();
        if(! StringUtils.isEmpty(lang)){
            String[] split = lang.split("_");
            locale = new Locale(split[0],split[1]);
        }
        return locale;
    }
````

#### 4.在页面中获取国际化的值(使用Thymeleaf)

![国际化2](C:\Users\Administrator\Desktop\SpringBoot笔记\images\国际化2.JPG)

### 7.在实际项目中的应用

#### 1.登陆错误消息的显示 

> #strings.isEmpty(error):当从Controller层返回回来的错误消息为不为空时，才会显示 P 标签内容，如果
>
> 没有，则代表没有错误消息，不会显示 P 标签.

````html
<p style="color:red" th:if="${not #strings.isEmpty(error)}" th:text="${error}"></p>
````

#### 2.解决页面重复提交的问题(重定向)

> 通过重定向到另一个页面请求，由视图解析器转到指定页面

![重定向](C:\Users\Administrator\Desktop\SpringBoot笔记\images\重定向.JPG)

![重定义2](C:\Users\Administrator\Desktop\SpringBoot笔记\images\重定义2.JPG)

#### 3.拦截器进行登录检查

>创建拦截器实现 HandlerInterceptor接口

````java
/**
 * 用于登陆检查的拦截器
 */
public class LoginHandlerInterceptor implements HandlerInterceptor {
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        HttpSession session = request.getSession();
        Object name = session.getAttribute("name");
        /**如果用户名不为空，说明是正确登陆的*/
        if(null != name){
            return true;
        }
        /**为空代表非法登陆，重新转发到登陆页面*/
        else {
            request.getRequestDispatcher("/login.html").forward(request,response);
            request.setAttribute("error","没有访问权限，请先登陆!");
            return false;
        }
    }
}
````

> 将拦截器类配置到自己扩展的SpringMVC功能中

````java
@Configuration
public class MyConfig implements WebMvcConfigurer {
    /**设置自己的视图解析器，默认首页为 login.html*/
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/").setViewName("login");
        registry.addViewController("/login.html").setViewName("login");
        registry.addViewController("/main.html").setViewName("dashboard");
    }
    /**配置自己定义的拦截器类*/
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        /***拦截所有请求，并排除特定登陆的几个请求*/
        registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**").
                excludePathPatterns("/","/login.html","/user/login");
    }
}
````

#### 4.基本CRUD操作

> 使用 RestfulCRUD风格

|      | 普通CRUD（uri来区分操作） |    RestfulCRUD    |
| :--: | :-----------------------: | :---------------: |
| 查询 |          getEmp           |     emp---GET     |
| 添加 |        addEmp?xxx         |    emp---POST     |
| 修改 |  updateEmp?id=xxx&xxx=xx  |  emp/{id}---PUT   |
| 删除 |      deleteEmp?id=1       | emp/{id}---DELETE |

##### 1.请求的架构

|     功能     | 请求URL | 请求方式 |
| :----------: | :-----: | :------: |
| 查询所有员工 |  emps   |   GET    |
| 查询某个员工 |  epm/1  |   GET    |
| 来到添加页面 |   emp   |   GET    |
|   修改员工   |   emp   |   PUT    |
|   删除员工   |  emp/1  |  DELETE  |
|   添加员工   |   emp   |   POST   |

##### 2.抽取公共片段

````html
  <!-- 抽取公共片段-->
   <div th:fragment="copy">
      &copy; 2011 The Good Thymes Virtual Grocery
   </div>
  <!-- 引入公共片段 insert默认会放在div标签中-->
   <div th:insert="~{footer :: copy}"></div>
````

##### 3.`th:insert`和`th:replace`（和`th:include`）之间的区别

- `th:insert` 将内容放在<div></div>标签中
- `th:replace` 整体替换，完全的复制
- `th:include`类似于`th:insert`，但只放入内容，不包含标签

#### 5.日期格式化问题

> SpringBoot默认解析的日期格式是yyyy/MM/dd格式，我们也可以修改成自己的日期格式 。可以在properties中自定义

````properties
spring.mvc.date-format=yyyy.MM.dd
````

> 在thmeleaf中格式化日期

```html
<td th:text="${#dates.format(emp.birth, 'yyyy-MM-dd HH:mm')}"></td>
```

#### 6.PUT和DELETE请求

> 1.页面的请求还是以 post 方式提交

```html
<input type="hidden" name="_method" value="put"/>
<input type="hidden" name="_method" value="delete"/>
```

### 8.错误处理机制

#### 1.SpringBoot默认的错误处理机制

> 如果是浏览器访问发生错误，springboot会返回一个html错误页面，而如果是其他客户端访问则会返回一个错误信息的json信息。
>
> 如何定制错误的页面?
>
> 1.有模板的情况下，error/状态码 [将错误页面命名为，错误状态码.html放在    templates里的 error 文件夹下]，发生此状态码的错误就会来到对应的页面；
>
> 我们可以使用 4xx 和 5xx 作为错误的文件名来匹配这种类型的所有错误(优先使用有精状态码的页面)
>
> 2.没有模版引擎(模版引擎找不到这个错误页面)，静态资源文件夹(static)下找 error/4xx.html.
>
> 3.以上都没有错误的话，就会默认来到SpringBoot默认的错误提示页面

![4xx](C:\Users\Administrator\Desktop\SpringBoot笔记\images\4xx.JPG)

>SpringBoot默认的错误空白页面

![错误2](C:\Users\Administrator\Desktop\SpringBoot笔记\images\错误2.JPG)

> 可以在渲染后的错误页面通过thymeleaf的行内写法获取以下错误数据
>
>   1.timestamp
>
>    2.status
>
>    3.error
>
>    4.exception
>
>    5.message
>
> ​    6.trace
>
> ​    7.path

#### 2.定制错误的json数据

> 拦截自定义异常，并返回自定义json错误信息,下面的写法会造成，不管是浏览器还是客户端访问，返回的都是json数据，没有做到浏览器与客户端访问的差异性。

```java
@ControllerAdvice
public class MyExceptionHandler {
    @ResponseBody
    @ExceptionHandler(MyException.class)
    public Map<String,Object> handlerException(Exception e){
        Map<String,Object> map = new HashMap<>();
        map.put("code","user is not exit");
        map.put("time",new Date());
        map.put("message",e.getMessage());
        return map;
    }
}
```

> 转发到error进行错误自适应,来自官方代码示例：

````java
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {

	@ExceptionHandler(YourException.class)
	@ResponseBody
	ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
		HttpStatus status = getStatus(request);
		return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
	}

	private HttpStatus getStatus(HttpServletRequest request) {
		Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
		if (statusCode == null) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
		return HttpStatus.valueOf(statusCode);
	}

}
````

### 9.配置嵌入式Servlet容器

> SpringBoot默认使用的嵌入式Servlet容器(Tomcat),

![tomcat](C:\Users\Administrator\Desktop\SpringBoot笔记\images\tomcat.JPG)

> 如何定制和修改Servlet相关配置？
>
> ​    1.在properties 文件中修改和 server 相关的配置

```properties
#通用的servlet容器设置
server.xx
server.port=8081
#设置项目访问名
server.servlet.context-path=/sbdemo

#Tomcat的设置
server.tomcat.xxx
```

>    2.编写一个WebServerFactoryCustomizer <ConfigurableServletWebServerFactory>的实现类 :Web服务定制器

````java
@Component
public class CustomizationBean implements       WebServerFactoryCustomizer<ConfigurableServletWebServerFactory> {
   /**定制嵌入式的Servlet容器相关规则*/
    @Override
    public void customize(ConfigurableServletWebServerFactory factory) {
        factory.setPort(8088);
    }
}
````

#### 9.1 注册Servlet,Filter,Listener

> ###### 可以使用`ServletRegistrationBean`，`FilterRegistrationBean`以及`ServletListenerRegistrationBean`类的完全控制。
>
> 例：如下，注册自己的过滤器

````java
@Configuration
public class MyFilterServlet {
    @Bean
    public FilterRegistrationBean filterRegistrationBean(){
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean();
        filterRegistrationBean.setFilter(new MyFilter());
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/","/hello"));
        return filterRegistrationBean;
    }
}
````

> 注册`ServletListenerRegistrationBean`

````java
//定义自己的Listener组件
public class MyListener implements ServletContextListener {
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("web启动");
    }
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("web关闭");
    }
}
````

```java
//将自定义的Listener添加到SPringBoot中
@Configuration
public class MyListenerRegister {
     @Bean
    public ServletListenerRegistrationBean servletListenerRegistrationBean(){
        return new ServletListenerRegistrationBean(new MyListener());
    }
}
```

> 注册自己的Servlet，添加到容器中

```java
public class MyServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        this.doGet(req,resp);
    }
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.getWriter().write("hello springbot !!!");
    }
}
```

>将自己的Servlet添加到容器中

```java
@Configuration
public class MyListenerRegister {
    @Bean
    public ServletRegistrationBean servletRegistrationBean(){
        return new ServletRegistrationBean(new MyServlet(),"/hello","/*");
    }
}
```



#### 9.2 如何使用其他的容器框架

![tomcat2](C:\Users\Administrator\Desktop\SpringBoot笔记\images\tomcat2.JPG)

#### 9.3 嵌入式Servlet的自动配置原理 

### 10.使用外置的Servlet容器(支持JSP)

> 1.创建一个war项目
>
> 2.将嵌入式的Tomcat指定为provided
>
> 3.必须编写一个 public class ServletInitializer extends SpringBootServletInitializer { 类

```xml
        <!-- 将嵌入式的Tomcat指定为provided --> 
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
            <scope>provided</scope>
        </dependency>
```

```java
public class ServletInitializer extends SpringBootServletInitializer {
//必须编写一个 public class ServletInitializer extends SpringBootServletInitializer类
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(SpringbootJspApplication.class);
    }

}
```

## 11. Docker


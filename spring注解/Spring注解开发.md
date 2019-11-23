# Spring注解开发

## 1. 通过注解给IOC容器添加组件

> * 首先先创建一个配置类
> * 在配置类中写一个返回类型为要创建组件类型，id默认为方法名的方法

```java
/**
 * 配置类就等于配置文件
 * @Configuration:告诉Spring这是一个配置类
 */
@Configuration
public class MyConfig {

    /**@Bean：
     * 给容器注册一个Bean，类型为返回值类型
     * id默认用方法名作为id
     * name="person01" 默认id为方法名，name重写指定要获取的对象id名
     * */
    @Bean(name = "person01")
    public Person getPersonInstance(){
        return new Person(2,"wmy",22);
    }
}
```

```java
    /**通过注解获取Person对象*/
    @Test
    public void testPersonByAnnotation(){
        System.out.println("通过注解获取对象实例...");
        ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig.class);
        System.out.println(context.getBean(Person.class));
        System.out.println("=============");
        /**获取到Person类型id名*/
        String[] str = context.getBeanNamesForType(Person.class);
        Arrays.stream(str).forEach(System.out::println);
    }
```

## 2. 使用@ComponentScan注解自动扫描

> 使用 `@ComponentScan` 在配置类上，标识在IOC容器启动时，会自动扫描哪些包下的组件，并自动加入到IOC容器当中。

```java
@Configuration
/**扫描com.njcit包下的所有被标识 @Service,@Repository,@Controller,@Component的bean，并添加到容器中*/
@ComponentScan(value = {"com.njcit"})
public class MyConfig {
```

### 2.1 排除要扫描的包

> `Filter[] excludeFilters() default {};`

```java
@Configuration
@ComponentScan(value = {"com.njcit"},
        excludeFilters = {@ComponentScan.Filter(type = FilterType.ANNOTATION,
                classes = {Controller.class})})
public class MyConfig {
```

### 2.2 只扫描指定的包

> `Filter[] includeFilters() default {};`
>
> `boolean useDefaultFilters() default true;` :默认扫描策略，全部扫描

```java
@Configuration
@ComponentScan(value = {"com.njcit"},
        includeFilters = {@ComponentScan.Filter(type = FilterType.ANNOTATION,
        classes = {Controller.class})},
        useDefaultFilters = false
)
public class MyConfig {
```

## 3. FilterType注解指定过滤规则

```java
public enum FilterType {
    /**注解类型*/
	ANNOTATION,
	/**指定的类型*/
	ASSIGNABLE_TYPE,
	/**按照Aspectj的表达式*/
	ASPECTJ,
	/**按照正则表达式*/
	REGEX,
	/**自定义规则*/
	CUSTOM

}
```

### 3.1 自定义TypeFilter指定过滤规则

```java
/**
 * 自定义类型过滤,需要实现TypeFilter接口，并重写它唯一的方法 match(),
 * 当match（）方法返回true，则当前扫描的类被放入spring容器中，返回false则不放入容器中。
 * 我这里规定类名中包含“Person”的返回true，即将其放入到容器中，否则返回false不放入到容器中：
 */
public class MyFilter implements TypeFilter {

    /**
     * @param metadataReader 读取到当前正在扫描的类信息
     * @param metadataReaderFactory  读取到其他任何类信息
     */
    @Override
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        /**如果ClassName中包含Person的则进行过滤匹配*/
        if (metadataReader.getClassMetadata().getClassName().contains("Person")){
            //获取当前类注解的信息
            AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
            for (String s : annotationMetadata.getAnnotationTypes()) {
                System.out.println("当前正在被扫描的类注解类型" + s);
            }
            //获取当前正在扫描类的信息
            ClassMetadata classMetadata = metadataReader.getClassMetadata();
            System.out.println("当前正在被扫描的类的类名" + classMetadata.getClassName());
            //获取当前类的资源信息（类存放的路径...）
            Resource resource = metadataReader.getResource();
            System.out.println("当前正在被扫描的类存放的地址" + resource.getURL());
            return true;
        }
        return false;
    }
}
```

> 将上述自定义的过滤规则添加到容器扫描中

```java
@Configuration
@ComponentScan(value = {"com.njcit"},
        includeFilters = {
                @ComponentScan.Filter(type = FilterType.CUSTOM,
                classes = {MyFilter.class})
        },useDefaultFilters = false
)
public class MyConfig {
```

## 4. @Scope 设置JavaBean的作用域

> 在Spring的IOC容器中，每一个创建的Bean都默认是单实例的。
>
> * prototype : 多实例的：IOC容器启动并不会去调用方法创建对象放在容器中，每次获取对象的时候才会调用创建对象。
> * singleton : 单实例（默认），IOC容器启动会调用方法创建对象放在IOC容器中，以后每次获取都会直接从容器中拿。
> * request : 同一次请求创建一个实例
> * session : 同一次会话创建一个实例

```java
    @Bean(value = "person02")
    @Scope(value = "prototype")
    public Person person(){
        System.out.println("create Person in IOC");
        return new Person(25,"hello",99);
    }
```

## 5. @Lazy 懒加载

> 针对于单实例，默认在IOC启动时会创建对象，并添加到容器中。
>
> 懒加载：容器启动不创建对象。第一次获取Bean对象时，才创建对象，并初始化。

```java
    @Bean(value = "person02")
    @Lazy(value = true)
    public Person person(){
        System.out.println("create Person in IOC");
        return new Person(25,"hello",99);
    }
```

## 6. @Conditional 按照条件注册Bean

> @Conditional 原码,由原码可见 **@Conditional注解即可用在类上，也可以用在方法上**

```java
@Target({ElementType.TYPE, ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Conditional {

	/**
	 * All {@link Condition Conditions} that must {@linkplain Condition#matches match}
	 * in order for the component to be registered.
	 */
	Class<? extends Condition>[] value();

}
```

>  从源码中可以看到，需要传入一个Class数组，并且需要继承Condition接口：
>
>  Condition是个接口，需要实现matches方法，返回true则注入bean，false则不注入。  

```java
@FunctionalInterface
public interface Condition {

	 /**
     * @param conditionContext:判断条件能使用的上下文环境
     * @param annotatedTypeMetadata:注解所在位置的注释信息
     */
	boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);

}
```

> 这是一个简单的例子，现在问题来了，如果我想根据当前操作系统来注入Person实例，windows下注入bill，linux下注入linus，怎么实现呢？
>
> 这就需要我们用到@Conditional注解了，前言中提到，需要实现Condition接口，并重写方法来自定义match规则。
>
> 首先，创建一个WindowsCondition类,LinuxCondition类似，也需要实现Condition接口：
>

```java
/**
 * 如果当前系统为Window则添加该Bean
 */
public class WindowsCondition implements Condition {

    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        //获取ioc使用的beanFactory
        ConfigurableListableBeanFactory factory = context.getBeanFactory();
        //获取类加载器
        ClassLoader classLoader = context.getClassLoader();
        //获取bean定义的注册类
        BeanDefinitionRegistry registry = context.getRegistry();
        //获得当前系统名
        Environment environment = context.getEnvironment();
        String osName = environment.getProperty("os.name");
        //包含Windows则说明是windows系统，返回true
        if("Windows 10".equals(osName)){
            return true;
        }
        return false;
    }
}
```

### 6.1 标注在方法上

>  **一个方法只能注入一个bean实例，所以@Conditional标注在方法上只能控制一个bean实例是否注入。** 

```java
    @Bean(value = "person03")
    /**只有当前系统是Windows系统，IOC才会创建添加到容器中*/
    @Conditional(value = {WindowsCondition.class})
    public Person getPersonInstance03(){
        return new Person(88,"windows",77);
    }

    @Bean(value = "person04")
    /**只有当前系统是Linux系统，IOC才会创建添加到容器中*/
    @Conditional(value = {LinuxCondidion.class})
    public Person getPersonInstance04(){
        return new Person(55,"linux",66);
    }
```

### 6.2 标注在类上

>  **一个类中可以注入很多实例，@Conditional标注在类上就决定了一批bean是否注入。** 

 ```java
@Configuration
/**如果当前系统是Window，则该配置类下注册的所有Bean都会被创建，并添加到容器中*/
@Conditional(value = {WindowsCondition.class})
public class MyConfig2 {

    @Bean(value = "person02")
    public Person person(){
        return new Person(25,"unix",99);
    }
    
    @Bean
    ....
 ```

### 6.3 多条件加载

>  前言中说，@Conditional注解传入的是一个Class数组，存在多种条件类的情况。 
>
> * 第一个条件类实现的方法返回true，第二个返回false，则结果false，不注入进容器。
>
> * 第一个条件类实现的方法返回true，第二个返回true，则结果true，注入进容器中。

```java
   @Bean(value = "person03")
    /**只有当前系统是Windows系统或者是Linux系统，IOC才会创建添加到容器中*/
    @Conditional(value = {WindowsCondition.class,LinuxCondidion.class})
    public Person getPersonInstance03(){
        return new Person(88,"windows",77);
    }
```

## 7. 给容器中注册组件方式

> * 配置包扫描+组件标识注解(`@Controller,.@Service,@Repository,@Component`)
> * `@Bean` (导入的第三方包里面组件)
> * `@Import` (快速的给容器中导入组件)

### 7.1 使用@Import导入组件

> `@Import` 可以快速的导入组件，value接收的是一个Class类型的数组,
>
> `@Import` 根据源码只能放在类上

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {
	Class<?>[] value();
}
```

> 将`Dog`类使用`@Import`注解导入到容器中
>
> id 默认是组件的全类名 `com.njcit.entity.Dog`

```java
@Configuration
@ComponentScan(value = {"com.njcit"}
)
@Import(value = {Dog.class})
public class MyConfig {
```

### 7.2 使用 ImportSelector 批量导入组件

```java
public interface ImportSelector {

	String[] selectImports(AnnotationMetadata importingClassMetadata);

}
```

> 编写自己的批量导入逻辑,实现ImportSelector接口

```java
/**
 * 自定义导入逻辑
 */
public class MyImportSelector implements ImportSelector {

    /**
     * AnnotationMetadata 导入该类的信息
     * @return 导入的组件的全类名
     */
    @Override
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
      //将要注册到容器的bean，以全类名的方式返回
        return new String[]{"com.njcit.entity.XiAn","com.njcit.entity.BeiJing"};
    }
}
```

> 将自定义的批量导入逻辑声明在 @Import中
>
> id 默认为类的全类名

```java
@Configuration
@ComponentScan(value = {"com.njcit"}
)
@Import(value = {MyImportSelector.class})
public class MyConfig {
```

### 7.3 ImportBeanDefinitionRegistrar

```java
public interface ImportBeanDefinitionRegistrar {

	/**
	 * @param importingClassMetadata annotation metadata of the importing class
	 * @param registry current bean definition registry
	 */
	public void registerBeanDefinitions(
			AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry);

}
```

> 自定义要注册的组件逻辑,实现ImportBeanDefinitionRegistrar接口

```java
public class MyImportBeanDefinitionRegister implements ImportBeanDefinitionRegistrar {

    @Override
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {

        /**如果容器已经有了dog的存在，则注册xian类，否则注册beijing类*/
        if(registry.containsBeanDefinition("com.njcit.entity.Dog")){
            BeanDefinition beanDefinition = new RootBeanDefinition(XiAn.class);
            //还可以设置类的作用域
            beanDefinition.setScope("prototype");
            registry.registerBeanDefinition("xian",beanDefinition);
        }
        else {
            BeanDefinition beanDefinition = new RootBeanDefinition(BeiJing.class);
            registry.registerBeanDefinition("beijing",beanDefinition);
        }
    }
}
```

> 将自定义的批量导入逻辑声明在 @Import中

```java
@Configuration
@ComponentScan(value = {"com.njcit"}
)
@Import(value = {MyImportBeanDefinitionRegister.class})
public class MyConfig {
```

### 7.4 FactoryBean

```java
public interface FactoryBean<T> {

	@Nullable
	T getObject() throws Exception;

	@Nullable
	Class<?> getObjectType();

	default boolean isSingleton() {
		return true;
	}
}
```

```java
/**
 * 定义自己的FactoryBean对象 
 */
public class MyFactoryBean implements FactoryBean<BeiJing> {

    @Override
    public BeiJing getObject() throws Exception {
        return new BeiJing();
    }

    @Override
    public Class<?> getObjectType() {
        return BeiJing.class;
    }
}
```

> 将自定义的`factoryBean`添加到配置类中

```java
@Configuration
@ComponentScan(value = {"com.njcit"}
)
@Import(value = {Dog.class})
public class MyConfig {
    
    /**将自定义的工厂bean声明在配置类中
    id 默认为方法名字，类型为FactoryBean中getObject()获取的对象类型
    */
    @Bean
    public MyFactoryBean beijing(){
        return new MyFactoryBean();
    }
}
```

`beijing`

`bean类型 class com.njcit.entity.BeiJing`

## 8. Bean生命周期

> * 指定初始化和销毁方法
>
>   通过`@Bean`注解标识初始化和销毁方法
>
>   ```java
>   @Bean(name = "dog",initMethod = "init",destroyMethod = "destroy")
>       public Dog getDogInstance(){
>           return new Dog();
>       }
>   ```
>
> * 通过让Bean 实现`InitializingBean` 接口，实现初始化,通过让Bean实现 `DisposableBean`接口，实现销毁，当类被注册到容器中，会自动调用`afterPropertiesSet()`初始化方法与`destroy()`方法
>
>   ```java
>   public class XiAn implements InitializingBean, DisposableBean{
>   
>       private String name;
>   
>       public XiAn() {
>           System.out.println("xian被创建...");
>       }
>   
>       @Override
>       public void destroy() throws Exception {
>           System.out.println("xian被卸载");
>       }
>   
>       @Override
>       public void afterPropertiesSet() throws Exception {
>           System.out.println("xian被初始化");
>       }
>   }
>   ```
>
> * 使用JSR250中注解 `@PostConstruct` **在bean创建和构造方法之后被标识的方法会被执行**
>
>   `@PreDestroy`在容器销毁`bean`之前。
>
>   当类被注册到容器中时，会自动调用被`@PostConstruct`标注的初始化方法,在完成构造方法之后，和在类被销毁时会自动被`@PreDestroy`标注的销毁方法
>
>   ```java
>   public class BeiJing {
>   
>       @PostConstruct
>       public void init(){
>           System.out.println("beijing类被初始化");
>       }
>   
>       @PreDestroy
>       public void destroy() {
>           System.out.println("beijing类被卸载");
>       }
>   }
>   ```
>
> * BeanPostProcessor: bean后置处理器,会给每个在容器中注册的类都添加上实现的初始化，和销毁方法。
>
>   * `postProcessBeforeInitialization`()在bean构造方法之前执行
>   * `postProcessAfterInitialization`()在bean构造方法之后执行
>
>   ```java
>   public interface BeanPostProcessor {
>       /**在初始化之前工作*/
>   	@Nullable
>   	default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
>   		return bean;
>   	}
>   
>   	@Nullable
>   	default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
>   		return bean;
>   	}
>   }
>   ```
>
>   ```java
>   //自定义 BeanPostProcessor 的实现类
>   @Component
>   public class MyBeanPostProcessor implements BeanPostProcessor {
>   
>       @Override
>       public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
>           System.out.println("在初始化之前被调用***");
>           System.out.println("beanName: " + beanName);
>           System.out.println("beanClass: " + bean.getClass());
>           System.out.println("=====================");
>           return bean;
>       }
>   
>       @Override
>       public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
>           System.out.println("在初始化之后被调用...");
>   
>           return bean;
>       }
>   }
>   ```
>
>   `dog类被创建...`
>   `在初始化之前被调用***`
>   `beanName: dog`
>
>   `beanClass: class com.njcit.entity.Dog`
>
>   `dog类被初始化`
>   `在初始化之后被调用...`

## 9. 为bean对象属性赋值

> * `@Value` 注解赋值
>
>   * 基本数值
>   * SpEL,#{}
>   * ${},取出配置文件中的值
>
>   ```java
>   public class Person {}
>       @Value("001")
>       private long id;
>       @Value("lijun")
>       private String name;
>       @Value("#{56-1}") //SpEl表达式
>       private int age;
>   ```
>
> * 从外部配置文件获取属性值
>
>   * 在配置类中通过`@PropertySource`注解加载到配置文件
>
>     ```java
>     /**使用@PropertySource加载外部文件配置*/
>     @PropertySource(value = {"classpath:person.properties"})
>     @Configuration
>     public class MyConfig3 {
>     ```
>
>   * 通过 ${} ,取出配置文件中的值，赋值给属性变量
>
>     ```java
>     public class Person {
>     
>         @Value(value = "${person.id}")
>         private long id;
>         @Value("${person.name}")
>         private String name;
>         @Value("${person.age}")
>         private int age;
>     ```
>
>   * 可能通过上下文环境获取外部配置文件值
>
>     ```java
>      ApplicationContext context = new AnnotationConfigApplicationContext(MyConfig3.class);
>     
>             Environment environment = context.getEnvironment();
>             String id = environment.getProperty("person.id");
>             String name = environment.getProperty("person.name");
>     ```

## 10. 自动装配

### 10.1 @Autowired

> @Autowired:自动注入
>
> 1.  默认优先级按照类型去容器中找到对应的组件`:PersonController pc = context.getBean(PersonController.class);`
>
> 2. 如果找到多个相同类型的组件，再将属性的名称作为组件的id去容器中查找 `PersonController pc = (PersonController)context.getBean("personController");`
>
> 3. 可以使用`@Qualifier(value="bookDao")` 使用@Qualifier指定需要配置的组件的id,而不是属性名
>
> 4. `@Autowired`自动装配默认一定要找到，否则会异常，可以使用`@Autowired(required=false)`
>
> 5. `@Primary` :让Spring进行自动装配时，默认首选使用哪个bean,该注解用于类或方法上,且不能与
>
>    `@Qualifier`注解一起使用，因为`@Qualifier`是精确匹配

### 10.2 @Resource 和 @Inject

> * `@Resource` 可以和`@Autowired`一样实现自动装配功能，默认是按照属性名称进行装配，但不能支持 `@Primary`注解和`@Autowired(required=false)`功能
>
> * `@Inject` 使用需要额外导入包,功能与 `@Autowired `类似,但没有 `requied=false `的功能
>
>   ```xml
>         <!-- Inject-->
>           <dependency>
>               <groupId>javax.inject</groupId>
>               <artifactId>javax.inject</artifactId>
>               <version>1</version>
>           </dependency>
>   ```

## 11. @Profile 切换环境

> Spring 为我们提供的可以根据当前环境，动态的激活和切换一系统组件的功能
>
> 指定组件在哪个环的情况下才能被注册到容器中，不指定时，任何环境都能注册这个组件
>
> 加了环境标识的bean,只有这个环境被激活才能注册到容器中
>
> ```java
> @Target({ElementType.TYPE, ElementType.METHOD})
> @Retention(RetentionPolicy.RUNTIME)
> @Documented
> @Conditional(ProfileCondition.class)
> public @interface Profile {
> 
> 	String[] value();
> 
> }
> ```

### 11.1 环境切换

> * 使用命令行动态参数，在虚拟机参数位置设置 `-Dspring.profiles.active=test`
>
> * 使用代码方式激活环境
>
>   ```java
>    /**创建出IOC容器*/
>           AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext();
>           /**设置需要使用的环境*/
>           context.getEnvironment().setActiveProfiles("dev");
>           /**将配置类注册到环境中*/
>           context.register(DataSourceConfig.class);
>           /**容器启动刷新*/
>           context.refresh();
>   ```
>
> * 这里举一个以加载不同数据库的环境做例子
>
>   ```java
>       @Bean
>       //定义环境是测试环境
>       @Profile(value = {"test"})
>       public DataSource dataSourceByTest() throws Exception {
>           ComboPooledDataSource dataSource = new ComboPooledDataSource();
>           dataSource.setDriverClass("com.mysql.jdbc.Driver");
>           dataSource.setUser("root");
>           dataSource.setPassword("123");
>           dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
>           return dataSource;
>       }
>       @Bean
>       //定义环境是开发环境
>       @Profile(value = {"dev"})
>       public DataSource dataSourceByDev() throws Exception {
>           ComboPooledDataSource dataSource = new ComboPooledDataSource();
>   ```
>
> * 当`@Profile` 定义在类上，只有当匹配`@Profile`指定的环境时，整个类才会被加载

## 12. 基于AOP的注解开发

### 12.1 定义切面类

```java
@Aspect
public class Log4Cal {

    @Before("execution(* com.njcit.aop.Calculator.*(..))")
    public void logStart(JoinPoint joinPoint){
        Signature signature = joinPoint.getSignature();
        System.out.println("被代理方法名 " + signature.getName() + " 被代理类的类型" + signature.getDeclaringType());
        System.out.println("代理方法开始运行前....");
    }

    @After("execution(* com.njcit.aop.Calculator.*(..))")
    public void logOver(){
        System.out.println("代理方法运行结束后....");
    }

    @AfterReturning(returning = "object",value = "execution(* com.njcit.aop.Calculator.*(..))")
    public void logReturning(Object object){
        System.out.println("返回结果：" + object);
        System.out.println("代理方法正常返回后运行....");
    }

    @AfterThrowing(throwing = "exception",value = "execution(* com.njcit.aop.Calculator.*(..))")
    public void logThrowing(Exception exception){
        System.out.println("异常信息：" + exception.getMessage());
        System.out.println("代理方法遇到异常....");
    }
}
```

### 12.2 将切面类和要代理的类加入到配置类中

> `@EnableAspectJAutoProxy` 开启基于注解的aop

```java
@Configuration
/**@EnableAspectJAutoProxy 开启基于注解的aop模式
 * 相当于在applicationContext.xml中配置<aop:aspectj-autoproxy/>开启注解功能
 * */
@EnableAspectJAutoProxy
public class AOPConfig {

    @Bean
    public Calculator calculator(){
        return new Calculator();
    }
    /**切面类*/
    @Bean
    public Log4Cal log4Cal(){
        return new Log4Cal();
    }
}
```

### 12.3 开启基于AOP注解功能

> 在配置类上使用 `@EnableAspectJAutoProxy`

```java
@Configuration
/**@EnableAspectJAutoProxy 开启基于注解的aop模式
 * 相当于在applicationContext.xml中配置<aop:aspectj-autoproxy/>开启注解功能
 * */
@EnableAspectJAutoProxy
public class AOPConfig {
```

## 13. 基于注解的声明式事务

> 1. 在配置类上添加 `@EnableTransactionManagement`注解
> 2. 在配置类上声明事务管理器 `DataSourceTransactionManager`,添加到容器中
> 3. 在CRUD方法上添加 `@Transactional`注解，标识这是一个事务方法

### 13.1 编写配置文件

```java
/**
 * 1. @EnableTransactionManagement : 开启基于注解的事务管理功能
 * 2. 将事务管理器添加到容器中 DataSourceTransactionManager
 * 声明式事务
 */
@Configuration
@ComponentScan
@EnableTransactionManagement
public class TXConfig {

    /**
     * 配置数据源
     */
    @Bean
    public DataSource dataSource() throws Exception{
        ComboPooledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setUser("root");
        dataSource.setPassword("123");
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
        return dataSource;
    }

    /**
     * 声明jdbcTemplate 来操作数据库
     */
    @Bean
    public JdbcTemplate jdbcTemplate() throws Exception {
        JdbcTemplate jdbcTemplate = new JdbcTemplate();
        jdbcTemplate.setDataSource(this.dataSource());
        return jdbcTemplate;
    }

    /**配置事务管理器*/
    @Bean
    public DataSourceTransactionManager dataSourceTransactionManager() throws Exception {
        DataSourceTransactionManager transactionManager = new DataSourceTransactionManager();
        transactionManager.setDataSource(this.dataSource());
        return transactionManager;
    }
}
```

### 13.2 在CRUD方法上添加事务注解

```java
   @Transactional(
            /**出现哪些异常需要数据回滚*/
            rollbackFor = {ArithmeticException.class}
    )
    public void insertPerson(){
        personDao.insertPerson();
    }
```

## 14. 扩展知识

### 14.1 BeanFactoryPostProcessor

> bean工厂的bean属性后置处理容器，说通俗一些就是可以管理我们的bean工厂内所有的beandefinition（未实例化）数据，可以随心所欲的修改属性。 

#### 14.1.1 使用方法

```java
/**
 * 实现自己的beanFactory后置处理器
 */
public class MyBeanFactoryPostProcessor implements BeanFactoryPostProcessor {
    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        System.out.println("MyBeanFactoryPostProcessor 在工作...");
        int beanCounts = beanFactory.getBeanDefinitionCount();
        System.out.println("有 " + beanCounts + " 已经被创建了");
        String[] beanNames = beanFactory.getBeanDefinitionNames();
        System.out.println("===============已创建的bean名字=============");
        for (int i = 0; i < beanNames.length; i++) {
            String name = beanNames[i];
            BeanDefinition bd = beanFactory.getBeanDefinition(name);
            System.out.println(name + " bean属性: " + bd.getPropertyValues().toString());
        }
    }
}
```

```java
/**
 * BeanPostProcessor : bean后置处理器，bean创建对象初始化前后进行拦截工作
 * BeanFactoryPostProcessor ：beanFactory的后置处理器
 *               在BeanFactory标准初始化之后调用,所有的bean已经保存加载到beanFactory中
 *               但bean的实例还未创建,它的运行在BeanPostProcessor之前,即在其他Bean实例化之前
 *
 */
@Configuration
public class ExtConfig {

    @Bean
    public MyBeanFactoryPostProcessor myBeanFactoryPostProcessor(){
        return new MyBeanFactoryPostProcessor();
    }
}
```

### 14.2 BeanPostProcessor

> 从范围上来说，从上面的所有的bean成为了特定的bean，其次BeanFactoryPostProcessor可以在初始化前修改bean的属性等情况，但是BeanPostProcessor只能在初始化后（注意初始化不包括init方法）执行一些操作。

#### 14.2.1 使用方法

```java
public class Cat {
    @Value("tomcat")
    private String catName;

    public Cat() {
        System.out.println("Cat的构造方法....");
    }

    @PostConstruct
    public void init(){
        System.out.println("Cat init ...");
    }

    @PreDestroy
    public void destroy(){
        System.out.println("Cat destroy ...");
    }

    @Override
    public String toString() {
        return "Cat{" +
                "catName='" + catName + '\'' +
                '}';
    }
}
```

```java
public class MyBeanPostProcessor implements BeanPostProcessor {

    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        /** 如果当前的bean是Student,则打印日志*/
        if(bean instanceof Cat){
            System.out.println("postProcessBeforeInitialization bean : " + beanName);
        }
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        if(bean instanceof Cat){
            System.out.println("postProcessAfterInitialization bean : " + beanName);
        }
        return bean;
    }
}
```

```java
@Configuration
public class MyConfig4 {

    @Bean
    public Cat cat(){
        return new Cat();
    }

    @Bean
    public MyBeanPostProcessor myBeanPostProcessor(){
        return new MyBeanPostProcessor();
    }
}
```

> 输出结果
>
> `Cat的构造方法....`
> `postProcessBeforeInitialization bean : cat`
> `Cat init ...`
> `postProcessAfterInitialization bean : cat`
>
> * 先打印出了构造器内部执行的话，意味着这个时候实例化了Student类，
>
> * 在init方法前执行了postProcessBeforeInitialization
>
> * 在init方法后执行了postProcessAfterInitialization

### 14.3 BeanDefinitionRegistryPostProcessor

> `BeanDefinitionRegistryPostProcessor` 是 `BeanFactoryPostProcessor` 的一个子接口
>
> 在所有bean定义信息将要被加载，但还未实例化之前。
>
> 一般来说在业务代码中，加上 @Component, @Service，@Repository, @Controller等注解就可以实现将bean注册到Spring中了。（静态）
>
> **问题：** 怎么动态注册到Spring中呢？
>  那么就是用`BeanDefinitionRegistryPostProcessor`了。
>  `BeanDefinitionRegistryPostProcessor`继承`BeanFactoryPostProcessor`，是一种比较特殊的`BeanFactoryPostProcessor`。
>
> `BeanDefinitionRegistryPostProcessor`中定义的`postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry)`方法 可以让我们实现自定义的注册bean定义的逻辑。

```java
public class MyBeanDefinitionRegistryPostProcessor implements BeanDefinitionRegistryPostProcessor {

    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) throws BeansException {
        System.out.println("postProcessBeanDefinitionRegistry...bean的数量 " + registry.getBeanDefinitionCount());
        /***为容器中注册一个组件*/
        BeanDefinition cat = new RootBeanDefinition(Cat.class);
        registry.registerBeanDefinition("myCat",cat);
    }

    @Override
    public void postProcessBeanFactory(ConfigurableListableBeanFactory beanFactory) throws BeansException {
        System.out.println("postProcessBeanFactory...bean的数量 " + beanFactory.getBeanDefinitionCount());
    }
}
```

### 14.4 ApplicationListener

> 监听窗口中发布的事件。事件驱动模型开发

```java
@FunctionalInterface
public interface ApplicationListener<E extends ApplicationEvent> extends EventListener {

	/**
	 * Handle an application event.
	 * @param event the event to respond to
	 */
	void onApplicationEvent(E event);
}
```

## 15. Spring容器的创建过程

> **Srping IOC的创建过程主要在 refresh() 方法中完成的**
>
> 1. `prepareRefresh()`刷新前的预处理
>
>    1. `initPropertySources()` 初始化一些属性设置，该方法是空的，由子类自定义个性化的属性设置方法
>    2. `getEnvironment().validateRequiredProperties()` 校验属性的合法性
>    3. `this.earlyApplicationEvents = new LinkedHashSet<>()`保存容器早期的事件
>
> 2. `ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory()` 获取bean工厂
>
>    1. `refreshBeanFactory();`刷新bean工厂
>       1. `this.beanFactory = new DefaultListableBeanFactory()`创建了一个bean工厂
>       2. 为该benaFactory设置一个id
>    2.  `getBeanFactory();`返回刚才GenericalApplicationContext创建的beanFactory对象
>    3. 将创建的BeanFactory ( DefaultListableBeanFactory )返回
>
> 3. `prepareBeanFactory(beanFactory);`BeanFactory预处理工作，对BeanFactory进行设置
>
>    1.  设置BeanFactory的类加载器，支持的表达式解析器，设置忽略的自动装配接口，注册可以解析的自动装配,环境变量等等...
>
> 4. `postProcessBeanFactory(beanFactory);`BeanFactory准备完成后的后置处理工作,子类通过重写这个方法来在`BeanFactory`创建并预准备完成以后做进一步处理。
>
> 5. `invokeBeanFactoryPostProcessors(beanFactory);`执行BeanFactory的后置处理器，**先执行`BeanDefinitionRegistryPostProcessor`再执行`BeanFactoryPostProcessor`**，在BeanFactory标准初始化之后执行的，即BeanFactory创建后
>
>    1. `PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(beanFactory, getBeanFactoryPostProcessors())`
>       1. 获取所有的`BeanDefinitionRegistryPostProcessor`的名字，存放在一个`String[]`中
>       2. 先执行实现了`PriorityOrdered`优先级接口的`BeanDefinitionRegistryPostProcessor`
>       3. 在执行实现了`Order`顺序接口的`BeanDefinitionRegistryPostProcessor`
>       4. 最后执行没实现任何优先级或者顺序接口的`BeanDefinitionRegistryPostProcessor`
>       5. 再执行`BeanFactoryPostProcessor`的方法
>          1. 获取所有的`BeanFactoryPostProcessor`
>          2. 先执行实现了`PriorityOrdered`优先级接口的`BeanFactoryPostProcessor`
>          3. 在执行实现了`Order`顺序接口的`BeanFactoryPostProcessor`
>          4. 最后执行没实现任何优先级或者顺序接口的`BeanFactoryPostProcessor`
>
> 6. `registerBeanPostProcessors(beanFactory);`注册Bean的后置处理器
>
>    1. `registerBeanPostProcessors(beanFactory, this);`
>
>       1. 获取所有的`BeanPostProcessor`名存放在`String[]`中
>
>       `DestructionAwareBeanPostProcessor`
>
>       `InstantiationAwareBeanPostProcessor`
>
>       `SmartInstantiationAwareBeanPostProcessor`
>
>       `MergedBeanDefinitonPostProcessor`不同接口类型的`BeanPostProcessor`，在Bean创建时期执行时机是不一样的
>
>       2. 先注册 `PriorityOrdered`优先级接口的`BeanPostProcessor`
>
>       3. 再注册`Order`接口的
>
>       4. 最后注册没有实现任何优先级接口的`BeanPostProcessor`
>
>       5. 最终注册所有的内部`internalPostProcessors`
>
>       6. 注册一个`ApplicationListenerDetector`来在Bean创建完成后检查是否是实现了`ApplicationListener`的实现类，如果是 `applicationContext.addApplicatonListener(ApplicationListener<?> bean)`
>
> 7. `initMessageSource();`初始化`MessageSource`组件(做国际化功能，消息绑定，消息解析)
>
>    1. `beanFactory = getBeanFactory();` 获取`BeanFactory`
>
>    2. `beanFactory.containsLocalBean(MESSAGE_SOURCE_BEAN_NAME)`看容器中是否有id为`messageSource`的组件，如果有赋值给`beanFactory`的`messageSource`属性,如果没有则创建一个
>
>       `DelegatingMessageSource`对象
>
> 8. `initApplicationEventMulticaster();`初始化事件派发器
>
>    1. `beanFactory = getBeanFactory();`获取`beanFactory`
>    2. 判断是否能从`BeanFactory`中获取id为`applicationEventMulticaster`的`ApplicationEventMulticaster`，如果没有则创建 一个`SimpleApplicationEventMulticaster`，并注册到`BeanFactory`中 。
>
> 9. `onRefresh();`留给子类，默认什么都不做，让子类重写这个方法，在容器刷新时自定义逻辑
>
> 10. `registerListeners();`给容器中注册所有定义的`ApplicationListener`
>
>     1. 从容器中拿到所有实现`ApplicationListener`的监听器名字放在`String[]`中
>     2. 将每个监听器添加到事件派发器中 `getApplicationEventMulticaster().addApplicationListenerBean(listenerBeanName);`
>     3. 派发早期注册的事件
>
> 11. `finishBeanFactoryInitialization(beanFactory);`初始化所有的剩下的单实例Bean
>
>     1. `beanFactory.preInstantiateSingletons();`
>
>        1. 获取容器中的所的Bean，依次进行初始化和创建对象。
>
>        2. 获取Bean的定义信息，判断bean是否不是抽象的，是单实例的，不是懒加载的
>
>           1. 判断是否是`FactoryBean`的实现类
>
>           2. 如果不是`FactoryBean`则用`getBean(beanName)`创建对象
>
>              1. 在`getBean(beanName)`中调用`doGetBean(name,null,null,false),`
>
>              2. 先判断是否可以从缓存中获取到以前创建的单实例bean，如果能获取到说明这个Bean是之前已经被创建过的，就不需要再创建，直接从缓存中获取
>
>              3. 如果缓存中没有，开始Bean的创建对象流程
>
>                 1. 标记当前bean已经被创建，防止被多个线程重复创建
>
>                 2. 获取Bean的定义信息
>
>                 3. 获取当前要创建的Bean所依赖的其他Bean，如果有则按照getBean()将所依赖的Bean先创建出来
>
>                 4. 判断要创建的Bean是否是单实例的，是则创建单实例Bean
>
>                    1. `createBean(benaName,mbd,args)`
>
>                    2. `Object bean = resolveBeforeInstantiation(beanName,mbdToUse)` 让`beanPostProcessor`先提前拦截返回代理对象
>
>                       【`InstantiationAwareBeanPostProcessor`】提前执行
>
>                       先触发 `postProcessBeforeInstantiation()`
>
>                       如果有返回值，触发`postProcessAfterInitialization()`
>
>                    3. 如果前面的`InstantiationAwareBeanPostProcessor`没有返回代理对象,调用4
>
>                    4. `Object beanInstance = doCreateBean(beanName,mbdToUse,args)`
>
>                       1. 创建Bean实例，`createBeanInstance(beanName,mbd,args)` 利用工厂方法或者对象的构造器创建Bean对象
>
>                       2. `applyMergedBeanDefinitionPostProcessors(mbd,beanType,beanName)`调用`MergedBeanDefinitionPostProcessor`的后置处理器
>
>                       3. `populateBean(beanName,mbd,instanceWrapper)` 为Bean属性赋值,在赋值之前执行一些后置处理器工作
>
>                          1. 拿到 `InstantiationAwareBeanPostProcessor`后置处理器执行 `postProcessAfterInstantiation()`
>                          2. 拿到 `InstantiationAwareBeanPostProcessor`后置处理器执行 `postProcessPropertyValues()`
>                          3. 应用Bean属性的值，为属性利用setter方法进行赋值 `applyPropertyValues(beanName,mbd,bw,pvs)`
>
>                          4`.initializeBean(beanName,exposedObject,mbd)` bean初始化
>
>                          1. `invokeAwareMethods(beanName,bean)`执行XXXAware接口的方法
>                          2. `applyBeanPostProcessorBeforeInitilization(wrappedBean,beanName)` 执行后置处理器初始化之前的方法 `BeanPostProcessor.postProcessorBeforeInitialization()`
>                          3. `invokeInitMethods(beanName,wrappedBean,mbd)` 执行初始化方法
>                          4. `applyBeanPostProcessorAfterInitilization(wrappedBean,beanName)` 执行后置处理器初始化之后的方法 `BeanPostProcessor.postProcessorAfterInitialization()`
>                          5. 注册Bean的销毁方法
>
>                    5. 将创建的Bean添加到缓存中singletonObjects,ioc容器就是这些Map,所有Bean都利用getBean创建完成以后，检查所有的Bean是否是`SmartInitializingSingleton`接口的，如果是，执行`afterSingletonInstantiation`()方法
>
> 12. `finishRefresh();`完成BeanFactory的初始化创建工作，IOC容器就创建完成，
>
>     1. `initLifecycleProcessor();` 初始化和生命周期有关的后置处理器
>     2. `getLifecycleProcessor().onRefresh()`;获取前面定义的生命周期处理喊叫，回调onRefresh()
>     3. `publishEvent(new ContextRefreshedEvent(this))`;发布容器刷新完成事件
>     4. `LiveBeansView.registerApplicationContext(this);`

### 15.1 IOC创建总结

> 1. Spring容器在启动的时候，先会保存所有注册进行的Bean定义信息
>
>    * xml注册bean
>    * 注解注册bean:`@Componment,@Bean,@Service`
>
> 2. Spring容器会在适合的时机创建这些Bean
>
>    * 用到这个Bean的时候，利用getBean()创建，创建好以后就保存在容器中
>    * 统一创建剩下的所有单实例bean
>
> 3. 后置处理器
>
>    * 每一个bean创建完成，就会使用各种后置处理器进行处理，来增强bean的功能，比如
>        `AutowiredAnnotationBeanPostProcessor`:处理自动注入
>
>    ​              `AnnotationAwareAspectJAutoProxyCreator`:实现AOP功能
>
> 4. 事件驱动模型
>
>    * `ApplicationListener` :事件监听
>    * `ApplicaitonEventMulticaster`:事件派发

```java
@Override
	public void refresh() throws BeansException, IllegalStateException {
		synchronized (this.startupShutdownMonitor) {
			// Prepare this context for refreshing.
			prepareRefresh();

			// Tell the subclass to refresh the internal bean factory.
			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();

			// Prepare the bean factory for use in this context.
			prepareBeanFactory(beanFactory);

			try {
				// Allows post-processing of the bean factory in context subclasses.
				postProcessBeanFactory(beanFactory);

				// Invoke factory processors registered as beans in the context.
				invokeBeanFactoryPostProcessors(beanFactory);

				// Register bean processors that intercept bean creation.
				registerBeanPostProcessors(beanFactory);

				// Initialize message source for this context.
				initMessageSource();

				// Initialize event multicaster for this context.
				initApplicationEventMulticaster();

				// Initialize other special beans in specific context subclasses.
				onRefresh();

				// Check for listener beans and register them.
				registerListeners();

				// Instantiate all remaining (non-lazy-init) singletons.
				finishBeanFactoryInitialization(beanFactory);

				// Last step: publish corresponding event.
				finishRefresh();
			}
            catch(BeansException ex){
                .....
            }
```

## 16. Servlet3.0注解开发

### 16.1 @WebServlet

> 在servlet3.0以后，我们可以不用再web.xml里面配置servlet，只需要加上`@WebServlet`注解就可以修改该servlet的属性了。web.xml可以配置的servlet属性，在@WebServlet中都可以配置。
>
> `@WebServlet`属性列表：

| 属性名         | **类型**       | **描述**                                                     |
| -------------- | -------------- | ------------------------------------------------------------ |
| name           | String         | 指定Servlet 的 name 属性，等价于 <servlet-name>。如果没有显式指定，则该 Servlet 的取值即为类的全限定名。 |
| value          | String[]       | 该属性等价于 urlPatterns 属性。两个属性不能同时使用。        |
| urlPatterns    | String[]       | 指定一组 Servlet 的 URL 匹配模式。等价于<url-pattern>标签。  |
| loadOnStartup  | int            | 指定 Servlet 的加载顺序，等价于 <load-on-startup>标签。      |
| initParams     | WebInitParam[] | 指定一组 Servlet 初始化参数，等价于<init-param>标签。        |
| asyncSupported | boolean        | 声明 Servlet 是否支持异步操作模式，等价于<async-supported> 标签。 |
| description    | String         | 该 Servlet 的描述信息，等价于 <description>标签。            |
| displayName    | String         | 该 Servlet 的显示名，通常配合工具使用，等价于 <display-name>标签。 |

```java
/**拦截hello请求的Servlet*/
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {

        resp.getWriter().write("hello world");
    }
```

55
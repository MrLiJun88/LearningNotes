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
> * 使用JSR250中注解 `@PostConstruct` 在bean创建完成并且属性赋值后初始化
>
>   `@PreDestroy`在容器销毁`bean`之前。当类被注册到容器中时，会自动调用被
>
>   `@PostConstruct`标注的初始化方法，和在类被销毁时会自动被`@PreDestroy`标注的销毁方法
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
>   * 在配置类中通过@PropertySource注解加载到配置文件
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

28


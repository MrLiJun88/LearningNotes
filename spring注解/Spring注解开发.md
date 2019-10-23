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

>  从原码中可以看到，需要传入一个Class数组，并且需要继承Condition接口：
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

### 6.2 标注在方法上

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

### 6.3 多条件类

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

8
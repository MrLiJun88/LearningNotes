# Spring实战笔记

## 1. Spring容器

Spring自带了多个容器实现，可以归为两种不同的类型:

*  bean工厂(由`org.springframework.beans.factory.BeanFactory`接口定义)是最简单的容器，提供基本的DI支持。
* 应用上下文(由`org.springframework.context.ApplicationContext`接口定义)，基于`BeanFactory`构建，并提供应用框架级别的服务，例如从属性文件解析文本信息以及发布应用事件给感兴趣的事件监听者。

### 1.1 应用上下文

Spring自带了多种类型的应用上下文。下面罗列的几个是你最有可能遇到的。

* `AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。
* `AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载SpringWeb应用上下文。
* `ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
* `FileSystemXmlApplicationContext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。
* `XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。

### 1.2 bean的生命周期

在bean准备就绪之前，bean工厂执行了若干启动步骤:

1. Spring对bean进行实例化
2. Spring将值和bean的引用注入到bean对应的属性中
3. 如果bean实现了`BeanNameAware`接口，Spring将bean的ID传递给`setBeanName`()方法
4. 如果bean实现了`BeanFactoryAware`接口，Spring将调用`setBeanFactory`()方法，将`BeanFactory`容器实例传入
5. 如果bean实现了`ApplicationContextAware`接口，Spring将调用`setApplicationContext`()方法，将bean所在的应用上下文的引用传入进来
6. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessBeforeInitialization`()方法
7. 如果bean实现了`InitializingBean`接口，Spring将调用它们的`afterPropertiesSet`()方法。类似地，如果bean使用`init-Method`声明了初始化方法，该方法也会被调用
8. 如果bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessAfterInitialization`()方法
9. 此时，bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁
10. 如果bean实现了`DisposableBean`接口，Spring将调用它的`destroy`()接口方法。同样，如果bean使用`destroy-method`声明了销毁方法，该方法也会被调用。

![image-20200406202908568](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200406202908568.png)

### 1.3 Spring模块

![image-20200406204355658](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200406204355658.png)

## 2. Spring配置方案

**尽可能的使用自动配置，显式配置越少越好，当必须要显式配置bean时，推荐使用类型安全并且比XML更加强大的`JavaConfig`。只有在`JavaConfig`中没有同样的实时时，才应该使用XML**

* 在XML中进行显式配置
* 在Java中进行显式配置
* 隐式的bean发现机制和自动装配

### 2.1 自动装配bean

Spring从两个角度来实现自动化装配:

* 组件扫描(component scanning)：Spring会自动发现应用上下文中所创建的bean
* 自动装配(autowiring)：Spring自动满足bean之间的依赖

**组件扫描和自动装配组合在一起就能发挥出强大的威力，它们能够将你的显式配置降低到最少**。

###  2.2 @Componment

* 在类上使用了`@Componment`注解表明该类会作为组件类，并告知Spring要为这个类创建bean,Spring会根据类名为其指定一个ID，默认将类名第一个字母变为小写设置为ID，即Person的ID就是person。
* 可以显式为bean设置ID
  * ![image-20200410210312041](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410210312041.png)
  * 使用`@Named`注解为bean设置ID(Java依赖注入规范 Java Dependency Injection),不推荐
  * ![image-20200410210656917](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410210656917.png)



### 2.3 @ComponentScan

* 默认会以配置类所在包作为基础包(base package)来扫描组件，可以通过设置`basePackages`这个属性来让容器扫描指定包的组件
  * ![image-20200410212211797](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410212211797.png)
* 通过`basePackageClasses`属性来让容器扫描指定包的组件,**这些类所在的包将会作为组件扫描的基础包**
  * ![image-20200410212458338](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410212458338.png)

### 2.4 @Autowired

* `@Autowired`**注解可以在类的任何方法上使用。不管是构造方法，Setter方法还是其他方法**，Spring都会尝试满足方法参数上所声明的依赖。假如有且只有一个bean匹配依赖，那么这个bean将会被装配进行；**如果没有相匹配的bean，那么在应用上下文创建的时候，Spring会抛出一个异常。**可以将`@Autewired`的`required`属性设置为`false`来避免这个错误,但有可能出现`NullPointerException`异常
* ![image-20200410213631029](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410213631029.png)
* **如果有多个bean都能满足依赖关系,Spring将出抛出一个异常**，表明没有明确指定要选择哪个bean进行自动装配。
* `@Inject`注解来源于Java依赖注入规范，该规范同时还为我们定义了`@Named`注解。在自动装配中，Spring同时支持`@Inject`和`@Autowired`。

## 3. 显式装配bean

如果想要将第三方库中的组件装配到你的应用中，在这种情况下，是没有办法在它的类上添加`@Componment`和`@Autowired`注解的，因次你必须要显式装配的方式装配bean

### 3.1 使用JavaConfig

* 在进行显式配置时，JavaConfig是更好的方案，因为它更为强大、类型安全并且对重构友好，通常会将JavaConfig放到单独的包中，使它与其他应用程序逻辑分离开来。
* `@Bean`注解会告诉Spring这个方法将会返回一个对象，该对象要注册为Spring应用上下文中的bean。**默认情况下，bean的ID与带有@Bean注解的方法名一样。如果你想为其设置一个不同的名字，可以通过name属性指定一个不同的名字:**
  * ![image-20200410220049012](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410220049012.png)
* 借助JavaConfig实现注入,将bean所依赖的bean注入其中
  * ![image-20200410221512057](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410221512057.png)
  * **默认情况下，Spring中的bean都是单实例的**
  * ![image-20200410221756805](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200410221756805.png)
  * 通过这种方式引用其他的bean通常是最佳的选择，**因为它不会要求将bean都声明到同一个配置类中，它可以通过组件扫描或者XML来进行配置，你可以将配置分散到多个配置类中、XML文件以及自动扫描和装配bean中**。不管CompactDisc是采用什么方式创建出来的，Spring都会将其传入到配置方法中，并用来创建CDPlayer bean

### 3.2 使用XML

* 声明util-命名空间

  * ```xml
    xmlns:util="http://www.springframework.org/schema/util"
    ```

  * util-命名空间所提供的功能之一就是<util:list>元素

  * ```xml
    			  <util:list id="list">
                <value>hello</value>
                <value>world</value>
            </util:list>
    ```

  * ![image-20200411124114421](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200411124114421.png)

## 4. 混合配置

在自动装配时，Spring容器并不在意要装配的bean来自哪里。自动装配的时候会考虑到Spring容器中所有bean,不管它是在JavaConfig或XML中声明的还是通过组件扫描获取到的

### 4.1 在JavaConfig中引入XML配置

* 将一个JavaConfig中的bean注入到另一个JavaConfig中的bean,只要使用`@Import`注解导入即可,如下图`CompactDisc`类型的引用来自于`CDPlayerConfig`中的bean
* ![image-20200411125556330](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200411125556330.png)
* 如果bean是在XML文件中定义的，而JavaConfig想要使用到它，则可以使用`@ImportResource`注解
* ![image-20200411130218109](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200411130218109.png)

### 4.2 在XML配置中引用JavaConfig

* 如果一个XML文件需要引用到另一个XML文件中的bean时，可以使用`@Import`导入其他的XML文件,如下：sgtPeppers是来自于spring-mvc.xml中的bean

  * ```xml
    	<import resource="spring-mvc.xml"></import>
        <bean class="com.njcit.inter.CDPlayer">
            <constructor-arg ref="sgtPeppers"></constructor-arg>
        </bean>
    ```

* <import>元素只能导入其他的XML配置文件，并没有XML元素能够导入JavaConfig类的，但是可以用<bean>元素，将`JavaConfig`类导入到XML配置中

  * ```xml
        <!-- 将JavaConfig中的bean导入到xml中使用-->
        <bean class="com.njcit.config.CDPlayerConfig2"></bean>
        <bean class="com.njcit.inter.CDPlayer">
            <constructor-arg ref="sgtPeppers"></constructor-arg>
        </bean>
    ```

## 5. 高级装配

### 5.1 Spring profile

* Spring根据环境决定该创建哪个bean和不创建哪个bean。**不过Spring并不是在构建的时候做同这样的决策，而是等到运行时再来确定。这样的结果就是同一个部署单元(可能会是WAR文件)能够适用于所有的环境，没有必要进行重新构建。**

* 在JavaConfig中，可以使用`@Profile`注解指定某个bean属于哪一个profile**,如果@Profile注解应用在了类级别上，它会告诉Spring配置类中所有的bean只有在dev profile被激活时才会**，如果dev profile没有激活，那么带有`@Bean`注解的方法都会被忽略

  * ```java
    @Configuration
    @Profile("dev")
    public class CDPlayerConfig {
    
        @Bean
        public CDPlayer cdPlayer(){
            return new CDPlayer(sgtPeppers());
        }
    ```

* 在方法上使用`@Profile`,注意：只有当对应的profile被激活时，相应的bean才会被创建，但是可能其他的bean并没有声明任何profile范围。那么**没有指定profile的bean始终都会被创建，与激活哪个profile没有关系。**

  * ```java
    	   @Bean
        @Profile("dev")
        public CDPlayer cdPlayer(){
            return new CDPlayer(sgtPeppers());
        }
      
        @Bean
        @Profile("test")
        public CompactDisc sgtPeppers(){
            return new SgtPeppers();
        }
    ```

* 在XML中指定profile,可以通过<beans>元素的profile属性

  * ```xml
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans 		  http://www.springframework.org/schema/beans/spring-beans.xsd"
           profile="test">
    ```

* 可以在<beans>元素中签到定义<beans>元素，而不是为每个环境都创建一个profile XML文件。这样能够将所有的profile bean定义放到同一个XML文件中

  * ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
           xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://www.springframework.org/schema/beans 				   http://www.springframework.org/schema/beans/spring-beans.xsd">
        
        <!-- 测试环境下的bean-->
        <beans profile="test">
           <bean id="sgtPeppers" class="com.njcit.inter.SgtPeppers"></bean>
        </beans>
        
        <!-- 开发环境下的bean-->
        <beans profile="dev">
            <bean id="hello" class="com.njcit.inter.SgtPeppers"></bean>
        </beans>
    </beans>
    ```

#### 5.1.1 激活profile

* Spring在确定哪个profile处于激活状态时，需要依赖两个独立的属性:`spring.profiles.active`和`spring.profiles.default`。如果设置了actice属性的话，那么它的值就会用来确定哪个profile是激活的。但如果没有设置spring.profiles.active属性的话，那Spring将会查找default的值，如果active和default都没有设置的话，那就没有激活的profile，因此只会创建那些没有定义在profile中的bean

* 有多种方式来设置这两个属性

  * 作为DispatcherServlet的初始化参数
  * 作为Web应用的上下文参数
  * 作为JDNI条目
  * 作为环境变量
  * 作为JVM的系统属性
  * 在集成测试类上，使用@ActiveProfiles注解设置

* `@ActiveProfiles` 在测试环境下指定激活哪个profile

  * ```java
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes = CDPlayerConfig.class)
    //在测试时激活test环境下的所有bean
    @ActiveProfiles("test")
    public class CDPlayerTest {
    ```

### 5.2 条件化的bean声明 

* `@Conditional` 注解可以用到带有@Bean注解的方法上。如果给定的条件计算结果为true,就会创建这个bean，否则的话，这个bean会被忽略。

* `@Profile`本身也使用了`@Conditional`注解，并且引用`ProfileCondition`作为`Condition`实现

  * ```java
    @Target({ElementType.TYPE, ElementType.METHOD})
    @Retention(RetentionPolicy.RUNTIME)
    @Documented
    @Conditional(ProfileCondition.class)
    public @interface Profile {
    	String[] value();
    }
    ```

### 5.3 自动装配与歧义性

* 仅有一个bean匹配所需的结果进，自动装配才是有效的。如果不仅有一个bean能够匹配结果的话，这种歧义性会阻碍Spring自动装配属性、构造器参数或方法参数。

* `@Primary`注解，当出现多个类型匹配的bean时，会有限装配含有@Primary注解的bean,用于类或方法上**,但当多个bean上标注了`@Primary`注解时就无法正常工作了**

  * ```java
    	@Bean
        @Primary
        public CDPlayer cdPlayer(CompactDisc compactDisc){
            return new CDPlayer(compactDisc);
        }
    ```

  * ```xml
    <!-- 在XML文件上设置primary属性-->
    <bean class="com.njcit.config.CDPlayerConfig2" primary="true"></bean>
    ```

* `@Qualifier`注解是使用限定符的主要方式。它可以与`@Autowired`和`@Inject`协同使用，在注入的时候指定想要注入进去的是哪个bean

* 

### 5.4 bean的作用域

* **在默认情况下，Spring应用上下文中所有的bean都是作为单例的形式创建的**。也就是说，不管给定的一个bean被注入到其他bean多少次，每次所注入的都是同一个实例。

* Spring定义了多种作用域，可以基于这些作用域创建bean

  * 单例(Singleton): 在整个应用中，只创建bean的一个实例
  * 原型(Prototype): 每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的bean实例
  * 会话(Session): 在Web应用中，为每个会话创建一个bean实例
  * 请求(Request): 在Web应用中，为每个请求创建一个bean实例

* 如果要选择其他作用域，要使用`@Scope`注解，它可以与`@Component`或`@Bean`一起使用,如下列：你也可以使用`@Scope("prototype")`,但使用`ConfigurableBeanFactory.SCOPE_PROTOTYPE`常量更加安全并且不易出错。

  * ```java
    	//设置bean的作用域为prototype
      	@Bean
        @Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
        public CDPlayer cdPlayer(CompactDisc compactDisc){
            return new CDPlayer(compactDisc);
        }
    ```

  * ```xml
    <!-- 在XML文件中设置bean的作用域-->
    <bean class="com.njcit.config.CDPlayerConfig2" scope="prototype"></bean></bean>
    ```

* 使用会话和请求作用域,比如在一个Web应用上，一个实现购物车的bean来说，会话作用域是最为合适的。如下：

  * ```java
     	@Bean
        @Scope(value = WebApplicationContext.SCOPE_SESSION,
        proxyMode = ScopedProxyMode.INTERFACES)
        public CDPlayer shoppingCard(CompactDisc compactDisc){
            return new CDPlayer(compactDisc);
        }
    ```

  * 我们将value设置成`WebApplicatinoContext`中的`SCOPE_SESSION`常量（它的值是session）。这会告诉Spring为Web应用中的每一个会话创建一个`shoppingCard`。这会创建多个`shoppingCard`的实例，**但对于给定的会话只会创建一个实例，在当前会话相关的操作中，这个bean实际上相当于单例的。**

  * 注意：`@Scope`同时还有一个`proxyMode`属性，它被设置成了`ScopedProxyMode.INTERFACES`。这个属性解决了将会话或请求作用域的bean注入到单例bean中所遇到的问题。如下例：

  * ```java
    @Component
    public class StoreService{
        @Autowired
        public void setShopping(ShoppingCart shoppingCart){
            this.shoppingCart = shoppingCart;
        }
    }
    ```

  * Spring并不会将实际的`Shopping Cart bean`注入到`StoreService`中，Spring会注入一个到`ShoppingCart bean`的代理。这个代理会暴露与`ShoppingCart`相同的方法，所以`StoreService`会认为它就是一个购物车。但是，当`StoreService`调用`ShoppingCart`的方法时，代理会对其进行懒解析并将调用委托给会话作用域内真正的`ShoppingCart bean`。

  * `proxyMode = ScopedProxyMode.INTERFACES`这表明这个代理要实现ShoppingCart接口，并将调用委托给实现的bean；如果ShoppingCart是接口而不是类的话，这是可以的（也是最理想的代理模式）。**但如果ShoppingCart是一个具体的类的话，Spring就没有办法创建基于接口的代理了。它必须使用CGLib来生成基于类的代理**。所以bean类型是具体类的话，我们必须要将`proxyMode`属性设置为`ScopedProxyMode.TARGET_CLASS`

  * ![image-20200414214933376](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200414214933376.png)

#### 5.4.1 在XML中声明作用域代理

* `<aop:scoped-proxy/>`是与`@Scope`注解的`proxyMode`属性功能相同的Sping XML配置元素。它会告诉Spring为bean创建一个作用域代理。**默认情况下，它会使用CGLib创建目标类的代理，但我们也可以将proxy-target-class属性设置成false，进而要求它生成基于接口的代理**

  * ```xml
     	  <bean class="com.njcit.config.CDPlayerConfig2" scope="prototype">
            <aop:scoped-proxy proxy-target-class="false"/>
        </bean>
    ```

### 5.5 Spring的表达式语言

#### 5.5.1 运行时值注入

* 属性占位符(Property placeholder)

  * Spring一直支持属性定义到外部的属性的文件中，并使用占位符值将其插入到Spring bean中，在Spring装配中，占位符的形式为 `${}`,我们可以在XML中按照方式声明bean属性值 

  * ```xml
    <context:property-placeholder location="jdbc.properties"/>
    
    	<bean id="sgtPeppers2" class="com.njcit.inter.SgtPeppers"
              c:_0="${jdbc.username}" c:_1="${jdbc.password}">
        </bean>
    ```

  * 在JavaConfig中使用`@Value`注解代替占位符,为了使用占位符号，我们必须配置一个`PropertyResourcesPlaceholderConfigurer` bean

  * ```java
    	@Bean
        public static PropertySourcesPlaceholderConfigurer placeholderConfigurer(){
            return new PropertySourcesPlaceholderConfigurer();
        }
    ```

  * 

* Spring表达式语言(SpEL)

* SpEL拥有很多特性，包括

  * 使用bean的ID来引用bean
  * 调用方法和访问对象的属性
  * 对值进行算术、关系和逻辑运算
  * 正则表达式匹配
  * 集合操作

* **SpEL表达式要放到 #{} 之中，而属性占位符需要放到 ${} 中**

## 6. AOP

* **DI能够将组件及其协作的其他组件解耦，AOP则有助于将应用组件与跨多个组件的任务进行解耦**

## 7. 面向切面的Spring

* 面向切面编程的基本原理
  * 通知：切面的工作被称为通知。**通知定义了切面是什么以及何时使用。除了描述切面要完成的工作，通知还解决了何时执行这个工作问题**。它应该应用在某个方法被之前？之后？还是只有方法出现异常时调用
  
    * Spring切面可以应用5种类型的通知
  
    1. 前置通知(Before)：在目标方法被调用之前调用通知功能
    2. 后置通知(After)：在目标方法完成之后调用通道，此时不会关心方法的输出是什么
    3. 返回通知(After-returning)：在目标方法成功执行之后调用通知
    4. 异常通知(After-throwing)：在目标方法抛出异常后调用通知
    5. 环绕通知(Around)：通知包裹了被通知的方法，在被通知的方法调用之前和调用之后执行自定义的行为
  
  * 连接点(Join point)：在应用执行过程中能够插入切面的一个点。这个点可以是调用方法时，抛出异常进，甚至修改一个字段时。切面代码可以利用这些点插入到应用的正常流程中，并添加新的行为。
  
  * 切点(Poincut)：切点的定义会匹配通知所要织入的一个或多个连接点。我们通常使用明确的类和方法名称，或是利用正则表达式定义所匹配的类和方法名称来指定这些切点
  
  * 切面(Aspect)：切面是通知和切点的结合。通知和切点共同定义了切面的全部内容。它是什么，在何时和何处完成
  
  * 引入(Introduction)：引入允许我们向现有的类添加新方法或属性
  
  * 织入(Weaving)：把切面应用到目标对象并创建新的代理对象的过程
  
  * Spring提供了4种类型的AOP支持
  
    * 基于代理的经典SpringAOP
    * 纯POJO切面
    * @AspectJ注解驱动的切面
    * 注入式AspectJ切面(适用于Spring各版本)
  
  * Spring在运行时通知对象，**Spring在运行期把切面织入到Spring管理的bean，并为其创建代理对象**
  
  * Spring只支持方法级别的连接点，而不支持对属性和构造方法的连接点
* 通过POJO创建切面
* 使用@AspectJ注解
* 为AspectJ切面注入依赖

### 7.1 定义切点

![image-20200416221637341](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200416221637341.png)

201
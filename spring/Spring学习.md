# Spring学习

## 1.Spring框架模块

![核心](C:\Users\Administrator\Desktop\spring\images\核心.JPG)

## 2.Spring核心容器

* spring-context
* spring-beans
* spring-core
* spring-expression
* spring运行的时候依赖日志包(commons-logging)，没有则会报错

## 3.IOC与AOP

> IoC（Inversion of  Control）就是由容器控制程序之间的关系，而非传统实现中，由程序代码直接操控。这也就是所谓“控制反转”的概念所在。控制权由应用代码中转到了外部容器，控制权的转移是所谓反转。IoC还有另外一个名字——“依赖注入（Dependency   Injection）”。从名字上理解，所谓依赖注入，即组件之间的依赖关系由容器在运行期决定，形象地说，即由容器动态地将某种依赖关系注入到组件之中。
>
>  
>
> IoC是近年来兴起的一种思想，不仅仅是编程思想。主要是协调各组件间相互的依赖关系，同时大大提高了组件的可移植性，组件的重用机会也变得更多。在传统的实现中，由程序内部代码来控制程序之间的关系。我们经常使用new关键字来实现两组键间关系的组合，这种实现的方式会造成组件之间耦合(一个好的设计，不但要实现代码重用，还要将组件间关系解耦)。IoC很好的解决了该问题，它将实现组件间关系从程序内部提到外部容器来管理。也就是说由容器在运行期将组件间的某种依赖关系动态的注入组件中。控制程序间关系的实现交给了外部的容器来完成。
>
>  
>
> IoC的实现形式有两种：
>
> ​     依赖查找：容器提供回调接口和上下文环境给组件。EJB和Apache Avalon都是使用这种方式。     
>
> ​     依赖注入：组件不做定位查询，只是提供普通的Java方法让容器去决定依赖关系。容器全权负责组件的装配，它会把符合依赖关系的对象通过JavaBean属性或者构造子传递给需要的对象。
>
> ​          通过JavaBean属性注射依赖关系的做法称为设值方法注入（Setter Injection）；
>
> ​          将依赖关系作为构造子参数传入的做法称为构造子注入（Constructor Injection）。
>
> 
>
> AOP为Aspect Oriented Programming的缩写，意为：面向方面编程，可以通过预编译方式和运行期动态代理实现在不修改源代码的情况下给程序动态统一添加功能的一种技术。
>
>  
>
> AOP主要应用于日志记录，性能统计，安全控制,事务处理等方面。
>
>  
>
> 1、AOP相关概念：
>
> ​      1）方面（Aspect）：一个关注点的模块化，这个关注点实现可能另外横切多个对象。事务管理是J2EE应用中一个很好的横切关注点例子。方面用Spring的Advisor或拦截器实现。
>
> ​      2）切入点（Pointcut）：指定一个通知将被引发的一系列连接点的集合。
>
> ​      3）连接点（Joinpoint）：程序执行过程中明确的点，如方法的调用或特定的异常被抛出。
>
> ​      4）通知（Advice）：在特定的连接点，AOP框架执行的动作。
>
> ​      5）目标对象（Target Object）：包含连接点的对象，也被称作被通知或被代理对象。
>
> ​      6）AOP代理（AOP Proxy）：AOP框架创建的对象，包含通知。在Spring中，AOP代理可以是JDK动态代理或CGLIB代理。
>
> ​      7）引入（Introduction）：添加方法或字段到被通知的类。Spring允许引入新的接口到任何被通知的对象。
>
> ​      8）编织（Weaving）：组装方面来创建一个被通知对象。
>
> 
>
> 2、Java领域的AOP实现有多种方案，主要包括：
>
> ​      1）AspectJ (TM)：创建于Xerox PARC. 有近十年历史，技术成熟。但其过于复杂；破坏封装；而且需要专门的Java编译器，易用性较差。
>
> ​      2）动态代理AOP：使用JDK提供的动态代理API或字节码Bytecode处理技术来实现。基于动态代理API的具体项目有： JBoss 4.0 JBoss 4.0服务器。
>
> ​      3）基于字节码的AOP，例如：Aspectwerkz，　CGlib， Spring等。

![ioc](C:\Users\Administrator\Desktop\spring\images\ioc.JPG)

## 4.IOC部分

### 4.1 通过p名称空间为bean赋值

> 通过p 名称空间为bean赋值
>
> 在applicationContext.xml中导入p名称空间

```xml-dtd
xmlns:p="http://www.springframework.org/schema/p"
```

```xml
    //使用名称空间为bean对象赋值
    <bean id="person" class="entity.Person"
      p:name="wangwu" p:age="55" p:gender="false">
    </bean>
```

### 4.2 为特殊类型赋值

````xml
       <property name="list">
            <list>
                <value>beijing</value>
                <value>shanghai</value>
                <value>xian</value>
            </list>
        </property>
        <property name="map">
            <map>
                <entry key="key1">
                    <value>entry1</value>
                </entry>
                <entry key="key2">
                    <value>entry2</value>
                </entry>
                <entry key="key3">
                    <value>entry3</value>
                </entry>
            </map>
        </property>
        <property name="properties">
            <props>
                <prop key="p1">pp1</prop>
                <prop key="p2">pp2</prop>
                <prop key="p3">pp3</prop>
            </props>
        </property>
````

### 4.3 使用util名称空间创建可以给外部引用的集合

> 导入名称空间

   `xmlns:util="http://www.springframework.org/schema/util"
  http://www.springframework.org/schema/util
  http://www.springframework.org/schema/util/spring-util-4.0.xsd"`

> 配置外部map,或者list,set,properties...

```xml
    <util:map id="myMap">
        <entry key="k1">
            <value>value1</value>
        </entry>
        <entry key="k2">
            <value>value2</value>
        </entry>
    </util:map>
```

### 4.4 使用util名称空间创建可以给外部引用的集合

> 修改了Person中引用对象dog的值

```xml
    <bean id="person" class="entity.Person">
        <property name="name" value="lijun"/>
        <property name="id" value="22"/>
        <property name="dog" ref="dog"/>
        <property name="dog.dogName" value="zhangsan"/>
    </bean>
```

### 4.5 通过继承实现bean配置信息的重用

> 当配置的两个bean只有各别信息不同时，可以设置 parent属性
>
> 这样没有声明的属性，就会从parent指定的bean配置中获取,避免了重复定义。

```xml
    <bean id="person2" class="entity.Person" parent="person">
        <property name="name" value="wmy"/>
    </bean>
```

### 4.6 声明一个抽象的bean

> 以下配置表示该bean是一个抽象的bean，不能获取实例，只能被其他bean继承

```xml
    <bean id="person" class="entity.Person" abstract="true">
        <property name="name" value="lijun"/>
        <property name="id" value="22"/>
    </bean>
```

### 4.7 声明bean之间的依赖关系(仅用于改变bean的创建顺序)

> 以下配置会在person创建之前，先根据depends-on中定义的属性先创建dog,person2,改变了原来按配置文件从上往下的创建顺序。

```xml
<bean id="person" class="entity.Person" depends-on="dog,person2">
    <property name="name" value="lijun"/>
</bean>
```

### 4.8 bean的作用域

> spring中bean的作用域默认是单实例(singleton)
>
> 在容器启动时单实例的bean就已经被创建成功，保存在容器中。
>
> 而多实例的bean，容器启动默认不会去创建，只有在获取时才会被创建。
>
> 
>
> * singleton:使用该属性定义Bean时，IOC容器仅创建一个Bean实例，IOC容器每次返回的是同一个Bean实例。
> * prototype:使用该属性定义Bean时，IOC容器可以创建多个Bean实例，每次返回的都是一个新的实例。
> * request:该属性仅对HTTP请求产生作用，使用该属性定义Bean时，每次HTTP请求都会创建一个新的Bean，适用于WebApplicationContext环境。
> * session:该属性仅用于HTTP Session，同一个Session共享一个Bean实例。不同Session使用不同的实例。
> * global-session:该属性仅用于HTTP Session，同session作用域不同的是，所有的Session共享一个Bean实例。

### 4.9 静态工厂与实例工厂

#### 4.9.1 静态工厂方法

```java
//创建静态工厂
public class StaticFactory {
    public static Person getPersonInstance(String name){
        System.out.println("调用静态工厂方法...");
        Person person = new Person();
        person.setName(name);
        person.setId(005);
        return person;
    }
}
```

```xml
<!-- 配置静态bean
  factory-method：指定哪个方法为静态工厂方法
  <constructor-arg>：为方法中的参数传参
-->
<bean id="staticFactory" class="factory.StaticFactory" factory-method="getPersonInstance">
       <constructor-arg name="name" value="ljun"/>
   </bean>
```

#### 4.9.2 实例工厂方法

```java
//创建实例工厂类
public class InstanceFactory {
    public Person getPersonInstance(String name){
        System.out.println("调用实例工厂方法...");
        Person person = new Person();
        person.setName(name);
        person.setId(005);
        return person;
    }
}
```

```xml
    <!-- 声明实例工厂bean-->
    <bean id="instanceFactory" class="factory.InstanceFactory">
    </bean>
   <!-- 声明bean对象，
        factory-bean:表示指定用哪个bean工厂来创建对象
        factory-method:表示指定用工厂中的哪个方法来创建实例对象
        <constructor-arg>:为方法传送参数
   -->
    <bean id="personFac" class="entity.Person" 
          factory-bean="instanceFactory" 
          factory-method="getPersonInstance">
        <constructor-arg name="name" value="wangwu"></constructor-arg>
    </bean>
```

### 4.10 FactoryBean

> FactoryBean是一个工厂Bean，可以生成某一个类型Bean实例，它最大的一个作用是：可以让我们自定义Bean的创建过程。
>
> 使用FactoryBean创建的对象实例，不管是单实例还是多实例，都是在获取的时候才创建对象。

```java
public interface FactoryBean<T> {
    //返回的对象实例
    T getObject() throws Exception;
    //Bean的类型
    Class<?> getObjectType();
    //true是单例，false是非单例  在Spring5.0中此方法利用了JDK1.8的新特性变成了default方法，返回true
    default boolean isSingleton() {
        return true;
    }
}
```

```java
//实现我们自己的工厂bean
public class MyFactoryBean implements FactoryBean<Person> {
    @Override
    public Person getObject() throws Exception {
        System.out.println("使用FactoryBean自动创建对象");
        Person person = new Person();
        person.setName("hahaha");
        person.setId(9);
        return person;
    }

    @Override
    public Class<?> getObjectType() {
        return Person.class;
    }
}
```

```xml
    <!-- 配置工厂bean-->
    <bean id="myFactoryBean" class="factory.MyFactoryBean"></bean>
```

### 4.11 创建带有生命周期的bean实例

> Bean的生命周期
>
> 1-2：创建实例；
>
> 　　现在假设spring就是个容器，而配置文件中配置的bean属性才是我们真正需要的东西。创建实例就是说，我把配置文件中的bean信息取出来化作一个真正的bean并放到容器中。
>
> 3-4:注入依赖关系；
>
> 　　第3步是创建实例之后对实例作了一些处理，第4步是把xml中配置的bean属性值赋予给容器中的实例化之后的bean。
>
> 5：bean初始化之前的处理；
>
> 　　应用开发者需要把容器中实例化的bean拿出来用，这个拿出来的过程就是初始化（注意实例化与初始化的区别，instantiation  和initialization，分得清吗？英语没学好怪我咯？），第五步就是在初始化之前，对已经实例化的bean再作一定的处理。
>
> 6,7：初始化。
>
> 　　如果bean实现了InitializingBean，那么将调用InitializingBean的afterPropertiesSet()方法做一些初始化处理。如果没有实现InitializingBean，而是在配置文件中定义了init-method属性值，那么系统会找到init-method对应的方法并执行之，程序猿哥哥一般在这个方法里写一些初始化操作；
>
> 8：bean初始化之后的处理。
>
> 　　初始化之后在这个方法中再对bean进行修饰装点。
>
> 9,10：交给应用开发人员处理；
>
> 　　如果在<bean>中指定Bean的作用范围是scopt="prototype",那么系统将bean返回给调用者，spring就不管了（如果两个实例调用的话，每一次调用都要重新初始化，一个实例的修改不会影响另一个实例的值。如果指定Bean的作用范围是scope="singleton"，则把bean放到缓冲池中，并将bean的引用返回给调用者。这个时候，如果两个实例调用的话，因为它们用的是同一个引用，任何一方的修改都会影响到另一方。）
>
> 11.bean用完之后；
>
> 　　对于scope="singleton"的bean，使用完之后spring容器会做一些处理，比如编写释放资源、记录日志等操作。
>
> 12.销毁；
>
> 　　调用配置文件中的销毁方法销毁实例。

![生命周期](C:\Users\Administrator\Desktop\spring\images\生命周期.jpg)

```xml
    <!-- 
       init-method:表示在对象创建的时间会调用的方法，如果声明的bean是单例，则在
容器启动的时候就会被调用
       destroy-method:在对象被销毁的时候会被调用
    -->
    <bean id="person" class="entity.Person" init-method="myInit"
       destroy-method="myDestory">
        <property name="id" value="1"/>
        <property name="name" value="lijun"/>
    </bean>
```

### 4.12 bean的后置处理器

> 该接口我们也叫后置处理器，作用是在每个Bean对象在实例化和依赖注入完毕后，在显示调用初始化方法的前后添加我们自己的逻辑。
>
> 且无论bean中是否有配置初始化方法，配置好的后置处理器一样工作。
>
> 源码如下：

```java
public interface BeanPostProcessor {
    //实例化、依赖注入完毕，在调用显示的初始化之前完成一些定制的初始化任务
    @Nullable
    default Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }

    //实例化、依赖注入、初始化完毕时执行
    @Nullable
    default Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        return bean;
    }
}
```

```java
/**
 * 定义自己的后置处理器
 * BeanPostProcessor接口的作用是：
 *   我们可以通过该接口中的方法在bean实例化、配置以及其他初始化方法前后添加一些我们自己的逻辑
 */
public class MyBeanPostProcessor implements BeanPostProcessor {

    /**
     * 实例化、依赖注入完毕，在调用显示的初始化之前完成一些定制的初始化任务
     * 注意：方法返回值不能为null
     * 如果返回null那么在后续初始化方法将报空指针异常或者通过getBean()方法获取不到bena实例对象
     * 因为后置处理器从Spring IoC容器中取出bean实例对象没有再次放回IoC容器中
     */
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("before--实例化的bean对象:"+bean+"\t"+beanName);
        // 可以根据beanName不同执行不同的处理操作
        return bean;
    }

    /**
     * 实例化、依赖注入、初始化完毕时执行
     * 注意：方法返回值不能为null
     * 如果返回null那么在后续初始化方法将报空指针异常或者通过getBean()方法获取不到bena实例对象
     * 因为后置处理器从Spring IoC容器中取出bean实例对象没有再次放回IoC容器中
     */
    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("after...实例化的bean对象:"+bean+"\t"+beanName);
        // 可以根据beanName不同执行不同的处理操作
        return bean;
    }
}
```

```xml
    <bean id="person" class="entity.Person" init-method="myInit"
       destroy-method="myDestory">
        <property name="id" value="1"/>
        <property name="name" value="lijun"/>
    </bean>

    <bean id="dog" class="entity.Dog">
        <property name="dogName" value="wangcai"/>
        <property name="dogAge" value="3"/>
    </bean>
    <!-- 注册处理器-->
    <bean class="processor.MyBeanPostProcessor"></bean>
```

```tex
//运行结果如下：
before--实例化的bean对象:Person{id=1, name='lijun', dog=null}	person
Person的 init()
after...实例化的bean对象:Person{id=1, name='lijun', dog=null}	person
before--实例化的bean对象:Dog{dogName='wangcai', dogAge=3}	dog
after...实例化的bean对象:Dog{dogName='wangcai', dogAge=3}	dog
```

### 4.13 Spring管理数据库连接池

```xml
<!-- 配置数据库连接池-->
  <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
      <property name="user" value="root"/>
      <property name="properties" value="root"/>
      <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/test"/>
      <property name="driverClass" value="com.mysql.jdbc.Driver"/>
  </bean>
```

### 4.14 引用外部属性文件

> 在applicationContext.xml中引用外部的数据库配置文件db.properties
>
> 引入名称空间

`xmlns:context="http://www.springframework.org/schema/context"`

```xml
   <!-- 引入外部文件-->
   <context:property-placeholder location="classpath:db.properties"/>
   <!-- 从外部文件中取值-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
        <property name="jdbcUrl" value="${db.url}"/>
        <property name="driverClass" value="${db.driver}"/>
    </bean>
```

### 4.15 基于XML的自动装配

> autowire='default' :不自动装置，spring默认设置
>
> * no : 不使用自动装配
> * byName ：以bean中定义的属性名称查找在xml文件中与属性名称相同的id作为值引用
> * byType ：以属性的类型查找xml中属性相同的bean作为引用，若有多个类型匹配，则会抛异常
> * constructor：根据构造方法进行赋值
>                    * 先按照有参构造器的中的类型进行装配，没有则会装配null
>                    * 如果匹配的类型有多个，则以参数的名为id继续匹配,找不到则装配null

### 4.16 SpEL测试 #()

> Spring 表达式语言 Spring Expression Language（简称 SpEL ）是一个支持运行时查询和操作对象图的表达式语言 。 语法类似于 EL 表达式 ，但提供了显式方法调用和基本字符串模板函数等额外特性 。

```xml
   <bean id="person" class="entity.Person">
        <!-- 引用其他bean的某个属性-->
        <property name="name" value="#{dog.dogName}"/>
        <!-- 获取字面量值-->
        <property name="id" value="#{11*12}"/>
        <!-- 引用其他bean对象-->
        <property name="dog" value="#{dog}"/>
        <!-- 调用静态方法
        #{T(全类名).静态方法名(arg1,arg2)}
        -->
        <property name="email" value="#{T(java.util.UUID).randomUUID().toString()}"/>
        <!-- 调用非静态方法 #{ id.方法名}-->
        <property name="address" value="#{dog.getDogName()}"/>
    </bean>
```

### 4.17 通过注解创建 Dao,Service,Controller

> * @Controller ：给控制器层添加的注解
> * @Service ：给业务逻辑层的注解
> * @Repository ：给dao层添加的注解
> * @Component ：给不属于以上层的组件的注解 (WebUtils)
>
> 某个类添加以上任何一个注解，这个类都会自行添加到IOC容器中

### 4.18 使用注解添加到IOC容器中的步骤

> * 给需要添加到容器的组件添加以上注解
> * 开始自动扫描，依赖 context 名称空间
> * 使用注解一定要导入aop包，支持加注解模式
> * 级件的id就是类名的首字母小写，默认的作用域是单例

```xml
<!-- 
   会把 base-package="com.njcit" 指定包下的所有有注解的类，添加到IOC容器中
-->
<context:component-scan base-package="com.njcit"/>
```

### 4.19 修改注解组件的默认行为

```java
/**
  将PersonDao在IOC容器中的id名改为 hello
  将作用域改为 prototype
*/
@Repository("hello")
@Scope(value = "prototype")
public class PersonDao {
}
```

### 4.20 扫描注解的时候可以排除哪些注解

> * annotation : 按照注解排除
> * aspectj ： aspectj表达式
> * assignable ：按照类排除
> * custom ：自定义一个TypeFilter,自己写代码选挪
> * regex ： 正则表达式

```xml
    <!-- 
      type="annotation : 排除规则，按照注解排除哪些注解不用添加到容器中
      expression="注解的全类名“
    -->
    <context:component-scan base-package="com.njcit">
        <context:exclude-filter 
                 type="annotation"     
                 expression="org.springframework.stereotype.Repository"/>
    </context:component-scan>
```

### 4.21 扫描注解的时候只添加哪些注解

> 因为spring默认是添加base-package下的所有有注解的包，如果要只添加指定的某个注解
>
> 需要禁用掉spring的默认添加行为，即 `use-default-filters="false"`

```xml
    <context:component-scan base-package="com.njcit" use-default-filters="false">
        <context:include-filter 
                  type="annotation" 
                  expression="org.springframework.stereotype.Controller"/>
    </context:component-scan>
```

### 4.22 @Autowired自动赋值

> @Autowired原理：
>
> ​    1): 先按照类型去容器中找到对应的组件
>
> ​               1:找到一个，就直接赋值
>
> ​               2:没有找到，则抛出异常
>
> ​               3:找到多个的情况下，再以要装配属性名作为id继续匹配
>
> ​                       1:匹配上，直接赋值
>
> ​                       2:没有匹配上，则继续报错
>
> ​                4:@Qualifier():指定一个名作为 id, 让spring不使用变量名作为id匹配
>
> ​                         1:找到，直接赋值
>
> ​                          2:找不到，报错
>
> ​    2):Autowired 默认一定会找到，找不到则抛出异常
>
> ​    3):设置Autowired找不到则自动装配为 null  `@Autowired(required = false)`

### 4.23 在方法上使用@Autowired

```java
    /**
     * 在方法上使用@Autowired
     * 这个方法会在bean创建的时候自动运行
     * 这个方法上的每一个参数都会自动注入值 
     */
    @Autowired
    public void save2(PersonDao personDao){
        System.out.println("在方法上使用自动装配");
        personDao.savePerson();
    }
```

### 4.24 @Autowired与@Resource的区别

> @Resource和@Autowired都是做bean的注入时使用，其实@Resource并不是Spring的注解，它的包是javax.annotation.Resource，需要导入，但是Spring支持该注解的注入。
>
> 1、共同点
>
> 两者都可以写在字段和setter方法上。两者如果都写在字段上，那么就不需要再写setter方法。
>
> 2、不同点
>
> （1）@Autowired
>
> @Autowired为Spring提供的注解，需要导入包org.springframework.beans.factory.annotation.Autowired;只按照byType注入。
>
> @Autowired注解是按照类型（byType）装配依赖对象，默认情况下它要求依赖对象必须存在，如果允许null值，可以设置它的required属性为false。如果我们想使用按照名称（byName）来装配，可以结合@Qualifier注解一起使用。如下：
>
> ```java
> @Repository
> @Qualifier(value="hahah")
> public interface UserDao {
>     
> }
> 
> public class TestServiceImpl {
>  @Autowired
>  @Qualifier("userDao")
>  private UserDao userDao; 
> }
> ```

> （2）@Resource
>
> @Resource默认按照ByName自动注入，由J2EE提供，需要导入包javax.annotation.Resource。@Resource有两个重要的属性：name和type，而Spring将@Resource注解的name属性解析为bean的名字，而type属性则解析为bean的类型。所以，如果使用name属性，则使用byName的自动注入策略，而使用type属性时则使用byType自动注入策略。如果既不制定name也不制定type属性，这时将通过反射机制使用byName自动注入策略。
>
> @Resource装配顺序：
>
> * 如果同时指定了name和type，则从Spring上下文中找到唯一匹配的bean进行装配，找不到则抛出异常。
>
> * 如果指定了name，则从上下文中查找名称（id）匹配的bean进行装配，找不到则抛出异常。
>
> * 如果指定了type，则从上下文中找到类似匹配的唯一bean进行装配，找不到或是找到多个，都会抛出异常。
>
> * 如果既没有指定name，又没有指定type，则自动按照byName方式进行装配；如果没有匹配，则回退为一个原始类型进行匹配，如果匹配则自动装配。
>
> * @Resource的作用相当于@Autowired，只不过@Autowired按照byType自动注入。

### 4.25 Spring的单元测试

> 在pom.xml中配置sprng单元测试

```xml
       <!-- spring-test -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
```

```java
/**
   @RunWith:指定使用哪种驱动进行单元测试，默认使用的是junit
   ContextConfigureation:指定Srping配置文件位置
*/
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = "classpath:applicationContext.xml")
public class SpringTest {
```

### 4.26 泛型依赖注入

> 先声明两个父类：BaseService<T> 和 BaseDao<T>，建立引用关系，然后在BaseService中声明一个save方法，在方法中调用BaseDao中方法

```java
public class BaseService<T> {

    @Autowired
    BaseDao<T> baseDao;

    public void save(){
        baseDao.addObject();
    }
}
```

> 再分别创建其对应的子类：PersonService 和 PersonDao，并在继承父类时传入泛型类类型的变量Person，然后加上@Repository注解和@Service注解，交给Spring IOC容器管理。

```java
@Service
public class PersonService extends BaseService<Person> {

}

@Repository
public class PersonDao extends BaseDao<Person> {

    @Override
    public void addObject() {
        System.out.println("保存用户");
    }
}
```

> 当在单元测试中调用 `personService.save()`方法后，会输出 "保存用户"

## 5. AOP部分

> 在软件业，AOP为Aspect Oriented Programming的缩写，意为：面向切面编程，通过预编译方式和运行期动态代理实现程序功能的统一维护的一种技术。AOP是OOP的延续，是软件开发中的一个热点，也是Spring框架中的一个重要内容，是函数式编程的一种衍生范型。利用AOP可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

### 5.1 AOP的相关定义

> * `Aspect`（切面）： Aspect 声明类似于 Java 中的类声明，在 Aspect 中会包含着一些 Pointcut 以及相应的 Advice。
> * `Joint point`（连接点）：表示在程序中明确定义的点，典型的包括方法调用，对类成员的访问以及异常处理程序块的执行等等，它自身还可以嵌套其它 joint point。
> * `Pointcut`（切点）：表示一组 joint point，这些 joint point 或是通过逻辑关系组合起来，或是通过通配、正则表达式等方式集中起来，它定义了相应的 Advice 将要发生的地方
> * `Advice`（增强）：Advice 定义了在 `Pointcut` 里面定义的程序点具体要做的操作，它通过 before、after 和 around 来区别是在每个 joint point 之前、之后还是代替执行的代码。
> * `Target`（目标对象）：织入 `Advice` 的目标对象.。
> * `Weaving`（织入）：将 `Aspect` 和其他对象连接起来, 并创建 `Advice`d object 的过程

### 5.2 动态代理

> 原理区别：
>
> java动态代理是利用反射机制生成一个实现代理接口的匿名类，在调用具体方法前调用InvokeHandler来处理。
>
> 而cglib动态代理是利用asm开源包，对代理对象类的class文件加载进来，通过修改其字节码生成子类来处理。
>
>  1、如果目标对象实现了接口，默认情况下会采用JDK的动态代理实现AOP 
> 2、如果目标对象实现了接口，可以强制使用CGLIB实现AOP 
>
> 3、如果目标对象没有实现了接口，必须采用CGLIB库，spring会自动在JDK动态代理和CGLIB之间转换
>
> 如何强制使用CGLIB实现AOP？
>  （1）添加CGLIB库，SPRING_HOME/cglib/*.jar
>  （2）在spring配置文件中加入<aop:aspectj-autoproxy proxy-target-class="true"/>
>
> JDK动态代理和CGLIB字节码生成的区别？
>  （1）JDK动态代理只能对实现了接口的类生成代理，而不能针对类
>  （2）CGLIB是针对类实现代理，主要是对指定的类生成一个子类，覆盖其中的方法
>    因为是继承，所以该类或方法最好不要声明成final 

### 5.3 Spring实现AOP功能

> 在pom.xml 文件中配置

```xml
        <!-- spring-aop -->
        <dependency>
           <groupId>org.springframework</groupId>
          <artifactId>spring-aop</artifactId>
          <version>5.1.5.RELEASE</version>
         </dependency>
        <!-- spring-aspects -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <!-- aop增强包,即使目标对象没有实现任何接口，也能创建动态代理类-->
        <!-- cglib -->
        <dependency>
            <groupId>cglib</groupId>
            <artifactId>cglib</artifactId>
            <version>2.2.2</version>
        </dependency>
        <!-- aopalliance -->
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
        <!-- aspectjweaver -->
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.2</version>
        </dependency>
```

> 将目标类与切面类加入到IOC容器中
>
> 告诉Spring 哪个是切面类  (在切面类上加 @Aspect 注解即可 )
>
> 告诉Spring，切面类里面的每一个方法，在什么时候执行
>
> * @Before 在目标方法之前运行  前置通知
> * @After 在目标方结束之后运行   后置通知 
> * @AfterReturning  在目标方法正常返回之后   返回通知
> * @AfterThrowing  在目标方法抛出异常之后运行  异常通知
> * @Around 环绕通知
>
> 开启基于AOP注解的功能  `<aop:aspectj-autoproxy/>`
>
> 如下：定义的一个切面类

```java
/**
 * @Aspect :声明该类是一个切面类
 * @Component:让spring自动将该类加入到IOC容器中
 * @Before("execution(【方法访问修饰符】 方法返回类型 方法全类名(方法参数类型))")
 */
@Aspect
@Component
public class MyLogging {

    @Before("execution(public void com.njcit.cal.CalculatorImpl.*(int,int))")
    public static void methodStart(){
        System.out.println("代理方法执行前的操作");
    }

    @After("execution(public void com.njcit.cal.CalculatorImpl.*(int,int))")
    public static void methodEnd(){
        System.out.println("代理方法执行后的操作");
    }

    @AfterThrowing("execution(public void com.njcit.cal.CalculatorImpl.*(int,int))")
    public static void methodExce(){
        System.out.println("代理方法执行有异常的操作");
    }
}
```

### 5.4 切入点表达式写法

> 固定格式：
>
>   `@Before("execution(【方法访问修饰符】 方法返回类型 方法全类名(方法参数类型))")`
>
> 通配符：
>
> * *:匹配一个或多个字符，任意一个参数类型
> * ..  匹配任意多个参数类型 或  匹配任意多层路径 
>
> **任意公共方法的执行：**
>
> ```
> `execution（``public` `* *（..））`
> ```
>
> **任何一个名字以 set 开始的方法的执行：**
>
> ```
> `execution（* set*（..））`
> ```
>
> **AccountService 接口定义的任意方法的执行：**
>
> ```
> `execution（* com.xyz.service.AccountService.*（..））`
> ```
>
> **在 service 包中定义的任意方法的执行：**
>
> ```
> `execution（* com.xyz.service.*.*（..））`
> ```
>
> **在 service 包或其子包中定义的任意方法的执行：**
>
> ```
> `execution（* com.xyz.service..*.*（..））`
> ```

### 5.5 通知方法的执行顺序

> 正常执行下：
>
> * Before   -->  @After   ->  @AfterReturning
>
> 出现异常情况下
>
> * Before   -->  @After   ->  @AfterThrowing
>
> `代理方法执行前的操作
>  代理方法执行后的操作
>  代理方法参数返回前的操作`

### 5.6 在通知方法中获取目标方法的详细信息

> 只需要为通知方法的参数列表上写一个参数：JoinPoint jpinPoint :封装了当前目标方法的详细信息。

```java
    @Before("execution(public void com.njcit.cal.CalculatorImpl.*(int,int))")
    public static void methodStart(JoinPoint joinPoint){
        /**获取目标方法参数*/
        Object[] args = joinPoint.getArgs();
        System.out.println("目标方法的参数: "+ Arrays.asList(args));
        /**获取目标方法的签名*/
        Signature signature = joinPoint.getSignature();
        String name = signature.getName();
        System.out.println("目标方法的名称：" + name);
        /**获取目标方法类的类型*/
        System.out.println(signature.getDeclaringType());
        /**获取目标方法类的类型名*/
        System.out.println(signature.getDeclaringTypeName());
        System.out.println(signature.getModifiers());
        System.out.println("代理方法执行前的操作");
    }
```

> 上述执行结果：
>
>  `目标方法的参数: [1, 5]
> 目标方法的名称：div
> class com.njcit.cal.CalculatorImpl
> com.njcit.cal.CalculatorImpl
> 1
> 代理方法执行前的操作
> 调用div方法
> 0
> 代理方法执行后的操作
> 代理方法参数返回前的操作`

### 5.7 在通知方法中获取目标方法返回值与异常信息

> 定义返回值与异常信息对象，并且一定要告诉Spring才可以被识别使用

```java
 /**returning:指定方法参数中哪个作为目标方法返回值的接收*/
    @AfterReturning(returning = "object",value = "execution(public void com.njcit.cal.CalculatorImpl.*(int,int))")
    public static void methodReturning(Object object){
        System.out.println("代理方法参数返回前的操作");
    }

    /**throwing:指定目标方法抛出异常的接收对象*/
    @AfterThrowing(throwing = "exception",value = "execution(public void com.njcit.cal.CalculatorImpl.*(int,int))")
    public static void methodExce(Exception exception){
        System.out.println(exception.getMessage());
        System.out.println("代理方法执行有异常的操作");
    }
```

### 5.8  抽取可重用的切入点表达式

> * 随便声明一个没有实现的返回void的空方法
> * 给方法上标注@Pointcut注解

```java
   /**定义了一个切入点表达式的模版*/
  @Pointcut(value = "execution(public int com.njcit.cal.CalculatorImpl.*(int,int))")
    public void template(){

    }

    /**其他通知就可以重用这个模版*/
    @Before(value="template()")
    public static void methodStart(JoinPoint joinPoint){
```

### 5.9 环绕通知 

> @Around:是Spring中最强大的通知
>
> 就相当于直接使用Java中的反射方法
>
> Spring AOP的环绕通知和前置、后置通知有着很大的区别，主要有两个重要的区别：
>
> 1）目标方法的调用由环绕通知决定，即你可以决定是否调用目标方法，而前置和后置通知是不能决定的，它们只是在方法的调用前后执行通知而已，即目标方法肯定是要执行的。joinPoint.proceed()就是执行目标方法的代码。
>
> 2）环绕通知可以控制返回对象，即可以返回一个与目标对象完全不同的返回值。虽然这很危险，但是却可以做到。

```java
   /**就是相当于直接使用Java反射来处理 method.invoke(obj,args)*/
    @Around(value = "template()")
    public Object methodAround(ProceedingJoinPoint pjp) {
        /**获取目标方法中的参数*/
        Object[] args = pjp.getArgs();
        Object object = null;
        try {
            /**@Before*/
            System.out.println("目标方法执行前的前置通知...");
            object = pjp.proceed(args);
            /**@After*/
            System.out.println("目标方法执行后的后置通知...");

        } catch (Throwable throwable) {
            /**@AfterThrowing*/
            System.out.println("目标方法出现异常的异常通知...");
            throwable.printStackTrace();
        }
        /**反射调用后的返回值也返回回去*/
        /**@AfterReturning*/
        System.out.println("目标方法返回值返回前的通知...");
        return object;
    }
```

> 上述输出结果：
>
> `目标方法执行前的前置通知...
> 调用div方法
> 目标方法执行后的后置通知...
> 目标方法返回值返回前的通知...
> 结果: 8`

### 5.10 多个切面运行顺序

> 当有多个切面同时对一个目标对象方法进行通知时，可以使用
>
> `@Order(number)`注解在切面类的上，number值越小，优先级越高。
>
> * 没有环绕通知的情况下运行顺序
>
> `MyLogging类代理方法执行前的操作
> MyValidate类代理方法执行前的操作
> 调用div方法
> MyValidate类代理方法执行后的操作
> MyValidate类代理方法参数返回前的操作
> MyLogging类代理方法执行后的操作
> MyLogging类代理方法参数返回前的操作`
>
> * 在MyLogging有环绕通知的情况下运行顺序
>
> `MyLogging类环绕通知目标方法执行前的前置通知...
> MyLogging类代理方法执行前的操作
> MyValidate类代理方法执行前的操作
> 调用div方法
> MyValidate类代理方法执行后的操作
> MyValidate类代理方法参数返回前的操作
> MyLogging类环绕通知目标方法执行后的后置通知...
> MyLogging类环绕通知目标方法返回值返回前的通知...
> MyLogging类代理方法执行后的操作
> MyLogging类代理方法参数返回前的操作
> 结果: 2`
>
> * 在MyValidate有环绕通知的情况下运行顺序
>
> `MyLogging类代理方法执行前的操作
> MyValidate类环绕通知目标方法执行前的前置通知...
> MyValidate类代理方法执行前的操作
> 调用div方法
> MyValidate类环绕通知目标方法执行后的后置通知...
> MyValidate类环绕通知目标方法返回值返回前的通知...
> MyValidate类代理方法执行后的操作
> MyValidate类代理方法参数返回前的操作
> MyLogging类代理方法执行后的操作
> MyLogging类代理方法参数返回前的操作
> 结果: 2`
>
> * 在MyLogging类和MyValidate类都有环绕通知的情况下运行顺序
>
> `MyLogging类环绕通知目标方法执行前的前置通知...
> MyLogging类代理方法执行前的操作
> MyValidate类环绕通知目标方法执行前的前置通知...
> MyValidate类代理方法执行前的操作
> 调用div方法
> MyValidate类环绕通知目标方法执行后的后置通知...
> MyValidate类环绕通知目标方法返回值返回前的通知...
> MyValidate类代理方法执行后的操作
> MyValidate类代理方法参数返回前的操作
> MyLogging类环绕通知目标方法执行后的后置通知...
> MyLogging类环绕通知目标方法返回值返回前的通知...
> MyLogging类代理方法执行后的操作
> MyLogging类代理方法参数返回前的操作
> 结果: 2`

### 5.11 AOP使用场景

>  Authentication 权限
>
>  Caching 缓存
>
>  Context passing 内容传递
>
>  Error handling 错误处理
>
>  Lazy loading　懒加载
>
>  Debugging　　调试
>
>  logging, tracing, profiling and monitoring　记录跟踪　优化　校准
>
>  Performance optimization　性能优化
>
>  Persistence　　持久化
>
>  Resource pooling　资源池
>
>  Synchronization　同步
>
>  Transactions 事务

### 5.12 使用注解配置AOP步骤

> * 将目标类和切面类都加入到IOC容器中。@Component
> * 告诉Spring哪个是切面类。 @Aspect
> * 在切面类中使用五个通知注解配置切面类中这些通知方法都何时何地去运行
> * 开启基于注解的AOP功能 `<aop:aspectj-autoproxy/>`

### 5.13 使用XML配置AOP步骤

```xml
    <!-- 配置切面类和目标类,类似于使用@Component-->
    <bean id="myLogging" class="com.njcit.aspect.MyLogging"/>
    <bean id="myValidate" class="com.njcit.aspect.MyValidate"/>
    <bean id="calculator" class="com.njcit.cal.CalculatorImpl"/>

    <!-- 使用aop全称空间指定切面类-->
    <aop:config>
        <!-- 指定切面类，类似于使用@Aspect-->
        <aop:aspect ref="myLogging" order="1">
            <!-- 配置通知方法-->
            <aop:around method="methodAround" pointcut-ref="myExecution"/>
            <aop:pointcut id="myExecution" expression="execution(* com.njcit.cal.CalculatorImpl.*(int,int))"/>
            <aop:before method="methodStart" pointcut-ref="myExecution"/>
            <aop:after method="methodEnd" pointcut-ref="myExecution"/>
            <aop:after-returning method="methodReturning" pointcut="execution(* com.njcit.cal.CalculatorImpl.div(int,int))" returning="returnValue"/>
            <aop:after-throwing method="methodExce" pointcut-ref="myExecution" throwing="exception"/>
        </aop:aspect>
    </aop:config>
```

## 6. Spring的数据库模块

### 6.1 使用Spring-JdbcTemplate

> 在pom.xml文件中配置

```xml
        <!-- 导入Spring数据库模块-->
        <!-- spring-jdbc -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <!-- spring-orm -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
        <!-- spring-tx -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>5.1.5.RELEASE</version>
        </dependency>
```

> 在.xml中配置

```xml
    <!-- 引入外部文件-->
    <context:property-placeholder location="classpath:db.properties"/>
    <!-- 从外部文件中取值-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${db.username}"/>
        <property name="password" value="${db.password}"/>
        <property name="jdbcUrl" value="${db.url}"/>
        <property name="driverClass" value="${db.driver}"/>
    </bean>
    <!-- 使用spring操作数据库-->
    <bean class="org.springframework.jdbc.core.JdbcTemplate">
        <constructor-arg name="dataSource" ref="dataSource"/>
    </bean>
```

## 7. Spring的声明式事务

> 一般情况下比编程式事务好用。将事务管理代码从业务方法中分离出来，以声明的方式来实现事务管理。将事务管理作为横切关注点，通过aop方法模块化。Spring中通过Spring AOP框架支持声明式事务管理。
>
> 所有的事务管理器都是 `PlatformTransactionManager`接口的实现类。

```java
public interface PlatformTransactionManager {
    TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;

    void commit(TransactionStatus var1) throws TransactionException;

    void rollback(TransactionStatus var1) throws TransactionException;
}
```

### 7.1 事务管理器

>  Spring的核心事务管理抽象，管理封装了一组独立于技术的方法，无论使用Spring的哪种事务管理策略（编程式或者声明式）事务管理器都是必须的。

![事务](C:\Users\Administrator\Desktop\spring\images\事务.jpg)

### 7.2 在Spring中配置数据库事务

```xml
    <!-- 1.配置事务管理器-->
    <bean id="transactionManager"                     class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>
    <!-- 2.开启基于注解的事务控制模式，依赖tx名称空间-->
    <tx:annotation-driven transaction-manager="transactionManager"/>
    <!-- 3.给事务方法添加注解-->
```

### 7.3 事务属性

> * 传播行为
> * 隔离规则
> * 回滚规则
> * 事务超时
> * 是否只读

### 7.4 传播行为

> 事务方法被另一个事务方法调用时，必须指定事务应该如何传播。例如方法可能继续在现有事务中运行，也可能开启一个新的事务，并在自己的事务运行。spring中的事务传播行为可以由传播属性指定。spring指定了7种类传播行为。

| REQUIRED          | 如果有事务在运行，当前的方法就在这个事务内运行，否则就开启一个新的事务，并在自己的事务内运行,默认传播行为 |
| ----------------- | ------------------------------------------------------------ |
| **REQUIRED_NEW**  | 当前方法必须启动新事务，并在自己的事务内运行，如果有事务正在运行，则将它挂起 |
| **SUPPORTS**      | 如果有事务在运行，当前的方法就在这个事务内运行，否则可以不运行在事务中 |
| **NOT_SUPPORTED** | 表示该方法不应该运行在事务中。如果存在当前事务，在该方法运行期间，当前事务将被挂起。如果使用JTATransactionManager的话，则需要访问TransactionManager |
| **MANDATORY**     | 当前的方法必须运行在事务内部，如果没有正在运行的事务，就会抛出异常 |
| **NEVER**         | 当前方法不应该运行在事务中，如果有运行的事务，就抛出异常     |
| **NESTED**        | 如果有事务在运行，当前的方法就应该在这个事务的嵌套事务内运行。嵌套的事务可以独立于当前事务进行单独地提交或回滚。如果当前事务不存在，那么其行为与PROPAGATION.REQUIRED一样。 |

### 7.5 事务隔离级别

> 并发事务会导致发生以下三种类型的问题

| 脏读           | 发生在一个事务读取了另一个事务改写尚未提交的数据时，改写的数据被回滚了，那么第一个事务获取的数据无效 |
| -------------- | ------------------------------------------------------------ |
| **不可重复读** | 当同一个事务执行两次及以上相同的查询时，每次都得到不同的数据。一般因为另一并发事务在两次查询期间进行了更新 |
| **幻读**       | 第一个事务读取了一些数据，此时第二个事务在该表中插入了一些新数据，这是第一个事务再读取相同的数据就会多几行 |

> **不可重复读和幻读的区别**：不可重复读侧重点在相同数据被修改，而幻读是删除或新增
>
> ​    从理论上讲，事务应该完全隔离，避免并发事务导致的问题，但是这样可能对性能产生极大影响，因为事务必须按顺序进行了。所以在实际的开发中，为了提升性能，事务会以比较低的隔离级别运行。

### 7.6 Spring 中事务的隔离级别可以通过隔离属性指定

> **注意**：事务的隔离级别需要底层数据库引擎的支持，而不是应用程序或者框架的支持
>
> Oracle支持2种事务隔离级别：READ_COMMITED，SERIALIZABLE
>
> MySQL支持4种事务隔离级别
>
> **Oracle默认的事务隔离级别为读已提交，MySQL默认的事务隔离级别为可重复读**

| DEFAULT             | 使用底层数据库的默认隔离级别，大部分数据库，默认隔离级别都是READ_COMMITED |
| ------------------- | ------------------------------------------------------------ |
| **READ_COMMITED**   | 只允许事务读取已经被其他事务提交的更改，可以避免脏读，但不可重复读和幻读问题仍然可能出现 |
| **READ_UNCOMMITED** | 允许事务读取未被其他事务提交的更改。脏读，不可重复读，幻读都可能会出现 |
| **REPEATABLE_READ** | 确保事务可以多次从一个字段中读取相同的值。在这个事务持续期间，禁止其他事务对这个字段进行更新,可以避免脏读和不可重复读，但是幻读的问题依然存在 |
| **SERIALIZABLE**    | 确保事务可以从一个表中读取相同的行，在这个事务持续期间，禁止其他事务对该表执行插入，更新，删除。所有的并发问题都能避免，但是性能比较低。 |

### 7.7 @Transactional注解中的众多属性

```java
public @interface Transactional {
    @AliasFor("transactionManager")
    String value() default "";

    @AliasFor("value")
    String transactionManager() default "";

    //事务的传播行为
    Propagation propagation() default Propagation.REQUIRED;

    //事务的隔离级别
    Isolation isolation() default Isolation.DEFAULT;

    //超时：事务超出指定时间，并回滚
    int timeout() default -1;
  
    //设置事务为只读事务
    boolean readOnly() default false;
    
     /**
     异常：运行时异常 和 编译期异常
     运行时异常时默认回滚的而编译期异常是默认不回滚的
     */
    
    //哪些异常事务需要回滚,Class类型
    Class<? extends Throwable>[] rollbackFor() default {};
    //哪些异常事务需要回滚,String类型
    String[] rollbackForClassName() default {};

    //哪些异常事务可以不回滚，Class类型
    Class<? extends Throwable>[] noRollbackFor() default {};
    //哪些异常事务可以不回滚,String类型(可以让原来默认回滚的异常不再回滚)
    String[] noRollbackForClassName() default {};
}
```

### 7.8 基于注解配置事务属性

```java
    /**
     * 给方法添加上事务
     */
    @Transactional(
            timeout = 5,
            readOnly = false,
            isolation = Isolation.READ_COMMITTED,
            rollbackFor = {NullPointerException.class},
            noRollbackFor = {FileNotFoundException.class},
            propagation = Propagation.REQUIRES_NEW
    )
    public void updateName(String username,int id) throws Exception {
        customerDao.updateName(username,id);
    }
```

### 7.9 基于xml配置事务属性

```xml
   <!-- 1.配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="dataSource"/>
    </bean>

    <!-- 2.配置事务属性-->
    <tx:advice id="myAdvice" transaction-manager="transactionManager">
        <tx:attributes>
            <!-- 根据方法名指定事务的属性 -->
            <tx:method name="*"/>
            <!--propagation配置事务传播行为-->
			<tx:method name="purchase" propagation="REQUIRES_NEW"/>
			<!--isolation配置事务的隔离级别-->
			<tx:method name="update*" isolation="SERIALIZABLE"/>
			<!--rollback-for配置事务遇到异常必须回滚，no-rollback-for配置事务遇到异常必须不能回滚-->
			<tx:method name="add*" rollback-for="java.io.IOException" no-rollback-for="com.dmsd.spring.tx.BookStockException"/>
			<!--read-only配置事务只读属性-->
			<tx:method name="find*" read-only="true"/>
			<!--timeout配置事务的超时属性-->
			<tx:method name="get*" timeout="3"/>
        </tx:attributes>
    </tx:advice>

    <!-- 3. 配置事务切入点, 以及把事务切入点和事务属性关联起来-->
    <aop:config>
        <aop:pointcut id="txPoint" expression="execution(* com.njcit.service.*.*(..))"/>
        <aop:advisor advice-ref="myAdvice" pointcut-ref="txPoint"/>
    </aop:config>
```

### 7.10 事务失效

> 因为spring事务是基于aop的代理机制，当方法中调用this本身的方法时候即使在this的方法标明事务注解，但是事务注解会失效。如下

```java
@Transactional
	@Override
	public void purchase(String username, String isbn) {
		this.update(username, isbn);
	}
 
	@Transactional
	public void update(String username, String isbn) {
		//1. 获取书的单价
		int price = bookShopDao.findBookPriceByIsbn(isbn);
		//2. 更新数的库存
		bookShopDao.updateBookStock(isbn);
		//3. 更新用户余额
		bookShopDao.updateUserAccount(username, price);
    }
```

> 因为调用this本身方法不走代理机制，这个时候可以通过配置解决这个问题。
>
> ## 解决事务失效
>
> 在配置中添加如下内容

```xml
<!--开启aspectj代理，并暴露aop代理到ThreadLocal-->
<aop:aspectj-autoproxy expose-proxy="true"/>
```

> 将上述调用的地方改成如下

```java
@Transactional
@Override
public void purchase(String username, String isbn) {
	((BookShopServiceImpl)AopContext.currentProxy()).update(username, isbn);
}
```


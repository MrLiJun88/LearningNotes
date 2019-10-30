# 图解Java设计模式

## 1 .设计模式常用的七大原则有

> -  单一职责原则
> -  接口隔离原则
> -  依赖倒转(倒置)原则
> -  里氏替换原则
> -  开闭原则
> -  迪米特法则
> -  合成复用原则

### 1.2 单一职责原则

>  对类来说的，即一个类应该只负责一项职责。如类A负责两个不同职责：职责1，职责2。当职责1需求变更而改变A时，可能造成职责2执行错误，所以需要将类A的粒度分解为A1，A2

#### 1.2.1 单一职责原则注意事项和细节

> - **降低类的复杂度，一个类只负责一项职责。**
> -   提高类的可读性，可维护性
> -   降低变更引起的风险
> -    通常情况下，我们应当遵守单一职责原则，只有逻辑足够简单，才可以在代码级违反单一职责原则；
> -   只有类中方法数量足够少，可以在方法级别保持单一职责原则



### 1.3 接口隔离原则

>  客户端不应该依赖它不需要的接口，**即一个类对另一个类的依赖应该建立在最小的接口上。应传统方法的问题和使用接口隔离原则改进。**
>
>   1) 类A通过接口Interface1依赖类B，类C通过接口Interface1依赖类D，如果接口
>
> ​      Interface1对于类A和类C来说不是最小接口，那么类B和类D必须去实现他们不
>
> ​      需要的方法
>
>   2) 将接口Interface1拆分为独立的几个接口，类A和类C分别与他们需要的接口建立
>
> ​     依赖关系。也就是采用接口隔离原则
>
>   3) 接口Interface1中出现的方法，根据实际情况拆分为三个接口



![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/cc6e240f7e71465cb0a77a95b6cd3eb2/01e31f2581954d64971d26a55d8c1450.jpg)

### 1.4 依赖倒转(倒置)原则

>  依赖倒转原则(Dependence Inversion Principle)是指：**依赖接口编程**
>
> 1) 高层模块不应该依赖低层模块，二者都应该依赖其抽象
>
>   2) 抽象不应该依赖细节，细节(实现类)应该依赖抽象
>
>   3) 依赖倒转(倒置)的中心思想是面向接口编程
>
>   4) 依赖倒转原则是基于这样的设计理念：相对于细节的多变性，抽象的东西要稳定的多。以抽象为基础搭建的架构比以细节为基础的架构要稳定的多。在  java中，抽象指的是接口或抽象类，细节就是具体的实现类
>
>  5) **使用接口或抽象类的目的是制定好规范，而不涉及任何具体的操作，把展现细节的任务交给他们的实现类去完成**

#### 1.4.1 依赖关系传递的三种方式

>  1) 接口传递
>
>   2) 构造方法传递
>
>   3) setter方式传递
>
> 依赖倒转原则的注意事项和细节
>
>   1) 低层模块尽量都要有抽象类或接口，或者两者都有，程序稳定性更好. 
>
>   2) 变量的声明类型尽量是抽象类或接口, 这样我们的变量引用和实际对象间，就存在
>
>   一个缓冲层，利于程序扩展和优化
>
>   3) 继承时遵循里氏替换原则

### 1.5 里氏替换原则

>  OO中的继承性的思考和说明：**如何正确的使用继承？**
>
> - 继承包含这样一层含义：父类中凡是已经实现好的方法，实际上是在设定规范和契约，虽然它不强制要求所有的子类必须遵循这些契约，但是如果子类对这些已经实现的方法任意修改，就会对整个继承体系造成破坏。
>
> - 继承在给程序设计带来便利的同时，也带来了弊端。比如使用继承会给程序带来侵入性，程序的可移植性降低，增加对象间的耦合性，如果一个类被其他的类所继承，则当这个类需要修改时，必须考虑到所有的子类，并且父类修改后，所有涉及到子类的功能都有可能产生故障
>
>  问题提出：在编程中，如何正确的使用继承? => 里氏替换原则
>
> 在使用继承时，遵循里氏替换原则，在子类中尽量不要重写父类的方法
>
> 里氏替换原则告诉我们，继承实际上让两个类耦合性增强了，在适当的情况下，可
>
> 以通过聚合，组合，依赖 来解决问题
>
> 解决方法：
>
> - 在实际编程中，我们常常会通过重写父类的方法完成新的功能，这样写起来虽然简单，但整个继承体系的复用性会比较差。特别是运行多态比较频繁的时候
> - 通用的做法是：原来的父类和子类都继承一个更通俗的基类，**原有的继承关系去掉，采用依赖，聚合，组合等关系代替**

### 1.6 开闭原则

> **在原有的程序上增加功能时尽量是扩展类或方法，而不是修改原有代码。**
>
>   1) 开闭原则（Open Closed Principle）是编程中最基础、最重要的设计原则
>
>   2) 一个软件实体如类，模块和函数应该对扩展开放(对提供方)，对修改关闭(对使方)。用抽象构建框架，用实现扩展细节。
>
>   3) 当软件需要变化时，尽量通过扩展软件实体的行为来实现变化，而不是通过修改已有的代码来实现变化。
>
>   4) 编程中遵循其它原则，以及使用设计模式的目的就是遵循开闭原则。

### 1.7 迪米特法则

> **最少知道原则，降低类之间的耦合。**
>
>   1) 一个对象应该对其他对象保持最少的了解
>
>   2) 类与类关系越密切，耦合度越大
>
>   3) **迪米特法则(Demeter Principle)又叫最少知道原则，即一个类对自己依赖的类知道的**
>
> **越少越好。也就是说对于被依赖的类不管多么复杂，都尽量将逻辑封装在类的内**
>
> **部。对外除了提供的public 方法，不对外泄露任何信息**
>
>   4) 迪米特法则还有个更简单的定义：只与直接的朋友通信
>
>   5) 直接的朋友：每个对象都会与其他对象有耦合关系，只要两个对象之间有耦合关系，
>
> 我们就说这两个对象之间是朋友关系。耦合的方式很多，依赖，关联，组合，聚合
>
> 等。**其中，我们称出现成员变量，方法参数，方法返回值中的类为直接的朋友，而**
>
> **出现在局部变量中的类不是直接的朋友。也就是说，陌生的类最好不要以局部变量**
>
> **的形式出现在类的内部**
>
> 迪米特法则注意事项和细节
>
>   1) 迪米特法则的核心是降低类之间的耦合
>
>   2) 但是注意：由于每个类都减少了不必要的依赖，因此迪米特法则只是要求降低类间(对象间)耦合关系， 并不是要求完全没有依赖关系

### 1.8 合成复用原则

> **尽量使用合成/聚合的方式，而不是使用继承**

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c613d1701a5d4af496d00dfbb47aace1/4d54d124153e48f98f54743453496220.jpg)

  

## 2. UML中类之间的关系

> 类之间的关系：**依赖(Dependency)、泛化（继承,Generalization）、实现(Realization)、关联()、聚合(aggregation)与组合(composition)**

### 2.1 依赖

>  只要是在类中用到了对方，那么他们之间就存在依赖关系。如果没有对方，连编绎都通过不了。
>
> 1.   类中用到了对方
> 2.  如果是类的成员属性
> 3.  如果是方法的返回类型
> 4.  是方法接收的参数类型
> 5.  方法中使用到

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/bac8a4839c004927ae18bee5855a2893/034aea08037a4ef586dda82d74099ef1.jpg)

### 2.2 泛化

> **泛化关系实际上就是继承关系，他是依赖关系的特例**
>
> -   泛化关系实际上就是继承关系
> -  如果A类继承了B类，我们就说A和B存在泛化关系

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/69cea47c723946a68c124b53fabf734d/d9fab4b3ebfb43d1938c5e6b2fc1c6c0.jpg)

### 2.3 实现

> **A类实现B接口，他是依赖关系的特例**

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/08a7d31f81d642dda5f479cb87a85c16/71c1cd9f2a104147a861fbf98e6674b7.jpg)

### 2.4 关联

> **类与类之间的联系，他是依赖关系的特例**
>
> 关系具有多重性：如“1”（表示有且仅有一个），“0...”（表示0个或者多个），
>
> “0，1”（表示0个或者一个），“n...m”(表示n到 m个都可以),“m...*”（表示至少m
>
> 个）

  

### 2.5 聚合

> **整体和部分的关系，整体与部分可以分开**。聚合关系是关联关系的特例，所以他具有关联的导航性与多重性。

如：一台电脑由键盘(keyboard)、显示器(monitor)，鼠标等组成；组成电脑的各个

配件是可以从电脑上分离出来的，使用带空心菱形的实线来表示

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/da193b21b099462e85fcc93ea1495a83/d065c265d01c442dbe4000c7ffff31b1.jpg)

### 2.6 组合

> **也是整体与部分的关系，但是整体与部分不可以分开**

​    一方不存在，则另一方也不存在 ,当Computer类删除时，Monitor类和Mouse类也将一起被删除

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/610b15032e234534bb0708bdb565fd0e/cfcfb27f6e0a44418eec6922a9a13605.jpg)

## 3. 设计模式分为三种类型，共23种

>    1) :创建型模式：单例模式、抽象工厂模式、原型模式、建造者模式、工厂模式。
>
> ​    2): 结构型模式：适配器模式、桥接模式、装饰模式、组合模式、外观模式、享
>
> 元模式、代理模式。
>
> ​    3): 行为型模式：模版方法模式、命令模式、访问者模式、迭代器模式、观察者
>
> 模式、中介者模式、备忘录模式、解释器模式（Interpreter模式）、状态模
>
> 式、策略模式、职责链模式(责任链模式)。

## 4. 创建型模式

### 4.1 单例模式

> 所谓类的单例设计模式，就是采取一定的方法保证在整个的软件系统中，对某个类只能存在一个对象实例，并且该类只提供一个取得其对象实例的方法(静态方法)。
>
> 比如Hibernate的SessionFactory，它充当数据存储源的代理，并负责创建Session对象。SessionFactory并不是轻量级的，一般情况下，一个项目通常只需要一个SessionFactory就够，这是就会使用到单例模式。
>
> 单例模式有八种方式(红色标注推荐使用)：
>
>   1) 饿汉式(静态常量)
>
>   2) 饿汉式（静态代码块）
>
>   3) 懒汉式(线程不安全)
>
>   4) 懒汉式(线程安全，同步方法)
>
>   5) 懒汉式(线程安全，同步代码块)
>
>   **6) 双重检查**
>
>   **7) 静态内部类**
>
>   **8) 枚举**

**单例模式推荐使用(双重检查，静态内部类)**

```java
/**
 * 双重检查应用实例(推荐使用这种)
 *
 * Double-Check概念是多线程开发中常使用到的，如代码中所示，我们进行了两
 * 次if (singleton == null)检查，这样就可以保证线程安全了。
 * 2) 这样，实例化代码只用执行一次，后面再次访问时，判断if (singleton == null)，
 * 直接return实例化对象，也避免的反复进行方法同步.
 * 3) 线程安全；延迟加载；效率较高
 * 4) 结论：在实际开发中，推荐使用这种单例设计模式
 */
public class SingletonDemo3 {
    public static void main(String[] args) {
        Singleton3 s1 = Singleton3.getInstance();
        Singleton3 s2 = Singleton3.getInstance();

        System.out.println(s1 == s2);
    }
}
class Singleton3 {
    private Singleton3(){}
    /**将实例对象设置成 static volatile */
    private static volatile Singleton3 singleton3;
    public static Singleton3 getInstance(){
        if(null == singleton3){
            synchronized (Singleton3.class){
                /**双重检查应用实例*/
                if(null == singleton3){
                    singleton3 = new Singleton3();
                }
            }
        }
        return singleton3;
    }
}
```

```java
/**
 *静态内部类(推荐使用,外部类的加载，不会导致它的内部静态内部类的加载)
 * 优缺点说明：
 * 1) 这种方式采用了类装载的机制来保证初始化实例时只有一个线程。
 * 2) 静态内部类方式在Singleton类被装载时并不会立即实例化，而是在需要实例化
 * 时，调用getInstance方法，才会装载SingletonInstance类，从而完成Singleton的
 * 实例化。
 * 3) 类的静态属性只会在第一次加载类的时候初始化，所以在这里，JVM帮助我们
 * 保证了线程的安全性，在类进行初始化时，别的线程是无法进入的。
 * 4) 优点：避免了线程不安全，利用静态内部类特点实现延迟加载，效率高
 * 5) 结论：推荐使用
 */
public class SingletonDemo1 {
    public static void main(String[] args) {
        Singleton1 s1 = Singleton1.getInstance();
        Singleton1 s2 = Singleton1.getInstance();

        System.out.println(s1 == s2);
    }
}
class Singleton1 {
    /**构造器私有化*/
    private Singleton1(){}
    /**
     * 当外部类Singleton1被加载时，静态内部类SingleInstance不会被加载
     */
    private static class SingleInstance {
        private static final Singleton1 INSTANCE = new Singleton1();
    }
    /**当调用 getInstance方法时，静态内部类才会被加载*/
    public static Singleton1 getInstance(){
        return SingleInstance.INSTANCE;
    }
}
```

#### 4.1.1  单例模式注意事项和细节说明

> ​     1) 单例模式保证了 系统内存中该类只存在一个对象，节省了系统资源，对于一些需
>
> 要频繁创建销毁的对象，使用单例模式可以提高系统性能
>
> ​     2) 当想实例化一个单例类的时候，必须要记住使用相应的获取对象的方法，而不是使
>
> 用new
>
> ​     3) 单例模式使用的场景：需要频繁的进行创建和销毁的对象、创建对象时耗时过多或
>
> 耗费资源过多(即：重量级对象)，但又经常用到的对象、工具类对象、频繁访问数
>
> 据库或文件的对象(比如数据源、session工厂等)

#### 4.1.2 单例模式在JDK源码中的使用

> 在 Runtime类中就使用到了单例模式的饿汉式

```java
public class Runtime {
    private static Runtime currentRuntime = new Runtime();

    public static Runtime getRuntime() {
        return currentRuntime;
    }
```

### 4.2 工厂模式

#### 4.2.1 简单工厂模式

> 1) 简单工厂模式是属于创建型模式，是工厂模式的一种。简单工厂模式是由一个工厂对象决定创建出哪一种产品类的实例。简单工厂模式是工厂模式家族中最简单实用的模式
>
>  2) 简单工厂模式：定义了一个创建对象的类，由这个类来封装实例化对象的行
>
> 为(代码)
>
>  3) 在软件开发中，当我们会用到大量的创建某种、某类或者某批对象时，就会
>
> 使用到工厂模式

​    

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/9cfa766e8fbd45fbab251c6710793c4a/9ce90acf4db1493496ee82e08d6b74ff.jpg)

#### 4.2.2 工厂方法模式

> - 工厂方法模式设计方案：将披萨项目的实例化功能抽象成抽象方法，在不同的口味点餐子类中具体实现。
> - 工厂方法模式：定义了一个创建对象的抽象方法，由子类决定要实例化的类。工厂方法模式将对象的实例化推迟到子类

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c55e8b664c3845c9bd91b41a62912c6f/994144f0d5e841e7a7410f1d7c6e485d.jpg)

#### 4.2.3 抽象工厂模式

> 1) 抽象工厂模式：定义了一个interface用于创建相关或有依赖关系的对象簇，而无需
>
> 指明具体的类
>
>  2) 抽象工厂模式可以将简单工厂模式和工厂方法模式进行整合。
>
>  3) 从设计层面看，抽象工厂模式就是对简单工厂模式的改进(或者称为进一步的抽象)。
>
>  4) 将工厂抽象成两层，AbsFactory(抽象工厂) 和 具体实现的工厂子类。程序员可以
>
> 根据创建对象类型使用对应的工厂子类。这样将单个的简单工厂类变成了工厂簇，更利于代码的维护和扩展

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d33aa975fedd431a90f6dcd3c71c2be1/a9278ca092074bbcb40ea38bb4934d2d.jpg)

#### 4.2.4 工厂模式在JDK源码中的使用

> 在Calendar类中

```java
Calendar cal = null;
if (aLocale.hasExtensions()) {
    String caltype = aLocale.getUnicodeLocaleType("ca");
    if (caltype != null) {
        switch (caltype) {
        case "buddhist":
        cal = new BuddhistCalendar(zone, aLocale);
            break;
        case "japanese":
            cal = new JapaneseImperialCalendar(zone, aLocale);
            break;
        case "gregory":
            cal = new GregorianCalendar(zone, aLocale);
            break;
```

#### 4.2.5 工厂模式小结

> 1) 工厂模式的意义
>
> 将实例化对象的代码提取出来，放到一个类中统一管理和维护，达到和主项目的依赖关系的解耦。从而提高项目的扩展和维护性。
>
> ​    2) 三种工厂模式 (简单工厂模式、工厂方法模式、抽象工厂模式)
>
> ​    3) 设计模式的依赖抽象原则
>
> -  创建对象实例时，不要直接 new 类, 而是把这个new 类的动作放在一个工厂的方法中，并返回。有的书上说，变量不要直接持有具体类的引用。
> -  不要让类继承具体类，而是继承抽象类或者是实现interface(接口)
> -  不要覆盖基类中已经实现的方法

### 4.3 原型模式

> 1.  原型模式(Prototype模式)是指：用原型实例指定创建对象的种类，并且通过拷贝这些原型，创建新的对象
> 2.  原型模式是一种创建型设计模式，允许一个对象再创建另外一个可定制的对象，无需知道如何创建的细节
> 3.  工作原理是:通过将一个原型对象传给那个要发动创建的对象，这个要发动创建的对象通过请求原型对象拷贝它们自己来实施创建，即 对象.clone()
> 4.  形象的理解：孙大圣拔出猴毛， 变出其它孙大圣

```java
@Override
public Object clone() throws CloneNotSupportedException {
    Sheep sheep = null;
    sheep = (Sheep)super.clone();
    return sheep;
}
```



![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/19f73225efb64e92ab6aedffece8376f/4722cb3ce203417ba179038ffd23cc89.jpg)

#### 4.3.1 原型模式在Spring中的应用

`<bean id="monster" class="com.bean.Monster" scope="prototype"/>` 

#### 4.3.2 深拷贝与浅拷贝的区别:

##### 4.3.2.1 浅拷贝的介绍

> 1) 对于数据类型是基本数据类型的成员变量，**浅拷贝会直接进行值传递，也就是将该属性值复制一份给新的对象。**
>
>  2) 对于数据类型是引用数据类型的成员变量，比如说成员变量是某个数组、某个类的对象等，那么浅拷贝会进行引用传递，**也就是只是将该成员变量的引用值（内存地址）复制一份给新的对象。因为实际上两个对象的该成员变量都指向同一个实例。在这种情况下，在一个对象中修改该成员变量会影响到另一个对象的该成员变量值**
>
>  3) 前面我们克隆羊就是浅拷贝
>
>  4) 浅拷贝是使用默认的 clone()方法来实现
>
> sheep = (Sheep) super.clone(); 

##### 4.3.2.2 深拷贝基本介绍

>  1) 复制对象的所有基本数据类型的成员变量值
>
>  2) **为所有引用数据类型的成员变量申请存储空间，并复制每个引用数据类型成员变量所引用的对象，直到该对象可达的所有对象。也就是说，对象进行深拷贝要对整个对象进行拷贝**
>
>  3) **深拷贝实现方式1：重写clone方法来实现深拷贝**
>
>  **4) 深拷贝实现方式2：通过对象序列化实现深拷贝(推荐)**

```java
/**重写clone方法来实现深拷贝*/
@Override
public Object clone() throws CloneNotSupportedException {
    People people = null;
    people = (People)super.clone();
    Dog dog = (Dog)people.dog.clone();
    people.dog = dog;
    return people;
}
```

```java
/**使用实现序列化的方式深拷贝对象*/
public Object getDeepCopyObject() throws Exception{
    /**进行序列化*/
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    ObjectOutputStream oos = new ObjectOutputStream(bos);
    /**将当前对象写入到对象流中*/
    oos.writeObject(this);
    /**进行反序列化*/
    ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
    ObjectInputStream ois = new ObjectInputStream(bis);
    /**将当前对象从对象流中读出，并返回*/
    return ois.readObject();
}
```

四:建造者模式

-    建造者模式（Builder Pattern） 又叫生成器模式，是一种对象构建模式。它可以将复杂对象的建造过程抽象出来（抽象类别），使这个抽象过程的不同实现方法可以构造出不同表现（属性）的对象。
-  建造者模式 是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节

建造者模式的四个角色

1.  Product（产品角色）： 一个具体的产品对象。
2.  Builder（抽象建造者）： 创建一个Product对象的各个部件指定的 接口/抽象类。 3.ConcreteBuilder（具体建造者）： 实现接口，构建和装配各个部件。

​      4.Director（指挥者）： 构建一个使用Builder接口的对象。它主要是用于创建一个复杂的对象。它主要有两个作用，一是：隔离了客户与对象的生产过程，二是：负责控制产品对象的生产过程。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c13f141f147d4fd292610b47976e2a0c/47b08697b59c41058aece46776cb0306.jpg)

建造者模式在JDK中的应用(StringBuilder)

public final class StringBuilder    extends AbstractStringBuilder     abstract class AbstractStringBuilder implements Appendable,  

Appendable 接口定义了多个append方法(抽象方法), 即Appendable为抽象建 造者(AbstractBuilder), 定义了抽象方法 AbstractStringBuilder 实现了 Appendable 接口方法，这里的 AbstractStringBuilder 已经是建造者，只是不能实例化 StringBuilder 即充当了指挥者角色，同时充当了具体的建造者，建造方法的 实现是由 AbstractStringBuilder 完成, 而StringBuilder 继承了 AbstractStringBuilder  

抽象工厂模式VS建造者模式

   抽象工厂模式实现对产品家族的创建，一个产品家族是这样的一系列产品：具有不同分类维度的产品组合，采用抽象工厂模式不需要关心构建过程，只关心什么产品由什么工厂生产即可。而建造者模式则是要求按照指定的蓝图建造产品，它的主要目的是通过组装零配件而产生一个新产品.

五：适配器模式

   基本介绍

1. 适配器模式(Adapter Pattern)将某个类的接口转换成客户端期望的另一个接口表示，主目的是兼容性，让原本因接口不匹配不能一起工作的两个类可以协同工作。其别名为包装器(Wrapper)
2. 适配器模式属于结构型模式
3.  主要分为三类：类适配器模式、对象适配器模式、接口适配器模式

类适配器模式：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/7cadb828c5c742988419cc1723766a54/99117e342c094c299fb867f51fccdfe6.jpg)

类适配器模式注意事项和细节

   1) Java是单继承机制，所以类适配器需要继承src类这一点算是一个缺点, 因为这要求dst必须是接口，有一定局限性;

   2) src类的方法在Adapter中都会暴露出来，也增加了使用的成本。

   3) 由于其继承了src类，所以它可以根据需求重写src类的方法，使得Adapter的灵

活性增强了

对象适配器模式(聚合关系)

1.  基本思路和类的适配器模式相同，只是将Adapter类作修改，不是继承src类，而是持有src类的实例，以解决兼容性的问题。 即：持有 src类，实现 dst 类接口，完成src->dst的适配
2.  根据“合成复用原则”，在系统中尽量使用关联关系来替代继承关系。
3.  对象适配器模式是适配器模式常用的一种

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/ce0e25eb07e24a539b3ffff8c52d7e9b/5bfefd6334014b5694dadb67b7ea4a87.jpg)

对象适配器模式注意事项和细节

   1) 对象适配器和类适配器其实算是同一种思想，只不过实现方式不同。根据合成复用原则，使用组合替代继承， 所以它解决了类适配器必须继承src的局限性问题，也不再要求dst必须是接口。

   2) 使用成本更低，更灵活

接口适配器模式:

接口适配器模式介绍

1. 一些书籍称为：适配器模式(Default Adapter Pattern)或缺省适配器模式。
2.  当不需要全部实现接口提供的方法时，可先设计一个抽象类实现接口，并为该接口中每个方法提供一个默认实现（空方法），那么该抽象类的子类可有选择地覆盖父类的某些方法来实现需求
3.  适用于一个接口不想使用其所有的方法的情况

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/56550adeeb1641548d310443d8a598e7/534db16b8e7c483da43e7ee06e8a8f4d.jpg)

适配器模式在SpringMVC框架中的应用(DispatcherServlet源码中)

 适配器模式的注意事项和细节

1.  三种命名方式，是根据 src是以怎样的形式给到Adapter（在Adapter里的形式）来命名的。
2. 类适配器：以类给到，在Adapter里，就是将src当做类，继承对象适配器：以对象给到，在Adapter里，将src作为一个对象，持有接口适配器：以接口给到，在Adapter里，将src作为一个接口实现
3.  Adapter模式最大的作用还是将原本不兼容的接口融合在一起工作。
4.  实际开发中，实现起来不拘泥于我们讲解的三种经典形式

六：桥接模式:

  基本介绍

1.  桥接模式(Bridge模式)是指：将实现与抽象放在两个不同的类层次中，使两个层次可以独立改变。
2.  是一种结构型设计模式
3.  Bridge模式基于类的最小设计原则，通过使用封装、聚合及继承等行为让不同的类承担不同的职责。它的主要特点是把抽象(Abstraction)与行为实现(Implementation)分离开来，从而可以保持各部分的独立性以及应对他们的功能扩展

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c270744e923746788d32b69242363093/1cd37c10eeb246179caf3791381f8c9f.jpg)

桥接模式其它应用场景

   1) 对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用.

   2) 常见的应用场景:

- -JDBC驱动程序
- -银行转账系统
- 转账分类: 网上转账，柜台转账，AMT转账
- 转账用户类型：普通用户，银卡用户，金卡用户..
- -消息管理
- 消息类型：即时消息，延时消息
- 消息分类：手机短信，邮件消息，QQ消息...

七：装饰模式

   装饰者模式：动态的将新功能附加到对象上。在对象功能扩展方面，它比继承更有弹性，装饰者模式也体现了开闭原则(ocp)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/28ccfb07f1764f85a51b9aa90009a0f8/ae77483413c64b8c8962f5db3c021491.jpg)

装饰模式对应的UML类图

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/322a7969c61b41e09c611469701ac32c/d8faf81b87cd4db2acbfe2fb09bfb7bf.jpg)

装饰模式在JDK源码中的应用：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/2e58e420a1394028bbe126b1219a8df2/fc6072411b594ea5a3ef2a0e8199d94a.jpg)

  Java的IO结构，FilterInputStream就是一个装饰者

//FilterInputStream 继承了InputStream，并且类中组合了InputStream //InputStream就类似于AbstractDrink，FilterInputStream类似于Decorator public class FilterInputStream extends InputStream {    /**     * The input stream to be filtered.     */    protected volatile InputStream in; 

八:组合模式(适用于树型结构)

   基本介绍

1. 组合模式（Composite Pattern），又叫部分整体模式，它创建了对象组的树形结构，将对象组合成树状结构以表示“整体-部分”的层次关系。 组合模式依据树形结构来组合对象，用来表示部分以及整体层次。
2.  这种类型的设计模式属于结构型模式。
3.  组合模式使得用户对单个对象和组合对象的访问具有一致性，即：组合能让客户以一致的方式处理个别对象以及组合对象

解决的问题

1.  组合模式解决这样的问题，当我们的要处理的对象可以生成一颗树形结构，而我们要对树上的节点和叶子进行操作时，它能够提供一致的方式，而不用考虑它是节点还是叶子
2.  对应的示意图

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/7b8d3fb4782e431ab1ff1326c7172253/5ccb58a23d9b4668bb4814fd44832909.jpg)

组合模式对应的UML类图：

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/88d9dcdaa2da456c9f46efb532e883f2/f747dfd24f5640a1a77d356b0ef8300b.jpg)

组合模式在JDK源码中的应用：

   HashMap在JDK中对应的UML类图

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/16e6c2c1db8647569e76e67a68ff1581/f7639bf422334f1fa065442ce54671da.jpg)

//Java的集合类-HashMap就使用了组合模式 //Node是一个叶子结节，它是HashMap中的一个静态内部类 //Map是一个接口，用于定义方法的声明，AbstractMap实现了Map //并将Map中的方法进行了空实现,HashMap相当于一个树干，管理着Node //Node是HashMap的静态内部类，HashMap具体实现了Map声明的方法 //并且操作的对象就是Node static class Node<K,V> implements Map.Entry<K,V> {    final int hash;    final K key; 

组合模式的注意事项和细节

1.  简化客户端操作。客户端只需要面对一致的对象而不用考虑整体部分或者节点叶子的问题。
2.  具有较强的扩展性。当我们要更改组合对象时，我们只需要调整内部的层次关系，客户端不用做出任何改动.
3.  方便创建出复杂的层次结构。客户端不用理会组合里面的组成细节，容易添加节点或者叶子从而创建出复杂的树形结构
4.  需要遍历组织机构，或者处理的对象具有树形结构时, 非常适合使用组合模式.
5.  要求较高的抽象性，如果节点和叶子有很多差异性的话，比如很多方法和属性都不一样，不适合使用组合模式

九：外观模式(过程模式):

   基本介绍

1.  外观模式（Facade），也叫“过程模式：外观模式为子系统中的一组接口提供一个一致的界面，此模式定义了一个高层接口，这个接口使得这一子系统更加容易使用
2.  外观模式通过定义一个一致的接口，用以屏蔽内部子系统的细节，使得调用端只需跟这个接口发生调用，而无需关心这个子系统的内部细节

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/9bd1915970f647c4a935404d97e01579/1e31dea1f43c41d2b58b5544e241c85e.jpg)

- 外观模式可以理解为转换一群接口，客户只要调用一个接口，而不用调用多个接口才能达到目的。比如：在pc上安装软件的时候经常有一键安装选项（省去选择安装目录、安装的组件等等），还有就是手机的重启功能（把关机和启动合为一个操作）。
- 外观模式就是解决多个复杂接口带来的使用困难，起到简化用户操作的作用

十:享元模式(蝇量模式)

   基本介绍

1.  享元模式（Flyweight Pattern)也叫蝇量模式: 运用共享技术有效地支持大量细粒度的对象.
2.  常用于系统底层开发，解决系统的性能问题。像数据库连接池，里面都是创建好的连接对象，在这些连接对象中有我们需要的则直接拿来用，避免重新创建，如果没有我们需要的，则创建一个.
3.  享元模式能够解决重复对象的内存浪费的问题，当系统中有大量相似对象，需要缓冲池时。不需总是创建新对象，可以从缓冲池里拿。这样可以降低系统内存，同时提高效率.
4.  享元模式经典的应用场景就是池技术了，String常量池、数据库连接池、缓冲池等等都是享元模式的应用，享元模式是池技术的重要实现方式.

内部状态和外部状态

​    比如围棋、五子棋、跳棋，它们都有大量的棋子对象，围棋和五子棋只有黑白两色，跳棋颜色多一点，所以棋子颜色就是棋子的内部状态；而各个棋子之间的差别就是位置的不同，当我们落子后，落子颜色是定的，但位置是变化的，所以棋子坐标就是棋子的外部状态

1. 享元模式提出了两个要求：细粒度和共享对象。这里就涉及到内部状态和外部状态了，即将对象的信息分为两个部分：内部状态和外部状态
2.  内部状态指对象共享出来的信息，存储在享元对象内部且不会随环境的改变而改变
3.  外部状态指对象得以依赖的一个标记，是随环境改变而改变的、不可共享的状态

 

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/193f2eae10214fd0ae065b0ec80b77d2/e421f6690ffc4b9c839ad0bf0739546f.jpg)

享元模式在JDK源码中的使用(Integer)

 Integer.valueOf(27); //valueOf的源码 public static Integer valueOf(int i) {    if (i >= IntegerCache.low && i <= IntegerCache.high)    //如果i的范围在-128~127之间，就从Integer的缓存池中获取,即    //使用了享元模式    //如果不在这个范围内，则new一个新的对象实例返回        return IntegerCache.cache[i + (-IntegerCache.low)];    return new Integer(i); } 

享元模式的注意事项和细节

1.  在享元模式这样理解，“享”就表示共享，“元”表示对象
2.  系统中有大量对象，这些对象消耗大量内存，并且对象的状态大部分可以外部化时，我们就可以考虑选用享元模式
3.  用唯一标识码判断，如果在内存中有，则返回这个唯一标识码所标识的对象，用HashMap/HashTable存储
4.  享元模式大大减少了对象的创建，降低了程序内存的占用，提高效率
5.  享元模式提高了系统的复杂度。需要分离出内部状态和外部状态，而外部状态具有固化特性，不应该随着内部状态的改变而改变，这是我们使用享元模式需要注意的地方.
6.  使用享元模式时，注意划分内部状态和外部状态，并且需要有一个工厂类加以控制。
7.  享元模式经典的应用场景是需要缓冲池的场景，比如 String常量池、数据库连接池

十一：代理模式

  代理模式的基本介绍

1.  代理模式：为一个对象提供一个替身，以控制对这个对象的访问。即通过代理对象访问目标对象.这样做的好处是:可以在目标对象实现的基础上,增强额外的功能操作,即扩展目标对象的功能。
2.  被代理的对象可以是远程对象、创建开销大的对象或需要安全控制的对象
3.  代理模式有不同的形式, 主要有三种 静态代理、动态代理 (JDK代理、接口代理)和 Cglib代理 (可以在内存动态的创建对象，而不需要实现接口， 他是属于动态代理的范畴) 

   一.静态代理

   静态代理在使用时,需要定义接口或者父类,被代理对象(即目标对象)与代理对象一起实现相同的接口或者是继承相同父类.

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c48412901142405abdd4331a323dead4/3aec121bdbd5433189146cfca944c59f.jpg)

静态代理优缺点

​    1) 优点：在不修改目标对象的功能前提下, 能通过代理对象对目标功能扩展

​    2) 缺点：因为代理对象需要与目标对象实现一样的接口,所以会有很多代理类

​    3) 一旦接口增加方法,目标对象与代理对象都要维护

二.动态代理

  动态代理模式的基本介绍

1.  代理对象,不需要实现接口，但是目标对象要实现接口，否则不能用动态代理
2.  代理对象的生成，是利用JDK的API，动态的在内存中构建代理对象
3.  动态代理也叫做：JDK代理、接口代理

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d5cd17ced7a744f2a72610e6666bccc7/00a50e2f62bb42d99ae3fdabc32ee331.jpg)

三.Cglib代理(不需要实现任何接口)

   静态代理和JDK代理模式都要求目标对象是实现一个接口,但是有时候目标对象只是一个单独的对象,并没有实现任何的接口,这个时候可使用目标对象子类来实现代理-这就是Cglib代理

1.  Cglib代理也叫作子类代理,它是在内存中构建一个子类对象从而实现对目标对象功能扩展, 有些书也将Cglib代理归属到动态代理。
2.  Cglib是一个强大的高性能的代码生成包,它可以在运行期扩展java类与实现java接口.它广泛的被许多AOP的框架使用,例如Spring AOP，实现方法拦截
3.  在AOP编程中如何选择代理模式：

​             \1. 目标对象需要实现接口，用JDK代理

​             \2. 目标对象不需要实现接口，用Cglib代理

1.  Cglib包的底层是通过使用字节码处理框架ASM来转换字节码并生成新的类

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/494506a7758c40e38d46d7f463a0cb6c/fa381ffe571d4c7a9e3ef9004e3cfad5.jpg)

十二:模板方法模式

   基本介绍

1.  模板方法模式（Template Method Pattern），又叫模板模式(Template Pattern)，在一个抽象类公开定义了执行它的方法的模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。
2.  简单说，模板方法模式 定义一个操作中的算法的骨架，而将一些步骤延迟到子类中，使得子类可以不改变一个算法的结构，就可以重定义该算法的某些特定步骤
3.  这种类型的设计模式属于行为型模式。
4. 模板方法模式的钩子方法

 在模板方法模式的父类中，我们可以定义一个方法，它默认不做任何事，子类可以视情况要不要覆盖它，该方法称为“钩子”

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/5ac340ea2d47440c87188416602adba7/83a5d173849f4112b1920cb94a204d60.jpg)

模板方法模式的注意事项和细节

1.  基本思想是：算法只存在于一个地方，也就是在父类中，容易修改。需要修改算法时，只要修改父类的模板方法或者已经实现的某些步骤，子类就会继承这些修改
2.  实现了最大化代码复用。父类的模板方法和已实现的某些步骤会被子类继承而直接使用。
3.  既统一了算法，也提供了很大的灵活性。父类的模板方法确保了算法的结构保持不变，同时由子类提供部分步骤的实现。
4.  该模式的不足之处：每一个不同的实现都需要一个子类实现，导致类的个数增加，使得系统更加庞大
5.  一般模板方法都加上final关键字， 防止子类重写模板方法.
6.  模板方法模式使用场景：当要完成在某个过程，该过程要执行一系列步骤 ，这一系列的步骤基本相同，但其个别步骤在实现时可能不同，通常考虑用模板方法模式来处理。

十三：命令模式

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/9c27461d51254d80823bcf3da4fc3f1c/b7db297f4ee04cb4985d38129eb35dc3.jpg)

基本介绍

1.  命令模式（Command Pattern）：在软件设计中，我们经常需要向某些对象发送请求，但是并不知道请求的接收者是谁，也不知道被请求的操作是哪个，我们只需在程序运行时指定具体的请求接收者即可，此时，可以使用命令模式来进行设计
2.  命名模式使得请求发送者与请求接收者消除彼此之间的耦合，让对象之间的调用关系更加灵活，实现解耦。
3.  在命名模式中，会将一个请求封装为一个对象，以便使用不同参数来表示不同的请求(即命名)，同时命令模式也支持可撤销的操作。
4.  通俗易懂的理解：将军发布命令，士兵去执行。其中有几个角色：将军（命令发布者）、士兵（命令的具体执行者）、命令(连接将军和士兵)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d66beb481bc4457983c81394fa732808/2e2658f5b0744f53989ab4ccc55c7eba.jpg)

命令模式的注意事项和细节

1.  将发起请求的对象与执行请求的对象解耦。发起请求的对象是调用者，调用者只要调用命令对象的execute()方法就可以让接收者工作，而不必知道具体的接收者对象是谁、是如何实现的，命令对象会负责让接收者执行请求的动作，也就是说：”请求发起者”和“请求执行者”之间的解耦是通过命令对象实现的，命令对象起到了纽带桥梁的作用。
2.  容易设计一个命令队列。只要把命令对象放到列队，就可以多线程的执行命令
3.  容易实现对请求的撤销和重做
4.  命令模式不足：可能导致某些系统有过多的具体命令类，增加了系统的复杂度，这点在在使用的时候要注意
5.  空命令也是一种设计模式，它为我们省去了判空的操作。在上面的实例中，如果没有用空命令，我们每按下一个按键都要判空，这给我们编码带来一定的麻烦。
6.  命令模式经典的应用场景：界面的一个按钮都是一条命令、模拟CMD（DOS命令）订单的撤销/恢复、触发-反馈机制

十四:访问者模式

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/4ec669b0757848b39456c7b4723056ea/0fec107c64f0433f99fa766a0c21f135.jpg)

  访问者模式基本介绍

1.  访问者模式（Visitor Pattern），封装一些作用于某种数据结构的各元素的操作，它可以在不改变数据结构的前提下定义作用于这些元素的新的操作。
2.  主要将数据结构与数据操作分离，解决 数据结构和操作耦合性问题
3.  访问者模式的基本工作原理是：在被访问的类里面加一个对外提供接待访问者的接口
4.  访问者模式主要应用场景是：需要对一个对象结构中的对象进行很多不同操作(这些操作彼此没有关联)，同时需要避免让这些操作"污染"这些对象的类，可以选用访问者模式解决

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/789e18d85a4744d498d999794bfc8ae0/4d5a59b2c0b14642ac4c5d9ebd619b07.jpg)

访问者模式的注意事项和细节

 优点

1.  访问者模式符合单一职责原则、让程序具有优秀的扩展性、灵活性非常高
2.  访问者模式可以对功能进行统一，可以做报表、UI、拦截器与过滤器，适用于数据结构相对稳定的系统

 缺点

1.  具体元素对访问者公布细节，也就是说访问者关注了其他类的内部细节，这是迪米特法则所不建议的, 这样造成了具体元素变更比较困难
2.  违背了依赖倒转原则。访问者依赖的是具体元素，而不是抽象元素
3.  因此，如果一个系统有比较稳定的数据结构，又有经常变化的功能需求，那么访问者模式就是比较合适的

十五：迭代器模式

​    基本介绍

1.  迭代器模式（Iterator Pattern）是常用的设计模式，属于行为型模式
2.  如果我们的集合元素是用不同的方式实现的，有数组，还有java的集合类，或者还有其他方式，当客户端要遍历这些集合元素的时候就要使用多种遍历方式，而且还会暴露元素的内部结构，可以考虑使用迭代器模式解决。
3.  迭代器模式，提供一种遍历集合元素的统一接口，用一致的方法遍历集合元素，不需要知道集合对象的底层表示，即：不暴露其内部的结构.

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/19fb7c32ac474cfc9c5e5e884db27a70/a9061fccc7064fc089c21b066b282ea5.jpg)

迭代器模式在JDK源码中的使用(JDK-ArrayList):

​     角色分析说明

- 内部类Itr 充当具体实现迭代器Iterator 的类， 作为ArrayList 内部类
-  List 就是充当了聚合接口，含有一个iterator() 方法，返回一个迭代器对象
-  ArrayList 是实现聚合接口List 的子类，实现了iterator()
-  Iterator 接口系统提供
-  迭代器模式解决了 不同集合(ArrayList ,LinkedList) 统一遍历问题

迭代器模式的注意事项和细节

 优点

1.  提供一个统一的方法遍历对象，客户不用再考虑聚合的类型，使用一种方法就可以遍历对象了。
2.  隐藏了聚合的内部结构，客户端要遍历聚合的时候只能取到迭代器，而不会知道聚合的具体组成。
3.  提供了一种设计思想，就是一个类应该只有一个引起变化的原因（叫做单一责任原则）。在聚合类中，我们把迭代器分开，就是要把管理对象集合和遍历对象集合的责任分开，这样一来集合改变的话，只影响到聚合对象。而如果遍历方式改变的话，只影响到了迭代器。
4.  当要展示一组相似对象，或者遍历一组相同对象时使用, 适合使用迭代器模式

 缺点

1. 每个聚合对象都要一个迭代器，会生成多个迭代器不好管理类

十六：观察者模式

  观察者模式：对象之间多对一依赖的一种设计方案，被依赖的对象为Subject，依赖的对象为Observer，Subject通知Observer变化,比如这里的奶站是Subject，是1的一方。用户时Observer，是多的一方。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/e74e5c332f1a42f9a00c400bfac4cf59/c4a40df078f940c8bda8d6822e47994b.jpg)

观察者模式的好处

1.  观察者模式设计后，会以集合的方式来管理用户(Observer)，包括注册，移除和通知。
2.  这样，我们增加观察者(这里可以理解成一个新的公告板)，就不需要去修改核心类WeatherData不会修改代码，遵守了ocp原则

观察者模式在JDK中的应用：

//JDK中有Observer接口，声明了update方法    public interface Observer {    void update(Observable o, Object arg); } /**Observable相当于图中WeatherData类，但它并没有实现接口 //而是直接将管理观察者对象的方法实现在类中 public class Observable {    private boolean changed = false;    public Observable() {        obs = new Vector<>();    }    public synchronized void addObserver(Observer o)     public synchronized void deleteObserver(Observer o)     public void notifyObservers(Object arg)  

十七：中介者模式

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/d29aa9df02b64290a95c1d598fc02d70/e7e1e696fd574dcda269a1bf1286893b.jpg)

   基本介绍

1.  中介者模式（Mediator Pattern），用一个中介对象来封装一系列的对象交互。中介者使各个对象不需要显式地相互引用，从而使其耦合松散，而且可以独立地改变它们之间的交互
2.  中介者模式属于行为型模式，使代码易于维护
3.  比如MVC模式，C（Controller控制器）是M（Model模型）和V（View视图）的中介者，在前后端交互时起到了中间人的作用

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/7992cfffe5b04e66a775730652c1a8a1/c911c1ccaebc43de89134e0691a1e161.jpg)

中介者模式的注意事项和细节

1.  多个类相互耦合，会形成网状结构, 使用中介者模式将网状结构分离为星型结构，进行解耦
2.  减少类间依赖，降低了耦合，符合迪米特原则
3.  中介者承担了较多的责任，一旦中介者出现了问题，整个系统就会受到影响
4.  如果设计不当，中介者对象本身变得过于复杂，这点在实际使用时，要特别注意

十八:备忘录模式

   基本介绍

1.  备忘录模式（Memento Pattern）在不破坏封装性的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态
2.  可以这里理解备忘录模式：现实生活中的备忘录是用来记录某些要去做的事情，或者是记录已经达成的共同意见的事情，以防忘记了。而在软件层面，备忘录模式有着相同的含义，备忘录对象主要用来记录一个对象的某种状态，或者某些数据，当要做回退时，可以从备忘录对象里获取原来的数据进行恢复操作
3.  备忘录模式属于行为型模式

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/fa8e1b14609e4300a5621e105628caf3/b745e3514b064073a596881e6202a361.jpg)

对原理类图的说明-即

(备忘录模式的角色及职责)

1. originator : 对象(需要保存状态的对象)
2.  Memento ： 备忘录对象,负责保存好记录，即Originator内部状态
3.  Caretaker: 守护者对象,负责保存多个备忘录对象， 使用集合管理，提高效率
4.  说明：如果希望保存多个originator对象的不同时间的状态，也可以，只需要要 HashMap <String, 集合> 

备忘录模式的注意事项和细节

1.  给用户提供了一种可以恢复状态的机制，可以使用户能够比较方便地回到某个历史的状态
2.  实现了信息的封装，使得用户不需要关心状态的保存细节
3.  如果类的成员变量过多，势必会占用比较大的资源，而且每一次保存都会消耗一定的内存, 这个需要注意
4.  适用的应用场景：1、后悔药。 2、打游戏时的存档。 3、Windows 里的 ctri + z。4、IE 中的      后退。 4、数据库的事务管理
5.  为了节约内存，备忘录模式可以和原型模式配合使用

十九:解释器模式

  基本介绍

1.   在编译原理中，一个算术表达式通过词法分析器形成词法单元，而后这些词法单元通过语法分析器构建语法分析树，最终形成一颗抽象的语法分析树。这里的词法分析器法分析器都可以看做是解释器 
2.  解释器模式（Interpreter Pattern）：是指给定一个语言(表达式)，定义它的文法 的一种表示，并定义一个解释器，使用该解释器来解释语言中的句子(表达式)
3. 应用场景应用可以将一个需要解释执行的语言中的句子表示为一个抽象语法树

​            • 一些重复出现的题可以用一种简单的语言来表达

​            • 一个简单语法需要解释的场景

1. 这样的例子还有，比编译器、运算表达式计算、正则表达式、机器人等

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/8bc518107aa642c3902f7eb1cc225857/b9cd4ac90aaa4dd6a98a19235158ac32.jpg)

1.  Expresson: 抽象表达式， 声明一个抽象的解释操作,这个方法为抽象语法树中所有的节点所 共享 
2. VarExpresson: 为终结符表达式, 实现与文法中的终结符相关的解释操作 
3. SymbolExpression: 为非终结符表达式，为文法中的非终结符实现解释操作.
4. AddExpression,SubExpression为SymbolExpression方法的具体实现

解释器模式的注意事项和细节

1.  当有一个语言需要解释执行，可将该语言中的句子表示为一个抽象语法树，就可以虑使用解释器模式，让程序具有良好的扩展性
2.   应用场景：编译器、运算表达式计算、正则表达式、机器人等
3.  使用解释器可能带来的问题：解释器模式会引起类膨胀、解释器模式采用递归调用法，将会导致调试非常复杂、效率可能降低.

二十:状态模式

   在很多情况下，一个对象的行为取决于它的一个或多个变化的属性，这些属性我们称之为状态，这个对象称之为状态对象。对于状态对象而已,它的行为依赖于它的状态.

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/81a6e75304db4b4fba90b3295a9bd55e/ed54546bf3af457f97f1ac43aac5aaa0.jpg)

基本介绍

1.  状态模式（State Pattern）：它主要用来解决对象在多种状态转换时，需要对外输出不同的行为的问题。状态和行为是一一对应的，状态之间可以相互转换
2.   当一个对象的内在状态改变时，允许改变其行为，这个对象看起来像是改变了其类

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/543b1224e9f6470aa6bd7a84fdf21e05/fead2a4fb6d8455b8d3822d14c56d5b4.jpg)

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/5c675e4e26ca4e529257e6fc7c56e370/4d102d2d8ccd44cfb58da8cf7fe5d59d.jpg)

状态模式的注意事项和细节

1.  代码有很强的可读性。状态模式将每个状态的行为封装到对应的一个类中 
2. 方便维护。将容易产生问题的if-else语句删除了，如果把每个状态的行为都放到一 个类中，每次调用方法时都要判断当前是什么状态，不但会产出很多if-else语句， 而且容易出错 
3.  符合“开闭原则”。容易增删状态 
4.  会产生很多类。每个状态都要一个对应的类，当状态过多时会产生很多类，加大维 护难度 
5.  应用场景：当一个事件或者对象有很多种状态，状态之间会相互转换，对不同的状 态要求有不同的行为的时候，可以考虑使用状态模式

二十一：策略模式

  基本介绍

1.  策略模式（Strategy Pattern）中，定义算法族，分别封装起来，让他们之间可以 互相替换，此模式让算法的变化独立于使用算法的客户 
2.  这算法体现了几个设计原则，

- 第一、把变化的代码从不变的代码中分离出来；
-  第二、针对接口编程而不是具体类（定义了策略接口）；
- 第三、多用组合/聚合， 少用继承（客户通过组合方式使用策略）。

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/c8489d598bf349a286a86066f0e7de3d/e57025602cdf4382a20284b0a553c071.jpg)

 策略模式的注意事项和细节

1.  策略模式的关键是：分析项目中变化部分与不变部分 
2.  策略模式的核心思想是：多用组合/聚合 少用继承；用行为类组合，而不是行为的 继承。更有弹性 
3.  体现了“对修改关闭，对扩展开放”原则，客户端增加行为不用修改原有代码，只 要添加一种策略（或者行为）即可，避免了使用多重转移语句（if..else if..else）
4.   提供了可以替换继承关系的办法： 策略模式将算法封装在独立的Strategy类中使得 你可以独立于其Context改变它，使它易于切换、易于理解、易于扩展
5.   需要注意的是：每添加一个策略就要增加一个类，当策略过多是会导致类数目庞大

二十二：职责链模式

  

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/0f6d9957146446bb9d4205c8562d31ed/6ee41716d2734231aab310c7416252e0.jpg)

基本介绍

1. 职责链模式（Chain of Responsibility Pattern）, 又叫 责任链模式，为请求创建了一个接收者 对象的链(简单示意图)。这种模式对请求的 发送者和接收者进行解耦。
2.   职责链模式通常每个接收者都包含对另一个接 收者的引用。如果一个对象不能处理该请求， 那么它会把相同的请求传给下一个接收者，依此类推。
3.  这种类型的设计模式属于行为型模式

![img](C:/Users/Administrator/Desktop/%E6%B7%AE%E5%B7%A5%E5%AD%A6%E4%B9%A0/%E6%9C%89%E9%81%93%E4%BA%91%E7%AC%94%E8%AE%B0/weixinobU7Vjva5V8h-sPnhvlf5aRHShUc/ed6af90fb9bb4f8c9585730ff72093ef/54f7044fdd2b44e3854fb3a61b3f6da2.jpg)

职责链模式的注意事项和细节

1.  将请求和处理分开，实现解耦，提高系统的灵活性 
2.  简化了对象，使对象不需要知道链的结构 
3.  性能会受到影响，特别是在链比较长的时候，因此需控制链中最大节点数量，一般 通过在Handler中设置一个最大节点数量，在setNext()方法中判断是否已经超过阀值， 超过则不允许该链建立，避免出现超长链无意识地破坏系统性能
4.  调试不方便。采用了类似递归的方式，调试时逻辑可能比较复杂
5.  最佳应用场景：有多个对象可以处理同一个请求时，比如：多级请求、请假/加薪 等审批流程、Java Web中Tomcat对Encoding的处理、拦截器
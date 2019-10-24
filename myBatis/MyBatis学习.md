# MyBatis学习

## 1. 入门

> [官方文档：]: https://mybatis.org/mybatis-3/zh/getting-started.html
>
> 在maven中导入依赖

```xml
       <!-- mybatis -->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.3</version>
        </dependency>
```

## 2. 获取SqlSessionFactory与SqlSession

```java
public class SessionUtil {

    /**每个基于 MyBatis 的应用都是以一个 SqlSessionFactory 的实例为核心的
     * SqlSessionFactory 的实例可以通过 SqlSessionFactoryBuilder 获得。而 SqlSessionFactoryBuilder
     * 则可以从 XML 配置文件或一个预先定制的 Configuration 的实例构建出 SqlSessionFactory 的实例。
     * */
    private static SqlSessionFactory sqlSessionFactory;
    
    static{
        String resource = "mybatis-config.xml";
        InputStream inputStream = null;
        try {
            inputStream = Resources.getResourceAsStream(resource);
        } catch (IOException e) {
            e.printStackTrace();
        }
        sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    }

    /**
     * 既然有了 SqlSessionFactory，顾名思义，我们就可以从中获得 SqlSession 的实例了。
     * SqlSession 完全包含了面向数据库执行 SQL 命令所需的所有方法。
     * 你可以通过 SqlSession 实例来直接执行已映射的 SQL 语句。
     * */
    public static SqlSession getSession(){
        SqlSession sqlSession = sqlSessionFactory.openSession();
        return sqlSession;
    }

    public static void closeSession(SqlSession sqlSession){
        sqlSession.close();
    }
}
```

## 3. 配置mybatis-config.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!-- 配置数据库连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/test"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>

    <!-- 引入接口实现的配置文件-->
    <mappers>
        <mapper resource="mapper/customerMapper.xml"/>
    </mappers>
</configuration>
```

## 4. 基于xml的mapper接口的sql实现配置

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!-- namespace:接口的全类名:相当于告诉mybatis是实现配置文件是实现哪个接口-->
<mapper namespace="com.njcit.mapper.CustomerMapper">
    <!--
    id : 接口中的方法名
    resultType : 方法的返回类型
    -->
    <select id="getCustById" resultType="com.njcit.entity.Customer">
    select id,name,email from customers where id = #{id}
  </select>
</mapper>
```

## 5. 作用域(Scopt)和生命周期

### 5.1 SqlSessionFactoryBuild

>  **这个类可以被实例化、使用和丢弃，一旦创建了 SqlSessionFactory，就不再需要它了。** 因此 SqlSessionFactoryBuilder 实例的最佳作用域是方法作用域（也就是局部方法变量）。你可以重用 SqlSessionFactoryBuilder 来创建多个 SqlSessionFactory实例，但是最好还是不要让其一直存在，以保证所有的 XML 解析资源可以被释放给更重要的事情。         

### 5.2 SqlSessionFactory

>  **SqlSessionFactory 一旦被创建就应该在应用的运行期间一直存在，没有任何理由丢弃它或重新创建另一个实例。**使用 SqlSessionFactory 的最佳实践是在应用运行期间不要重复创建多次，多次重建          SqlSessionFactory 被视为一种代码“坏味道（bad smell）”。因此          SqlSessionFactory 的最佳作用域是应用作用域。有很多方法可以做到，最简单的就是使用单例模式或者静态单例模式。         

### 5.3 SqlSession

>  每个线程都应该有它自己的 SqlSession 实例。SqlSession的实例不是线程安全的，因此是不能被共享的，所以它的最佳的作用域是请求或方法作用域。**绝对不能将 SqlSession 实例的引用放在一个类的静态域，甚至一个类的实例变量也不行。也绝不能将 SqlSession 实例的引用放在任何类型的托管作用域中，比如 Servlet 框架中的 HttpSession。**如果你现在正在使用一种 Web 框架，要考虑 SqlSession 放在一个和 HTTP 请求对象相似的作用域中。换句话说，每次收到的 HTTP 请求，就可以打开一个 SqlSession，返回一个响应，就关闭它。这个关闭操作是很重要的，你应该把这个关闭操作放到 finally 块中以确保每次都能执行关闭。          下面的示例就是一个确保 SqlSession 关闭的标准模式：         
>
>  在你的所有的代码中一致地使用这种模式来保证所有数据库资源都能被正确地关闭。 

```java
try (SqlSession session = sqlSessionFactory.openSession()) {
  // 你的应用逻辑代码
}
```

## 6. 全局配置文件

### 6.1 properties

> 引入外部配置文件

```xml
    <!-- 引入外部数据库配置文件-->
    <properties resource="db.properties"/>

    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <!-- 配置数据库连接池-->
            <dataSource type="POOLED">
                <property name="driver" value="${db.driver}"/>
                <property name="url" value="${db.url}"/>
                <property name="username" value="${db.username}"/>
                <property name="password" value="${db.password}"/>
            </dataSource>
        </environment>
    </environments>
```

### 6.2 setting

>  这是 MyBatis 中极为重要的调整设置，它们会改变 MyBatis 的运行时行为。下表描述了设置中各项的意图、默认值等。 

### 6.3 类型别名(typeAliases)

>  类型别名是为 Java 类型设置一个短的名字。        它只和 XML 配置有关，存在的意义仅在于用来减少类完全限定名的冗余。例如： 

```xml
<typeAliases>
  <typeAlias alias="Author" type="domain.blog.Author"/>
  <typeAlias alias="Blog" type="domain.blog.Blog"/>
  <typeAlias alias="Comment" type="domain.blog.Comment"/>
  <typeAlias alias="Post" type="domain.blog.Post"/>
  <typeAlias alias="Section" type="domain.blog.Section"/>
  <typeAlias alias="Tag" type="domain.blog.Tag"/>
</typeAliases>
```

#### 6.3.1 批量起别名

```xml
    <typeAliases>
        <!-- 批量起别名,默认别名就是指定名下的类包-->
        <package name="com.njcit.entity"/>
    </typeAliases>
```

### 6.4 类型处理器(typeHandlers)

240
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

>  无论是 MyBatis 在预处理语句（PreparedStatement）中设置一个参数时，还是从结果集中取出一个值时，          都会用类型处理器将获取的值以合适的方式转换成 Java 类型。下表描述了一些默认的类型处理器。 ###

### 6.5 对象工厂

>  MyBatis 每次创建结果对象的新实例时，它都会使用一个对象工厂（ObjectFactory）实例来完成。          默认的对象工厂需要做的仅仅是实例化目标类，要么通过默认构造方法，要么在参数映射存在的时候通过参数构造方法来实例化。如果想覆盖对象工厂的默认行为，则可以通过创建自己的对象工厂来实现。比如： 

### 6.6 插件

> ​          MyBatis 允许你在已映射语句执行过程中的某一点进行拦截调用。默认情况下，MyBatis 允许使用插件来拦截的方法调用包括：        
>
> - Executor(update, query, flushStatements, commit, rollback, getTransaction, close,isClosed)          
> - ParameterHandler(getParameterObject, setParameters)          
> - ResultSetHandler(handleResultSets, handleOutputParameters)          
> - StatementHandler(prepare, parameterize, batch, update, query)          
>
> 这些类中方法的细节可以通过查看每个方法的签名来发现，或者直接查看 MyBatis 发行包中的源代码。          如果你想做的不仅仅是监控方法的调用，那么你最好相当了解要重写的方法的行为。          因为如果在试图修改或重写已有方法的行为的时候，你很可能在破坏 MyBatis 的核心模块。          这些都是更低层的类和方法，所以使用插件的时候要特别当心。

### 6.7 环境配置

> ​          MyBatis 可以配置成适应多种环境，这种机制有助于将 SQL 映射应用于多种数据库之中，现实情况下有多种理由需要这么做。例如，开发、测试和生产环境需要有不同的配置；或者想在具有相同 Schema 的多个生产数据库中使用相同的 SQL 映射。有许多类似的使用场景。
>
> ​          **不过要记住：尽管可以配置多个环境，但每个 SqlSessionFactory实例只能选择一种环境。**        
>
> ​          所以，如果你想连接两个数据库，就需要创建两个 SqlSessionFactory实例，每个数据库对应一个。而如果是三个数据库，就需要三个实例，依此类推，记起来很简单：        
>
> - **每个数据库对应一个 SqlSessionFactory 实例**          
>
> ​          为了指定创建哪种环境，只要将它作为可选的参数传递给SqlSessionFactoryBuilder 即可。可以接受环境配置的两个方法签名是：        

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, environment, properties);
```

>  如果忽略了环境参数，那么默认环境将会被加载，如下所示：         

```java
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader);
SqlSessionFactory factory = new SqlSessionFactoryBuilder().build(reader, properties);
```

>  环境元素定义了如何配置环境。         

```xml
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

>  注意这里的关键点:
>
>  默认的环境和环境 ID 是自解释的，因此一目了然。你可以对环境随意命名，但一定要保证默认的环境 ID 要匹配其中一个环境 ID。 
>
> -  默认使用的环境 ID（比如：default="development"）。          
> -  每个 environment 元素定义的环境 ID（比如：id="development"）。          
> -  事务管理器的配置（比如：type="JDBC"）。          
> -  数据源的配置（比如：type="POOLED"）。          

#### 6.7.1 事务管理器

>  如果你正在使用 Spring + MyBatis，则没有必要配置事务管理器，因为 Spring 模块会使用自带的管理器来覆盖前面的配置。         

#### 6.7.2 数据源

> dataSource 元素使用标准的 JDBC 数据源接口来配置 JDBC 连接对象的资源。
>
> - 许多 MyBatis 的应用程序会按示例中的例子来配置数据源。虽然这是可选的，但为了使用延迟加载，数据源是必须配置的。          
>
> 有三种内建的数据源类型（也就是 **type=”[UNPOOLED|POOLED|JNDI]”**）：

#### 6.7.3 使用第三方数据源

>  你可以通过实现接口 `org.apache.ibatis.datasource.DataSourceFactory` 来使用第三方数据源：         

```java
public interface DataSourceFactory {
    void setProperties(Properties var1);

    DataSource getDataSource();
}
```

>  `org.apache.ibatis.datasource.unpooled.UnpooledDataSourceFactory` 可被用作父类来构建新的数据源适配器，比如下面这段插入 C3P0 数据源所必需的代码： 

```java
public class C3P0DataSourceFactory extends UnpooledDataSourceFactory {

  public C3P0DataSourceFactory() {
    this.dataSource = new ComboPooledDataSource();
  }
}
```

>  为了令其工作，记得为每个希望 MyBatis 调用的 setter 方法在配置文件中增加对应的属性。        下面是一个可以连接至 PostgreSQL 数据库的例子： 

```xml
<dataSource type="org.myproject.C3P0DataSourceFactory">
  <property name="driver" value="org.postgresql.Driver"/>
  <property name="url" value="jdbc:postgresql:mydb"/>
  <property name="username" value="postgres"/>
  <property name="password" value="root"/>
</dataSource>
```

### 6.8 数据库厂商标识(databaseIdProvider)

>  MyBatis 可以根据不同的数据库厂商执行不同的语句，这种多厂商的支持是基于映射语句中的 `databaseId` 属性。MyBatis 会加载不带 `databaseId` 属性和带有匹配当前数据库 `databaseId` 属性的所有语句。          如果同时找到带有 `databaseId` 和不带 `databaseId` 的相同语句，则后者会被舍弃。为支持多厂商特性只要像下面这样在 mybatis-config.xml 文件中加入 `databaseIdProvider` 即可：         

```xml
<databaseIdProvider type="DB_VENDOR">
  <property name="SQL Server" value="sqlserver"/>
  <property name="DB2" value="db2"/>
  <property name="Oracle" value="oracle" />
</databaseIdProvider>
```

### 6.9 映射器(Mappers)

>  既然 MyBatis 的行为已经由上述元素配置完了，我们现在就要定义 SQL 映射语句了。但是首先我们需要告诉 MyBatis 到哪里去找到这些语句。Java 在自动查找这方面没有提供一个很好的方法，所以最佳的方式是告诉 MyBatis 到哪里去找映射文件。你可以使用相对于类路径的资源引用，或完全限定资源定位符（包括 `file:///` 的 URL），或类名和包名等。例如：         

```xml
<!-- 使用相对于类路径的资源引用 -->
<mappers>
  <mapper resource="org/mybatis/builder/AuthorMapper.xml"/>
  <mapper resource="org/mybatis/builder/BlogMapper.xml"/>
  <mapper resource="org/mybatis/builder/PostMapper.xml"/>
</mappers>
```

```xml
<!-- 将包内的映射器接口实现全部注册为映射器 
     使用接口映射，需要将接口对应的.xml配置文件放在与接口同包下，并且名字相同
-->
<mappers>
  <package name="org.mybatis.builder"/>
</mappers>
```

```xml
<!-- 使用映射器接口实现类的完全限定类名
     使用接口映射，需要将接口对应的.xml配置文件放在与接口同包下，并且名字相同
 -->
<mappers>
  <mapper class="org.mybatis.builder.AuthorMapper"/>
  <mapper class="org.mybatis.builder.BlogMapper"/>
  <mapper class="org.mybatis.builder.PostMapper"/>
</mappers>
```

```xml
<!-- 使用完全限定资源定位符（URL） -->
<mappers>
  <mapper url="file:///var/mappers/AuthorMapper.xml"/>
  <mapper url="file:///var/mappers/BlogMapper.xml"/>
  <mapper url="file:///var/mappers/PostMapper.xml"/>
</mappers>
```

## 7. XML映射文件

> SQL映射元素
>
> -   `cache` – 对给定命名空间的缓存配置。        
> -  `cache-ref`– 对其他命名空间缓存配置的引用。        
> -  `resultMap` – 是最复杂也是最强大的元素，用来描述如何从数据库结果集中来加载对象。        
> -  ~~`parameterMap`   – 已被废弃！老式风格的参数映射。更好的办法是使用内联参数，此元素可能在将来被移除。文档中不会介绍此元素。~~                  
> -   `sql`  – 可被其他语句引用的可重用语句块。        
> -   `insert`   – 映射插入语句        
> -  `update`  – 映射更新语句        
> -   `delete`   – 映射删除语句        
> -   `select` – 映射查询语句        

### 7.1 select

> select 标签中的属性。

```xml
<select
  id="selectPerson"
  parameterType="int"
  parameterMap="deprecated"
  resultType="hashmap"
  resultMap="personResultMap"
  flushCache="false"
  useCache="true"
  timeout="10"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY"></select>
```

|       属性       |                             描述                             |
| :--------------: | :----------------------------------------------------------: |
|       `id`       |      在命名空间中唯一的标识符，可以被用来引用这条语句。      |
| `parameterType`  | 将会传入这条语句的参数类的完全限定名或别名。这个属性是可选的，因为                MyBatis 可以通过类型处理器（TypeHandler） 推断出具体传入语句的参数，默认值为未设置（unset）。 |
| ~~parameterMap~~ | ~~这是引用外部 parameterMap 的已经被废弃的方法。请使用内联参数映射和 parameterType 属性。~~ |
|   `resultType`   | 从这条语句中返回的期望类型的类的完全限定名或别名。 注意如果返回的是集合，那应该设置为集合包含的类型，而不是集合本身。可以使用resultType 或 resultMap，但不能同时使用。 |
|   `resultMap`    | 外部 resultMap 的命名引用。结果集的映射是 MyBatis 最强大的特性，如果你对其理解透彻，许多复杂映射的情形都能迎刃而解。可以使用resultMap 或 resultType，但不能同时使用。 |
|   `flushCache`   | 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：false。 |
|    `useCache`    | 将其设置为 true 后，将会导致本条语句的结果被二级缓存缓存起来，默认值：对 select 元素为 true。 |
|    `timeout`     | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖驱动）。 |
|   `fetchSize`    | 这是一个给驱动的提示，尝试让驱动程序每次批量返回的结果行数和这个设置值相等。默认值为未设置（unset）（依赖驱动）。 |
| `statementType`  | STATEMENT，PREPARED 或 CALLABLE 中的一个。这会让 MyBatis 分别使用                Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `resultSetType`  | FORWARD_ONLY，SCROLL_SENSITIVE, SCROLL_INSENSITIVE 或                DEFAULT（等价于 unset） 中的一个，默认值为 unset （依赖驱动）。 |
|   `databaseId`   | 如果配置了数据库厂商标识（databaseIdProvider），MyBatis会加载所有的不带 databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。 |
| `resultOrdered`  | 这个设置仅针对嵌套结果 select 语句适用：如果为true，就是假设包含了嵌套结果集或是分组，这样的话当返回一个主结果行的时候，就不会发生有对前面结果集的引用的情况。这就使得在获取嵌套的结果集的时候不至于导致内存不够用。默认值：`false`。 |
|   `resultSets`   | 这个设置仅对多结果集的情况适用。它将列出语句执行后返回的结果集并给每个结果集一个名称，名称是逗号分隔的。 |

### 7.2 insert,update 和 delete

> 标签中的元素

```xml
<insert
  id="insertAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  keyProperty=""
  keyColumn=""
  useGeneratedKeys=""
  timeout="20">

<update
  id="updateAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">

<delete
  id="deleteAuthor"
  parameterType="domain.blog.Author"
  flushCache="true"
  statementType="PREPARED"
  timeout="20">
```

|        属性        |                             描述                             |
| :----------------: | :----------------------------------------------------------: |
|        `id`        |        命名空间中的唯一标识符，可被用来代表这条语句。        |
|  `parameterType`   | 将要传入语句的参数的完全限定类名或别名。这个属性是可选的，因为 MyBatis 可以通过类型处理器推断出具体传入语句的参数，默认值为未设置（unset）。 |
| ~~`parameterMap`~~ | ~~这是引用外部 parameterMap 的已经被废弃的方法。请使用内联参数映射和 parameterType 属性。~~ |
|    `flushCache`    | 将其设置为 true 后，只要语句被调用，都会导致本地缓存和二级缓存被清空，默认值：true（对于 insert、update 和 delete 语句）。 |
|     `timeout`      | 这个设置是在抛出异常之前，驱动程序等待数据库返回请求结果的秒数。默认值为未设置（unset）（依赖驱动）。 |
|  `statementType`   | STATEMENT，PREPARED 或 CALLABLE 的一个。这会让 MyBatis 分别使用                Statement，PreparedStatement 或 CallableStatement，默认值：PREPARED。 |
| `useGeneratedKeys` | （仅对 insert 和 update 有用）这会令 MyBatis 使用 JDBC 的                getGeneratedKeys 方法来取出由数据库内部生成的主键（比如：像 MySQL 和 SQL Server 这样的关系数据库管理系统的自动递增字段），默认值：false。 |
|   `keyProperty`    | （仅对 insert 和 update 有用）唯一标记一个属性，MyBatis 会通过                getGeneratedKeys 的返回值或者通过 insert 语句的 selectKey 子元素设置它的键值，默认值：未设置（`unset`）。如果希望得到多个生成的列，也可以是逗号分隔的属性名称列表。 |
|    `keyColumn`     | （仅对 insert 和 update 有用）通过生成的键值设置表中的列名，这个设置仅在某些数据库（像  PostgreSQL）是必须的，当主键列不是表中的第一列的时候需要设置。如果希望使用多个生成的列，也可以设置为逗号分隔的属性名称列表。 |
|    `databaseId`    | 如果配置了数据库厂商标识（databaseIdProvider），MyBatis 会加载所有的不带databaseId 或匹配当前 databaseId 的语句；如果带或者不带的语句都有，则不带的会被忽略。 |

### 7.3 sql

>  这个元素可以被用来定义可重用的 SQL 代码段，这些 SQL 代码可以被包含在其他语句中。它可以（在加载的时候）被静态地设置参数。在不同的包含语句中可以设置不同的值到参数占位符上。比如：         

### 7.4 参数 

> 

#### 7.4.1 #{} 与 ${} 的区别

>  默认情况下,使用 `#{}` 格式的语法会导致 MyBatis 创建 `PreparedStatement`参数占位符并安全地设置参数（就像使用 ? 一样）。这样做更安全，更迅速，通常也是首选做法，不过有时你就是想直接在 SQL 语句中插入一个不转义的字符串。比如，像 ORDER BY，你可以这样来使用：      
>
> ```sql
> ORDER BY ${columnName}
> ```
>
>  
>
> #{属性名} : 是参数预编译的方式，参数的位置都用？代替，参数是后来编译设置进去的，不存在sql注入问题
>
> ${属性名} ：不是参数预编译，而是直接和sql语句进行拼串

#### 7.4.2 结果映射(resultMap)

> - constructor \- 用于在实例化类时，注入结果到构造方法中             
>   - `idArg` - ID 参数；标记出作为 ID 的结果可以帮助提高整体性能
>   - `arg` - 将被注入到构造方法的一个普通结果
> - `id` – 一个 ID 结果；标记出作为 ID 的结果可以帮助提高整体性能
> - `result` – 注入到字段或 JavaBean 属性的普通结果
> - association – 一个复杂类型的关联；许多结果将包装成这种类型             
>   - 嵌套结果映射 – 关联本身可以是一个 `resultMap` 元素，或者从别处引用一个
> - collection – 一个复杂类型的集合             
>   - 嵌套结果映射 – 集合本身可以是一个 `resultMap` 元素，或者从别处引用一个
> - discriminator – 使用结果值来决定使用哪个 resultMap
>   - case – 基于某些值的结果映射                 
>     - 嵌套结果映射 – `case` 本身可以是一个 `resultMap` 元素，因此可以具有相同的结构和元素，或者从别处引用一个

|     属性      |                             描述                             |
| :-----------: | :----------------------------------------------------------: |
|     `id`      |     当前命名空间中的一个唯一标识，用于标识一个结果映射。     |
|    `type`     | 类的完全限定名, 或者一个类型别名（关于内置的类型别名，可以参考上面的表格）。 |
| `autoMapping` | 如果设置这个属性，MyBatis将会为本结果映射开启或者关闭自动映射。这个属性会覆盖全局的属性 autoMappingBehavior。默认值：未设置（unset）。 |

#### 7.4.3 一对多关联

## 8. 动态SQL

> * if
> * choose(wen,otherwise)
> * trim(where ,sert)
> * foreach

### 8.1 if

> *where* 元素只会在至少有一个子元素的条件返回 SQL 子句的情况下才去插入“WHERE”子句。而且，若语句的开头为“AND”或“OR”，*where* 元素也会将它们去除。

```xml
    <resultMap id="customerByCondition" type="com.njcit.entity.Customer">
        <id property="id" column="id"/>
        <result property="name" column="name"/>
        <result property="email" column="email"/>
        <result property="birthday" column="birth"/>
    </resultMap>
    <!-- if 多条件查询
    根据id号，和姓名，两个条件进行模糊查询
    -->
    <select id="getCustomerByCondition" resultMap="customerByCondition">
        select id,name,email,birth from customers
        <where>
            <if test="id != null">
                id > #{id}
            </if>
            <if test="name != null and name != ''">
               and name like #{name}
            </if>
        </where>
    </select>
```

### 8.2 trim

> 如果 *where* 元素没有按正常套路出牌，我们可以通过自定义 trim 元素来定制 *where* 元素的功能。比如，和 *where* 元素等价的自定义 trim 元素为：

```xml
   <select id="getCustomerByCondition" resultMap="customerByCondition">
        select id,name,email,birth from customers
        <!--
        prefix: 前缀，为sql整体添加一个前缀
        prefixOverrides: 取出整体字符串前面多余的字符
        suffix:后缀，为sql整体添加一个后缀
        suffixOverrides:取出整体字符串后面多说的字符
        -->
        <trim prefix="where" prefixOverrides="and | or">
            <if test="id != null">
                id > #{id}
            </if>
            <if test="name != null and name != ''">
                and name like #{name}
            </if>
        </trim>
    </select>
```

### 8.3 foreach

>  动态 SQL 的另外一个常用的操作需求是对一个集合进行遍历，通常是在构建 IN 条件语句的时候。比如： 

```xml
    <select id="getCustsByList" resultMap="custsByLlist">
        select id,name,email,birth from customers where id IN
        <!--
        collection:要遍历的集合名，如果是List类型，规定使用list
        item:集合中的每个元素对象
        open:以什么开始
        close:以什么结束
        separator:以什么符号分隔
        index:索引，如果是map类型，就是map中的每个key
        -->
        <foreach collection="list" item="item" index="index" open="(" separator="," close=")">
        #{item}
        </foreach>
    </select>
```

### 8.4 choose,when,otherwise

> 相当于java语言中的 switch 语句，若一个都没有匹配到，则会匹配 <otherwise/>

```xml
  <select id="getCustomerByChoose" resultMap="custsByLlist">
        select * from customers
        <where>
            <choose>
                <when test="email != null">
                    and email=#{email}
                </when>
                <when test="name != null">
                    and name=#{name}
                </when>
                <otherwise>
                    and 1 = 1
                </otherwise>
            </choose>
        </where>
    </select>
```

### 8.5 set

>  类似的用于动态更新语句的解决方案叫做 *set*。*set* 元素可以用于动态包含需要更新的列，而舍去其它的。比如： 

```xml
    <update id="updateCustomer" parameterType="com.njcit.entity.Customer">
        update customers
        <set>
            <if test="name != null">name=#{name},</if>
            <if test="email != null">email=#{email}</if>
        </set>
        where id=#{id}
    </update>
```

> 这里，*set* 元素会动态前置 SET 关键字，同时也会删掉无关的逗号，因为用了条件语句之后很可能就会在生成的 SQL 语句的后面留下这些逗号。（译者注：因为用的是“if”元素，若最后一个“if”没有匹配上而前面的匹配上，SQL 语句的最后就会有一个逗号遗留）
>
> 若你对 *set* 元素等价的自定义 trim 元素的代码感兴趣，那这就是它的真面目：

```xml
<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

### 8.6 bind

>  `bind` 元素可以从 OGNL 表达式中创建一个变量并将其绑定到上下文。比如： 

```xml
<select id="selectBlogsLike" resultType="Blog">
  <bind name="pattern" value="'%' + name + '%'" />
  SELECT * FROM BLOG
  WHERE title LIKE #{pattern}
</select>
```

### 8.7 多数据库支持

>  一个配置了“_databaseId”变量的 databaseIdProvider 可用于动态代码中，这样就可以根据不同的数据库厂商构建特定的语句。比如下面的例子： 

```xml
<insert id="insert">
  <selectKey keyProperty="id" resultType="int" order="BEFORE">
    <if test="_databaseId == 'oracle'">
      select seq_users.nextval from dual
    </if>
    <if test="_databaseId == 'db2'">
      select nextval for seq_users from sysibm.sysdummy1"
    </if>
  </selectKey>
  insert into users values (#{id}, #{name})
</insert>
```

## 9. 缓存

> Mybatis 使用到了两种缓存：**本地缓存（local cache）和二级缓存（second level cache）。**
>
> **每当一个新 session 被创建，MyBatis 就会创建一个与之相关联的本地缓存。**任何在 session  执行过的查询语句本身都会被保存在本地缓存中，那么相同的查询语句和相同的参数所产生的更改就不会二度影响数据库了。本地缓存会被增删改、提交事务、关闭事务以及关闭 session 所清空。
>
> 默认情况下，本地缓存数据可在整个 session 的周期内使用，这一缓存需要被用来解决循环引用错误和加快重复嵌套查询的速度，所以它可以不被禁用掉，但是你可以设置 localCacheScope=STATEMENT 表示缓存仅在语句执行时有效。

### 9.1 一级缓存

#### 9.1.1 一级缓存的生命周期

> * MyBatis在开启一个数据库会话时，会 创建一个新的SqlSession对象，SqlSession对象中会有一个新的Executor对象。Executor对象中持有一个新的PerpetualCache对象；当会话结束时，SqlSession对象及其内部的Executor对象还有PerpetualCache对象也一并释放掉。
> * 如果SqlSession调用了close()方法，会释放掉一级缓存PerpetualCache对象，一级缓存将不可用。
> * 如果SqlSession调用了clearCache()，会清空PerpetualCache对象中的数据，但是该对象仍可使用。
> * SqlSession中执行了任何一个update操作(update()、delete()、insert()) ，都会清空PerpetualCache对象的数据，但是该对象可以继续使用

### 9.2 二级缓存

> SqlSessionFactory层面上的二级缓存默认是不开启的，二级缓存的开席需要进行配置，实现二级缓存的时候，MyBatis要求返回的POJO必须是可序列化的。  也就是要求实现Serializable接口，配置方法很简单，只需要在映射XML文件配置就可以开启缓存了<cache/>，如果我们配置了二级缓存就意味着：
>
> - 映射语句文件中的所有select语句将会被缓存。
> - 映射语句文件中的所欲insert、update和delete语句会刷新缓存。
> - 缓存会使用默认的Least Recently Used（LRU，最近最少使用的）算法来收回。
> - 根据时间表，比如No Flush Interval,（CNFI没有刷新间隔），缓存不会以任何时间顺序来刷新。
> - 缓存会存储列表集合或对象(无论查询方法返回什么)的1024个引用
> - 缓存会被视为是read/write(可读/可写)的缓存，意味着对象检索不是共享的，而且可以安全的被调用者修改，不干扰其他调用者或线程所做的潜在修改。

#### 9.2.1 开始二级缓存

> 在全局配置中开始二级缓存，默认是开启的

```xml
<!--开启二级缓存 -->
        <setting name="cacheEnabled" value="true"/>
```

#### 9.2.2  通过 cache 元素的属性来修改

>  在需要缓存的mapp.xml中配置二级缓存
>
> 这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。         

```xml
<mapper namespace="com.njcit.mapper.CustomerMapper">
<cache
  eviction="FIFO"
  flushInterval="60000"
  size="512"
  readOnly="true"/>
```

> ​          这个更高级的配置创建了一个 FIFO 缓存，每隔 60 秒刷新，最多可以存储结果对象或列表的 512          个引用，而且返回的对象被认为是只读的，因此对它们进行修改可能会在不同线程中的调用者产生冲突。        
>
> ​          可用的清除策略有：        
>
> - `LRU` – 最近最少使用：移除最长时间不被使用的对象。          
> - `FIFO` – 先进先出：按对象进入缓存的顺序来移除它们。          
> -  `SOFT` – 软引用：基于垃圾回收器状态和软引用规则移除对象。          
> - `WEAK` – 弱引用：更积极地基于垃圾收集器状态和弱引用规则移除对象。          
>
> **默认的清除策略是 LRU。**
>
> ​          flushInterval（刷新间隔）属性可以被设置为任意的正整数，设置的值应该是一个以毫秒为单位的合理时间量。默认情况是不设置，也就是没有刷新间隔，缓存仅仅会在调用语句时刷新。        
>
> ​          size（引用数目）属性可以被设置为任意正整数，要注意欲缓存对象的大小和运行环境中可用的内存资源。默认值是 1024。        
>
> ​          readOnly（只读）属性可以被设置为 true 或 false。只读的缓存会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这就提供了可观的性能提升。而可读写的缓存会（通过序列化）返回缓存对象的拷贝。速度上会慢一些，但是更安全，因此默认值是 false。        
>
> ​          **提示 二级缓存是事务性的。这意味着，当 SqlSession 完成并提交时，或是完成并回滚，但没有执行          flushCache=true 的 insert/delete/update 语句时，缓存会获得更新。**        

### 9.3 缓存有关设置

> * 全局Setting的cacheEnable:配置二级缓存的开关，一级缓存是一直打开的
> * select标签的useCache属性：配置这个select是否使用二级缓存，一级缓存是一直使用的
> * sql标签的fluchCache属性：增删改默认的fluchCache=true,sql执行以后，会同时清空一级缓存和二级缓存，select默认fluchCache=false
> * sqlSession.clearCache():只是用来清除一级缓存

### 9.4 整合第三方缓存

> 


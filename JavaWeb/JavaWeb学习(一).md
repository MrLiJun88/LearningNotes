JavaWeb学习

## 1. HTTP

> * Http是一个**基于请求/响应模式的，无状态的协议**(request/response based ,stateless protocol)
>
> * Http1.1版本中，给出了一个**持续连接（Persistent Connections）的机制，并将其作为Http1.1中建立连接的缺省行为**。通过这种连接，浏览器可以在建立一个连接之后，发送请求并得到回应，然后继续发送请求并再次得到回应。而且，客户端还可以发送流水线请求，也就是说，客户端可以连续发送多个请求，而不用等待每一个响应的到来

### 1.1 HTTP URL格式 

> * – http://host[: port] [abs_path]
> * – 其中http表示要通过HTTP协议来定位网络资源。
> * – Host表示合法的Internet主机域名或IP地址（以点分十进制格式表示）
> * – Port用于指定一个端口号，拥有被请求资源的服务器主机监听该端口的TCP连接。如果port是空，则使用缺省的端口80。
> * – abs_path指定请求资源的URI（Uniform Resource Identifier,统一资源标识符），如果URL中没有给出abs_path,那么当它作为请求URI时，必须以“/”的形式给出。通常这个工作浏览器就帮我们完成了

### 1.2 HTTP请求

> 客户端通过发送HTTP请求向服务器请求对资源的访问
>
> * HTTP请求由三部分组成，分别是：**请求行，消息报头，请求正文**
> * 请求行以一个方法符号开头，后面跟着请求URI和协议的版本，以CRLF作为结尾。请求行以空格
>   分隔。除了作为结尾的CRLF外，不允许出现单独的CR或LF字符，格式如下：
>   `– Method Request-URI HTTP-Version CRLF`
>   • Method表示请求的方法，Request-URI是一个统一资源标识符，标识了要请求的资源，`HTTP-Version`表示请求的HTTP协议版本， CRLF表示回车换行。例如：`– GET /test.html HTTP/1.1 (CRLF)`

### 1.3 GET与POST

> * GET方法用于**获取由Request-URI所标识的资源的信息**，常见形式是：
>   `– GET Request-URI HTTP/1.1`
> *  POST方法用于向服务器发送请求，要求**服务器接受附在请求后面的数据**。POST方法在表单提交的时候用的最多
>   * 采用POST方法提交表单的例子
>     `POST /login.jsp HTTP/1.1 (CRLF)`
>     `Accept:image/gif (CRLF) (….)`
>     `Host:www.sample.com (CRLF)(….)`
>     `….`
>     `Cache-Control:no=cache (CRLF)`
>     `(CRLF)`
>     `username=hello&password=123456`

#### post请求中文乱码问题解决

> * ```java
>   @Override
>   protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException,
>   IOException {
>           // 设置请求体的字符集为 UTF-8，从而解决 post 请求的中文乱码问题
>           req.setCharacterEncoding("UTF-8");
>   ```

### 1.4 HTTP响应

> * 在接收和解释请求消息后，服务器会返回一个HTTP响应消息与HTTP请求类似，HTTP响应也是由三个
>   部分组成，分别是：**状态行，消息报头，响应正文**

### 1.5 HTTP消息

> * HTTP消息由客户端到服务器的请求和服务器到客户端的响应组成。
> * **请求消息和响应消息都是由开始行，消息报头（可选），空行（只有CRLF的行），消息正文（可选）组成。对于请求消息，开始行就是请求行，对于响应消息，开始行就是状态行**

### 1.6 JSP与Servlet

> * 我们可以将Servlet看作是嵌套了HTML代码的Java类；可以将JSP看作是嵌套了Java代码的HTML页面。

### 1.7 get请求与post请求区别

> 

## 2. JSP语法概述

> * JSP原始代码中包含了JSP元素和Template(模板) data两类
> * Template data指的是JSP引擎不处理的部分，即标记`<%……%>`以外的部分，例如代码中的HTML的内容等 ，这些数据会直接传送到客户端的浏览器
> * JSP元素则是指将由JSP引擎直接处理的部分，这一部分必须符合JAVA语法，否则会导致编译错误。

### 2.1 jsp语法

> * 编译器指令(DIRECTIVE) 例如： `<%@ page import=“java.io.*”` 
> * 脚本语法(SCRIPTING) 例如 ：`<% .... %>`中的java代码
> * 动作语法(ACTION) 例如： `<jsp:forward>，<jsp:getProperty>，<jsp:include>`

### 2.2 编译器指令

> * 编译器指令包括**“包含指令”,“页指令”和“taglib指令”**
> * 它们包含在“`<%@ %>`”卷标里。
> * 两个主要的指令是**page**与**include**

### 2.3 jsp声明 

> * `<%! declaration; [ declaration; ] ...%>` 或 `<%! 声明; [声明; ] ... %>`
>
> * 用声明方式与脚本段方式定义的变量是不一样的，**用声明方式定义的变量在jsp被解析成Servlet对象后是一个成员变量，而使用脚本段方式定义的变量是一个局部变量。**
>
> * **因为Servlet是单例的，所以每次刷新页面的时候，使用声明方式定义的变量的值不会再生成，而使用脚本段方式定义的变量因为是一个局部变量，所以每次刷新页面的时候，都会重新生成。**
>
> * 通常使用脚本段方式定义变量，因为可以确保在多个用户访问同一个jsp页面时，不会造成数据被另一个访问用户所修改，而影响其他访问用户
>
> * ```jsp
>   <%! int i = 0; %>
>   <%! int a, b, c; %>
>   <%! Date date; %>
>   ```

### 2.4 表达式

> * 用于在页面上输出信息，语法格式如下：`<%= expression %>` 或`<%= 表达式 %>`

### 2.5 脚本段

> `<% ... %>`

### 2.6 包含指令不可以传递参数

> * `include`指令：向当前页中插入一个静态文件的内容
>
> * JSP 语法格式如下：
>
> * `<%@ include file="relativeURL" %>` 或 `<%@ include file="相对位置" %>`
>
> * ```jsp
>   <%@ include file = "include.jsp" %>
>   ```

### 2.7 转向指令

> * `<jsp:forward>`标签从一个JSP文件向另一个文件传递一个包含用户请求的request对象。
> * `<jsp:forward>`标签以后的代码，**将不能执行**

```jsp
	<!-- 转向指令 -->
	<jsp:forward page="forwardTo.jsp">
        <!-- 可以携带参数 -->
		<jsp:param name="userName" value="lijun"/>
		<jsp:param name="password" value="kiss84399"/>
	</jsp:forward>
```

### 2.8 包含指令可以传递参数

> * `<jsp:include>` 可以包含静态或动态文件
>
> * ```jsp
>   	<!-- 转向指令 -->
>     	<jsp:include page="include.jsp">
>     		<!-- 传递参数 -->
>     		<jsp:param name="userName" value="lijun"/>
>     		<jsp:param name="password" value="kiss84399"/>
>     	</jsp:include>
>   ```

### 2.9 JSP九种内置对象

> 1. **request，请求对象**
> 2. response，响应对象
> 3. pageContext，页面上下文对象
> 4. **session，会话对象**
> 5. **application，应用程序对象**
> 6. out，输出对象
> 7. config，配置对象
> 8. page，页面对象
> 9. exception，异常对象

#### 2.9.1 request和response

> * “`request`” 对象代表的是来自客户端的请求，例如我们在FORM表单中填写的信息等，是最常用
>   的对象 。 关 于 它 的 方 法 使 用 较 多 的 是`getParameter 、 getParameterNames` 和`getParameterValues`，通过调用这几个方法来获取请求对象中所包含的参数的值
> * “`response`” 对象代表的是对客户端的响应，也就是说可以通过“`response`”对象来组织发送到客户端的数据。但是由于组织方式比较底层，所以不建议普通读者使用，需要向客户端发送文字时直接使用“`out`” 对象即可

#### 2.9.2 pageContext

> * “`pageContext`” 对象直译时可以称作“页面上下文”对象，代表的是当前页面运行 的 一 些 属 性 ， 常 用 的 方 法 包 括`findAttribute 、 getAttribute 、getAttributesScope` 和`getAttributeNamesInScope`，一般情况下“`pageContext`” 对象用到得也不是很多，只有在项目所面临的情况比较复杂的情况下，才会利用到页面属性来辅助处理。一般`Servlet`容器会使用该对象

#### 2.9.3 session

> * session” 对象代表服务器与客户端所建立的会话，当需要在不同的JSP页面中保留客户信息的情况下使用，比如在线购物、客户轨迹跟踪等
> * HTTP是无状态（stateless）协议；
> * Web Server 对每一个客户端请求都没有历史记忆；
> *  **Session用来保存客户端状态信息**；

#### 2.9.4 application

> * “application” 对象负责提供应用程序在服务器中运行时的一些全局信息，常用的方法有`getMimeType`和`getRealPath`等。

#### 2.9.5 out

> * “`out`” 对象代表了向客户端发送数据的对象，与“response” 对象不同，通过“out” 对象发送的内容将是浏览器需要显示的内容，是文本一级的，可以通过“out” 对象直接向客户端写一个由程序动态生成HTML文件。常用的方法除了print和println之外，还包括`clear、clearBuffer 、 flush 、 getBufferSize` 和`getRemaining`，这是因为“`out`” 对象内部包含了一个缓冲区，所以需要一些对缓冲区进行操作的方

#### 2.9.6 config、page、exception

> * “config” 对象提供一些配置信息，常用的方法有`getInitParameter`和`getInitParameterNames`，以获得Servlet初始化时的参数
> * “page” 对象代表了正在运行的由JSP文件产生的类对象，不建议一般读者使用。
> * “exception” 对象则代表了JSP文件运行时所产生的异常对象，此对象不能在一般JSP文件中直接使用 ， 而只能在使用了 `“ <%@ pageisErrorPage="true "%>”`的JSP文件中使用,表示当前的页面是一个异常页面。

#### 2.9.7 request请求对象

> * request的setAttribute与getAttribute方法一般都是成对出现的，首先通过setAttribute方法设置属性与属性值，然后通过getAttribute方法根据属性获取到与该属性对应的对象值（**获取到之后一般都需要进行向下类型转换，将属性值转换为真正的对象**）。setAttribute与getAttribute方法都是在服务器端内部执行的，客户端不知道服务器端是否执行过这两个方法。
> * request的getParameter方法的作用是获取到客户端 通过表单或url请求参数所发送过来的参数值，是客户端与服务器端之间的交互，服务器端要想获取到客户端发送过来的数据，就需要使用getParameter方法来获取。**没有与getParameter方法对应的setParameter方法**。
> * request对象内数据的存活范围就是在request对象的存活范围内，当客户端向服务器端发送一个请求，服务器向客户端返回一个响应后，该请求对象就被销毁了；之后再向服务器端发送新的请求时，服务器会创建新的request对象，该request对象与之前的request对象没有任何关系，因此也无法获得在之前的request对象中所存放的任何数据。
> * session对象内数据的存活范围也就是session对象的存活范围（**只要浏览器不关闭，session对象就会一直存在?**），因此在同一个浏览器窗口中，无论向服务器端发送多少个请求，session对象只有一个。
> * application（应用对象）：存活范围最大的对象，只要服务器没有关闭，application对象中的数据就会一直存在。**在整个服务器运行过程当中，application对象只有一个**。
> * request、session以及application这3个对象的范围是逐个增加的：request只在一个请求的范围内；**session是在浏览器窗口的范围内？**；application则是在整个服务器的运行过程中。

### 2.10 请求转发和重定向

> * HttpServletResponse对象的sendRedirect(String location)方法称作**重定向**。如果location地址前面加上“/”，则表示相对于Servlet容器的根来请求，即http://localhost:8080，如果location地址前没有加上“/”，则表示相对于当前请求的URI来寻找地址,即 http://localhost:8080/mytest/.....jsp。
>
> * RequestDispatcher的forward（request, response）方法称作**请求转发**。
>
>   * ```java
>     request.getRequestDispatcher("myResult.jsp").forward(request, response);
>     ```
>
> * **请求转发，整个过程处于同一个请求当中**
>
> * **重定向。实际上客户端会向服务器端发送两个请求**。
>
> * RequestDispatcher是通过调用HttpServletRequest对象的`getRequestDispatcher`()方法得到的，是属于请求对象的方法。
>
> * sendRedirect()是HttpServletResponse对象的方法，即响应对象的方法，既然调用了响应对象的方法，那就表明整个请求过程已经结束了，服务器开始向客户端返回执行的结果。
>
>   * ```java
>     response.sendRedirect("myResult.jsp");
>     ```
>

### 2.11 使用js对客户端验证

> ```jsp
> <script type="text/javascript">
> 		//对表单进行验证
> 		function validate(){
> 			var username = document.getElementsByName("username")[0];
> 			var password = document.getElementsByName("password")[0];
> 			var gender = document.getElementsByName("gender");
> 			var interest = document.getElementsByName("interest");
> 			var commont = document.getElementsByName("commont")[0];
> 			if(0 == username.value.length){
> 				alert("用户名不能为空");
> 				return false;
> 			}
> 			if(username.value.length < 4 || username.value.length > 10){
> 				alert("用户名介于4到10");
> 				return false;
> 			}
> 			if(0 == password.value.length){
> 				alert("密码不能为空");
> 				return false;
> 			}
> 			if(password.value.length < 4 || password.value.length > 10){
> 				alert("密码介于4到10");
> 				return false;
> 			}
> 			//验证性别
> 			if(!gender[0].checked && !gender[1].checked){
> 				alert("性别必须要选择");
> 				return false;
> 			}
> 			//验证复选框,至少选一个
> 			var n = 0;
> 			for(var i = 0;i < interest.length;i++){
> 				if(interest[i].checked){
> 					n++;
> 				}
> 			}
> 			if(n < 1){
> 				alert("兴趣至少选一个");
> 				return false;
> 			}
> 			//判读testarea
> 			if(0 == commont.value.length){
> 				alert("评论必须要填写");
> 				return false;
> 			}
> 			return true;
> 		}
> 	</script>
> <body>
> 	<form onsubmit="return validate()">
> 		用户名:<input type="text" name="username"><br>
> 		密码:<input type="password" name="password"><br>
> 		性别: 男:<input type="radio"  name="gender" value="男">&nbsp;&nbsp;&nbsp;女:				<input type="radio" name="gender" value="女"><br>
> 		兴趣:足球<input type="checkbox" name="interest" value="足球">&nbsp;&nbsp;
> 		篮球<input type="checkbox" name="interest" value="篮球">&nbsp;&nbsp;
> 		排球<input type="checkbox" name="interest" value="排球">&nbsp;&nbsp;
> 		羽毛球<input type="checkbox" name="interest" value="羽毛球">&nbsp;&nbsp;
> 		乒乓球<input type="checkbox" name="interest" value="乒乓球">&nbsp;&nbsp;<br>
> 		地址:<select name="address">
> 			<option value="上海">上海</option>
> 			<option value="南京">南京</option>
> 			<option value="西安">西安</option>
> 			<option value="武汉">武汉</option>
> 		</select><br>
> 		说明:<textarea name="commont" cols="20" rows="15"></textarea><br>
> 		<input type="submit" value="submit"/>
> 	</form>
> </body>
> </html>
> ```
>
> 

## 3. JavaBean

> * JavaBean是一种可重复使用、且跨平台的软件组件。JavaBean可分为两种：一种是
>   有用户界面（UI，User Interface）的JavaBean；**还有一种是没有用户界面，主要负责处理事务（如数据运算，操纵数据库）的JavaBean**。JSP通常访问的是后一种JavaBean。

### 3.1 JSP与JavaBean搭配使用的优点

> * 使得HTML与Java程序分离，这样便于维护代码。如果把所有的程序代码都写到JSP网页中，会
>   使得代码繁杂，难以维护
> * 可以降低开发JSP网页人员对Java编程能力的要求
> * JSP侧重于生成动态网页，事务处理由JavaBean来完成，这样可以充分利用JavaBean组件的可重用性特点，提高开发网站的效率

### 3.2 JavaBean的特征

> * 一个标准的JavaBean有以下几个特性：
>   * – JavaBean是一个公共的（public）类
>   * – JavaBean有一个不带参数的构造方法
>   * – JavaBean通过setXXX方法设置属性，通过getXXX方法获取属性

### 3.3 使用javaBean

> 1. 导入javaBean类
>
>    ```jsp
>    <%@ page import="mypack.CounterBean" %>
>    ```
>
> 2. 声明javaBean对象
>
>    ```jsp
>    <jsp:useBean>标签用来声明JavaBean 如： 
>    <jsp:useBean id="myBean" class="mypack.CounterBean" scope="session" />
>    ```
>
> 3. 使用javaBena
>
> 4. 可以在<jsp:setProperty>中通过param参数为bean的属性动态赋值。由http参数传进去
>
>    * ```jsp
>      <jsp:setProperty property="age" name="person" param="helloworld"/>
>      ```
>
>    * 对应的Servlet代码是:
>
>      ![image-20200308151115656](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200308151115656.png)
>
>    ```jsp
>    	<!-- 定义javaBean对象 -->
>    	<jsp:useBean id="person" class="com.njcit.www.entity.Person" scope="session">	</jsp:useBean>
>    	<!-- 为javaBean属性设置值 -->
>    	<jsp:setProperty property="name" name="person" value="hello"/>
>    	<jsp:setProperty property="age" name="person" value="5"/>
>    	<jsp:setProperty property="address" name="person" value="南京"/>
>    	 <!-- 获取javaBean中定义的属性 -->
>    	<jsp:getProperty property="name" name="person"/>
>    	<jsp:getProperty property="age" name="person"/>
>    	<jsp:getProperty property="address" name="person"/>
>    ```

#### 3.4 javaBean范围

> * scope属性决定了JavaBean对象存在的范围,scope的可选值包括：
>   * page（默认值）
>   *  request
>   *  session
>   *  application

#### 3.5 javaBean在page范围内

> * 客户每次请求访问JSP页面时，都会创建一个JavaBean对象。JavaBean对象的有效范围是客户请求访问的当前JSP网页。JavaBean对象在以下两种情况下都会结束生命期：
>   * 客户请求访问的当前JSP网页通过<forward>标记将请求转发到另一个文件
>   * 客户请求访问的当前JSP页面执行完毕并向客户端发回响应。

#### 3.6 javaBean在request范围内

> * 客户每次请求访问JSP页面时，都会创建新的JavaBean对象。JavaBean对象的有效范围为：
>
>   * 客户请求访问的当前JSP网页
>
>   * 和当前JSP网页共享同一个客户请求的网页，即当前JSP网页中<%@ include>指令以及<forward>标记包含的其他JSP文件
>
>   * 当所有共享同一个客户请求的JSP页面执行完毕并向客户端发回响应时，JavaBean对象结束生命周期。
>
>   * JavaBean对象作为属性保存在HttpRequest对象中，属性名为JavaBean的id，属性值为 JavaBean 对 象 ， 因 此 也 可 以 通 过HttpRequest.getAttribute() 方法取得JavaBean对象，例如：
>
>     * ```java
>       CounterBean obj = (CounterBean)request.getAttribute(“myBean”);
>       ```

#### 3.7 javaBean在session范围内

> * JavaBean对象被创建后，它存在于整个Session的生存周期内，同一个Session中的JSP文件共享这个JavaBean对象。
> * JavaBean对象作为属性保存在HttpSession对象中，属性名为JavaBean的id，属性值为JavaBean对象。除了可以通过JavaBean的id直接引用 JavaBean 对象外，也可以通过HttpSession.getAttribute() 方法取得JavaBean对象

#### 3.8 javaBean在application范围内

> * JavaBean对象被创建后，它存在于整个Web应用的生命周期内，Web应用中的所有JSP文件都能共享同一个JavaBean对象。

## 4. Servlet

> * Java Servlet是和平台无关的服务器端组件，它运行在Servlet容器中。Servlet容器负责Servlet和客户的通信以及调用Servlet的方法，Servlet和客户的通信采用“请求/响应”的模式
> * 与其它服务器资源（如数据库或基于Java的应用程序）进行通信
> * Servlet的框架是由两个Java包组成
>   * javax.servlet包：定义了所有的Servlet类都必须实现或扩展的通用接口和类。
>   * javax.servlet.http 包 ： 定 义 了 采 用 HTTP 协 议 通 信 的HttpServlet类。
> * Servlet的框架的核心是javax.servlet.Servlet接口，所有的Servlet都必须实现这一接口。在Servlet接
>   口 中 定 义 了 五 个 方 法 ， 其 中 有 三 个 方 法 代 表 了Servlet的生命周期:
>   * **init方法：负责初始化Servlet对象**
>   * **service方法：负责响应客户的请求；**
>   * **destroy方法：当Servlet对象退出生命周期时，负责释放占用的资源**
> * 每一个Servlet都必须要实现Servlet接口，GenericServlet是个通用的、不特定于任何协议的Servlet，它实现了Servlet接口，而HttpServlet继承于GenericServlet，因此HttpServlet也实现了Servlet接口，所以我们定义的Servlet只需要继承HttpServlet父类即可。
> * Servlet接口中定义了一个service方法，HttpServlet对该方法进行了实现，实现方式就是将ServletRequest与ServletResponse转换为HttpServletRequest与HttpServletResponse
>
> * ![image-20200308192833032](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200308192833032.png)
> * 转换完毕后，会调用HttpServlet类中自己定义的service方法，如下所示
> * ![image-20200308192917271](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200308192917271.png)
> * 在该service方法中，首先获得到请求的方法名，然后根据方法名调用对应的doXXX方法，比如说请求方法为GET，那么就去调用doGet方法；请求方法为POST，那么就去调用doPost方法。
> * ![image-20200308193010661](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200308193010661.png)
> * 在HttpServlet类中所提供的doGet、doPost等方法都是直接返回错误信息，所以我们需要在自己定义的Servlet类中重写这些方法
> * ![image-20200308193039101](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200308193039101.png)

![image-20200308172039155](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200308172039155.png)

### 4.1 HttpServlet的启动

> * 如果你的Servlet类扩展了HttpServlet类，你通常不必实现service方法，因为HttpServlet类已经实现了service方法，该方法的声明形式如下:
>   * ![image-20200309195237376](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200309195237376.png)
> * 在 HttpServlet 的 service 方 法 中 ， 首先从`HttpServletRequest`对象中获取HTTP请求方式的信息，然后再根据请求方式调用相应的方法。例如：如果请求方式为GET，那么调用doGet方法；如果请求方式为POST，那么调用doPost方法

### 4.2 ServletRequest

> * `ServletRequest`接口中**封装了客户请求信息**，如客户请求方式、参数名和参数值、客户端正在使用的协议,以及发出客户请求的远程主机信息等 。`ServletRequest`接口还为Servlet提供了直接以二进制方式读取客户请求数据流的`ServletInputStream`。
> * `ServletRequest`的子类可以为Servlet提供更多的和特定协议相关的数据. 例如: `HttpServletRequest` 提供了读取HTTP Head信息的方法
> * 主要方法
>   * getAttribute 根据参数给定的属性名返回属性值
>   * getContentType 返回客户请求数据MIME类型
>   * getInputStream 返回以二进制方式直接读取客户请求数据的输入流 getParameter 根据给定的参数名返回参数值
>   * getRemoteAddr 返回远程客户主机的IP地址
>   * getRemoteHost 返回远程客户主机名
>   * getRemotePort 返回远程客户主机的端口

### 4.3 ServletResponse

> * ServletResponse 接口为Servlet提供了返回响应结果的方法。它允许Servlet设置返回数据的长度和MIME类型, 并且提供输出流ServletOutputStream。
> * ServletResponse子类可以提供更多和特定协议相关的方法。例如: HttpServletResponse 提供设定HTTP HEAD信息的方法。
> * 主要方法
>   * getOutputStream 返回可以向客户端发送二进制数据的输出流对象ServletOutputStream
>   * getWriter 返回可以向客户端发送字符数据的PrintWriter对象
>   * getCharacterEncoding 返回Servlet发送的响应数据的字符编码
>   * getContentType 返回Servlet发送的响应数据的MIME类型
>   * setContentType 设置Servlet发送的响应数据的MIME类型

#### 4.3.1 响应的乱码解决

> * ```java
>   // 它会同时设置服务器和客户端都使用 UTF-8 字符集，还设置了响应头
>   // 此方法一定要在获取流对象之前调用才有效
>   resp.setContentType("text/html; charset=UTF-8");
>   ```

### 4.4 Servlet的生命周期

> * Servlet 的生命周期可以分为三个阶段:
>   * 初始化阶段
>   * 响应客户请求阶段
>   *  终止阶段
> * 在javax.servlet.Servlet接口中定义了三个方法init(), service(), destroy()，它们将分别 在 Servlet 的 不 同 阶 段 被 调 用 

### 4.5 Servlet的初始化

> * 在下列时刻Servlet容器装载Servlet：
>
>   * Servlet容器启动时自动装载某些Servlet,数字越小，优先级越高
>
>     * ```xml
>        <servlet>
>           <servlet-name>InitServlet</servlet-name>
>           <servlet-class>com.njcit.www.servlet.InitServlet</servlet-class>
>           <load-on-startup>4</load-on-startup>
>         </servlet>
>       ```
>
>   * 在Servlet容器启动后，客户首次向 Servlet 发出请求
>
>   * Servlet的类文件被更新后，重新装载Servlet 
>
> * Servlet被装载后，Servlet容器创建一个 Servlet 实例并且调用 Servlet 的 init()方法进行初始化。在
>   Servlet的整个生命周期中，init方法只会被调用一次
>   
> * 某些Servlet在web.xml文件中只有<servlet>元素而没有<servlet-mapping>元素，这样我们就无法通过url地址的方式访问这个Servlet了，这种Servlet通常会在<servlet>元素中配置一个<load-on-startup>子元素，让容器在启动的时候自动加载该Servlet，并且调用其init方法完成一些全局性的初始化工作。

### 4.5 Servlet的响应客户请求阶段

> * 对于到达Servlet容器的客户请求，Servlet容器创建特定于这个请求的ServletRequest对象和ServletResponse对象，然后调用 Servlet 的service方法。service方法从ServletRequest对象获得客户请求信息、处理该请求，并通过ServletResponse对象向客户返回响应结果。
>
> * 对于Tomcat来说，它会将客户传递过来的参数放在一个Hashtable中，该Hashtable的定义是：
>
>   * ![image-20200314224803581](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200314224803581.png)
>
>   * 这是一个String->String[]的键值映射。
>

### 4.6 Servlet终止阶段

> * 当Web应用被终止，或Servlet容器终止运行，或Servlet容器重新装载Servlet的新实例时，Servlet容器会先调用 Servlet的destroy方法。在destroy方法中，可以释放Servlet所占用的资源

### 4.7 ServletContext(application)与Web

> * 当Servlet容器启动Web应用时，并为每个Web应用创建惟一的ServletContext对象。你可以把ServletContext看成是一个Web应用的服务器端组件的共享内存。在ServletContext中可以存放共享数据，它提供了读取或设置共享数据的方法
>   * setAttribute（String name,Object object）把一个对象和一个属性名绑定，将这个对象存储在ServletContext中。
>   * getAttribute（String name）根据给定的属性名返回所绑定的对象
>   
> * ServletContext的四个作用
>
>   1、获取 web.xml 中配置的上下文参数 context-param
>   2、获取当前的工程路径，格式: /工程路径
>   3、获取工程部署后在服务器硬盘上的绝对路径
>   4、像 Map 一样存取数据
>
>   * ```java
>     @Override
>     	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>     		ServletContext context = req.getServletContext();
>     		//获取 web.xml 中配置的上下文参数 context-param
>     		System.out.println(context.getInitParameter("hello"));
>     		//获取当前的工程路径，格式: /工程路径
>     		String path = context.getContextPath();
>     		System.out.println(path);
>     		//获取工程部署后在服务器硬盘上的绝对路径
>     		String realPath = context.getRealPath("/");
>     		System.out.println(realPath);
>     		//像 Map 一样存取数据
>     		context.setAttribute("hello", "world");
>     	}
>     ```

### 4.8 Servlet的多线程同步问题

> * Servlet/JSP技术和ASP、PHP等相比，由于其多线程运行而具有很高的执行效率
> * 由于Servlet/JSP默认是以多线程模式执行的，所以，在编写代码时需要非常细致地考虑多线程的同步问题。
> * 如果在编写Servlet/JSP程序时不注意到多线程同步的问题，这往往造成编写的程序在少量用户访问时没有任何问题，而在并发用户上升到一定值时，就会经常出现一些莫明其妙的问题，对于这类随机性的问题调试难度也很大
> * **Servlet本身是单实例,多线程的，这样当有多个用户同时访问某个Servlet时，会访问该唯一的Servlet实例中的成员变量，如果对成员变量进行写入操作，那就会导致Servlet的多线程问题，即数据不一致**
> * 解决Servlet多线程同步问题的最好方案：**去除实例变量，使用局部变量**

### 4.9 ServletConfig类

> * ServletConfig 类从类名上来看，就知道是 Servlet 程序的配置信息类。
> * Servlet 程序和 ServletConfig 对象都是由 Tomcat 负责创建，我们负责使用。
> * Servlet 程序默认是第一次访问的时候创建，S**ervletConfig 是每个 Servlet 程序创建时，就创建一个对应的 ServletConfig 对象**

#### 4.9.1 ServletConfig的作用

> * 可以获取 Servlet 程序的别名 servlet-name 的值
>
> * 获取初始化参数 init-param
>
> * 获取 ServletContext 对象
>
> * ```java
>   @WebServlet(name = "SecondServlet",urlPatterns = {"/SecondServlet"},
>   initParams = {@WebInitParam(name="hello",value="world"),@WebInitParam(name="lijun",value="java")})
>   public class SecondServlet extends HttpServlet{
>   	
>   	@Override
>   	public void init(ServletConfig config) throws ServletException {
>   		//可以获取 Servlet 程序的别名 servlet-name 的值
>   		System.out.println(config.getServletName());
>   		//获取初始化参数 init-param
>   		System.out.println(config.getInitParameter("hello"));
>   		System.out.println(config.getInitParameter("lijun"));
>   		//获取 ServletContext 对象
>   		System.out.println(config.getServletContext());
>   	}
>   ```

## 5. Cookie

> * Cookie的英文原意是“点心”，它是用户访问Web服务器时，服务器在用户硬盘上存放的信息，好像是服务器送给客户的“点心”
>
> * 服务器可以根据Cookie来跟踪用户，这对于需要区别用户的场合（如电子商务）特别有用
>
> * 一个Cookie包含一对Key/Value。下面的代码生成一个Cookie并将它写到用户的硬盘上
>
>   * ```java
>     Cookie theCookie=new Cookie("cookieName","cookieValue");
>     //将Cookie对象放入响应对象是，发送给客户端
>     response.addCookie(the Cookie);
>     //从请求中获取Cookies对象数组
>     Cookie[] cookies = request.getCookies();
>     ```

## 6. Session

> * Web服务器跟踪客户状态通常有四种方法
>   * 建立含有跟踪数据的隐藏字段
>   *  重写包含额外参数的URL
>   *  使用持续的Cookie
>   *  使用Servlet API中的Session（会话）机制

### 6.1 Session概念

> * Session用于跟踪客户的状态。Session指的是在一段时间内，单个客户与Web服务器的一连串相关的交互过程 。 在 一 个Session中，客户可能会多次请求访问同一个网页，也有可能请求访问各种不同的服务器资源

### 6.2 Session的运行机制

> * 当一个 Session 开 始 时 ， Servlet 容 器 将 创 建 一 个HttpSession对象，在HttpSession对象中可以存放客户状态的信息（例如购物车）。
> *  Servlet容器为HttpSession分配一个惟一标志符，称为Session ID。**Servlet容器把Session ID作为Cookie保存在客户的浏览器中。**
> * **每次客户发出 HTTP 请求时 ，Servlet 容器可以从HttpServletRequest对象中读取Session ID，然后根据Session ID找到相应的HttpSession对象，从而获取客户的状态信息**

![image-20200315153112355](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200315153112355.png)

### 6.3 HttpSession接口

> * `getId()`  返回Session的ID
> * `invalidate()`  使当前的Session失效，Servlet容器会释放HttpSession对象占用的资源
> * `setAttribuate(String name, Object value)`  将一对name/value属性保存在HttpSession对象中
> * `getAttribute(String name)` 根据name参数返回保存在HttpSession对象中的属性值
> * `isNew()` 判断是否是新创建的Session。如果是新创建的Session，返回true，否则返回false
> * `setMaxInactiveInterval()` 设定一个Session可以处于不活动状态的最大时间间隔，以秒为单位。如果超过这个时间，Session自动失效。如果设置为负数，表示不限制Session处于不活动状态的时间,默认时间是1800s,即30分钟。

### 6.4 Session的生命周期

> * 当客户第一次访问Web应用中支持Session的某个网页时，就会开始一个新的Session。
>
> * **接下来当客户浏览这个Web应用的不同网页时，始终处于同一个Session中。**
>
> * 默认情况下，JSP网页都是支持Session的，也可以通过以下语句显式声明支持Session：
>
>   * ```html
>     <%@ page session= "true">
>     ```
>
> * 在以下情况中，Session将结束生命周期，Servlet容器会将Session所占用的资源释放掉：
>
>   * **客户端关闭浏览器(真的这样吗?)**
>   * Session过期
>   *  服务器端调用了HttpSession的`invalidate()`方法

### 6.5 Session过期 

> * Session过期是指当Session开始后，在一段时间内客户没有和Web服务器交互，这个Session会失效，HttpSession的`setMaxInactiveInterval()`方法可以设置允许Session保持不活动状态的时间（以秒为单位），如果超过这一时间，Session就会失效。

## 7. 过滤器

> * Servlet过滤器是在Java Servlet规范2.3中定义的，它能够对Servlet容器的请求和响应对象进行检查和修改。
> * **Servlet过滤器本身并不生成请求和响应对象，它只提供过滤作用。**
> * **Servlet过滤器能够在Servlet被调用之前检查Request对象，修改Request Header和Request内容；**
> * **在Servlet被调用之后检查Response对象，修改Response Header和Response内容。Servlet过滤器负责过滤的Web组件可以是Servlet、JSP或HTML文件**

### 7.1 过滤器的过滤过程

![image-20200322132813465](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200322132813465.png)

### 7.2 Filter接口

> * 所有的 Servlet 过 滤 器 类 都 必 须 实 现`javax.servlet.Filter`接口。这个接口含有
>   3个过滤器类必须实现的方法：
>   * `init()`
>   * `doFilter()`
>   * `destroy`
> * `init(FilterConfig)`：这是Servlet过滤器的初始化方法，Servlet容器创建Servlet过滤器实例后将调用这个方法。**在这个方法中可以读取web.xml 文件中Servlet过滤器的初始化参数**
> * `doFilter(ServletRequest, ServletResponse,FilterChain)`：这个方法完成实际的过滤操作。**当客户请求访问与过滤器关联的URL时，Servlet容器将先调用过滤器的doFilter方法。FilterChain参数用于访问后续过滤器**
> * `destroy()`：Servlet容器在销毁过滤器实例前调用该方法，在这个方法中可以释放Servlet过滤器占用的资源

### 7.3 发布Servelt过滤器

> * 发布Servlet过滤器时，必须在web.xml文件中加入<filter>元素和<filter-mapping>元素。<filter>元素用来定义一个过滤器：
>
> * ````xml
>       <filter>
>           <filter-name>LoginFilter</filter-name>
>           <filter-class>filter.LoginFilter</filter-class>
>       </filter>
>       <filter-mapping>
>           <filter-name>LoginFilter</filter-name>
>           <url-pattern>/*</url-pattern>
>       </filter-mapping>
>   ````

### 7.4 串联Servlet过滤器工作流程

> ![image-20200323183253476](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200323183253476.png)

### 7.5 配置串联过滤器

> * ```xml
>   	<filter>
>           <filter-name>myFilter1</filter-name>
>           <filter-class>filter.MyFilter1</filter-class>
>       </filter>
>       <filter>
>           <filter-name>myFilter2</filter-name>
>           <filter-class>filter.MyFilter2</filter-class>
>       </filter>
>     
>       <filter-mapping>
>           <filter-name>myFilter1</filter-name>
>           <url-pattern>/InfoServlet</url-pattern>
>       </filter-mapping>
>       <filter-mapping>
>           <filter-name>myFilter2</filter-name>
>           <url-pattern>/InfoServlet</url-pattern>
>       </filter-mapping>
>   ```
>
> * 两个串联过滤器的执行结果
>
>   * ![image-20200323190002241](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200323190002241.png)
>
> * 多个串联过滤器的执行顺序，主要看在web.xml中配置的<filter-mapping>先后顺序

## 8. 监听器

> * Listener是Servlet的监听器，它可以监听客户端的请求、服务端的操作等。通过监听器，可以自动激发一些操作，**比如监听在线的用户的数量。**
> * 当增加一个HttpSession时，就激发sessionCreated(HttpSessionEvent se)方法，这样就可以给在线人数加1 
> * **监听器的创建会在Filter(过滤器)之前被创建**
> * 常用的监听接口有以下几个
>   * `ServletContextListener`会在ServletContext创建和销毁时触发
>   * ![image-20200323194809326](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200323194809326.png)
>   * `ServletContextAttributeListener` 监听对ServletContext属性的操作，比如增加、删除、修改属性
>   * ![image-20200323200032886](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200323200032886.png)
>   * `HttpSessionListener`监听HttpSession的操作。当创建一个Session时，激发`session Created(HttpSessionEvent se)`方法；当销毁一个Session时，激发`sessionDestroyed (HttpSessionEvent se)`方法
>   * ![image-20200324133131149](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200324133131149.png)
>   * `HttpSessionAttributeListener`监听HttpSession中的属性的操作。当在Session增加一个属性时，激发`attributeAdded(HttpSessionBindingEvent se)` 方法；当在Session删除一个属性时，激发`attributeRemoved(HttpSessionBindingEvent se)`方法；当在Session属性被重新设置时，激发`attributeReplaced(HttpSessionBindingEvent se)` 方法
>   * ![image-20200324134309570](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200324134309570.png)

### 8.1 配置监听器

> * ```xml
>       <listener>
>           <listener-class>listener.MyServletContextListener</listener-class>
>       </listener>
>       <listener>
>           <listener-class>listener.MyServletContextAttributeListener</listener-class>
>       </listener>
>       <listener>
>           <listener-class>listener.MyHttpSessionAttributeListener</listener-class>
>       </listener>
>   ```

## 9. EL表达式语言

### 9.1 EL内置对象

> * EL 语法很简单，它最大的特点就是使用上很方便  `${sessionScope.user.sex}`
> * 所有EL都是以 ${ 为起始、以} 为结尾的。上述EL范例的意思是：从Session的范围中，取得用户的性别
> * EL 提供 . 和 [ ] 两种运算符来存取数据。下列两者所代表的意思是一样的
>   * `${sessionScope.user.sex}` 等于`${sessionScope.user["sex"]}`
> * . 和 [ ] 也可以同时混合使用，如下：
>   * `${sessionScope.shoppingCart[0].price}`回传结果为shoppingCart中第一项物品的价格
> * 两者差异
>   * **当要存取的属性名称中包含一些特殊字符，如 . 或 – 等并非字母或数字的符号，就一定要使用 [ ]**，例如：`${user.My-Name }`,应当改为：`${user["My-Name"] }`
>   * `${sessionScope.user[data]}`此时，data 是一个变量，假若data的值为"sex"时，那上述的例子等于`${sessionScope.user.sex}`；假若data 的值为"name"时，它就等于`${sessionScope.user.name}`。因此，**如果要动态取值时，就可以用上述的方法来做，但 . 无法做到动态取值。**

### 9.2 EL变量

> * EL 存取变量数据的方法很简单，例如：${username}。它的意思是取出某一范围中名称为username的变量。因为我们并没有指定哪一个范围的username，**所以它的默认值会先从Page 范围找，假如找不**
>   **到，再依序到Request、Session、Application范围。假如途中找到username，就直接回传，不再继续找下去**，但是假如全部的范围都没有找到时，就回传null
> * ![image-20200324142424663](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200324142424663.png)
> * **我们也可以指定要取出哪一个范围的变量**
> * ![image-20200324142457712](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200324142457712.png)

### 9.3 自动类型转换

> * EL 除了提供方便存取变量的语法之外，它另外一个方便的功能就是：自动转变类型
> * ${param.count + 20}会将传来的count自动转化为数值

### 9.4 EL隐含对象

> * 与范围有关的EL 隐含对象包含以下四个：`pageScope、requestScope、sessionScope 和applicationScope`，它们基本上就和JSP的`pageContext、request、session和applicatio`n一样，不过必须注意的是，这四个隐含对象只能用来取得范围属性值，即JSP中的getAttribute(String name)，却不能取得其他相关信息，例如：JSP中的request对象除可以存取属性之外，还可以取得用户的请求参数或表头信息等等。**但是在EL中，它就只能单纯用来取得对应范围的属性值**，例如：我们要在session 中储存一个属性，它的名称为username，在JSP 中使用`session.getAttribute(“username”)` 来取得username 的值， 但是在EL 中， 则是使用`${sessionScope.username}`来取得其值的
> * ![image-20200324144015804](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200324144015804.png)
> * ![image-20200324144054327](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200324144054327.png)

## 10. JSP标签

> * 客户化JSP标签技术是在JSP 1.1版本中才出现的，它支持用户在JSP文件中自定义标签，这样可以使JSP代码更加简洁。
> * 这些可重用的标签能处理复杂的逻辑运算和事务，或者定义JSP网页的输出内容和格式

### 10.1 创建JSP标签的步骤

> 1. 创建标签的处理类
> 2. 创建标签库描述文件，**标签库描述文件的后缀名是：.tld**
> 3. 在JSP文件中引入标签库，然后插入标签，例如:`<mm:hello/>`

### 10.2 自定义标签

> * Servlet容器编译JSP网页时，如果遇到自定义标签，就会调用这个标签的处理类。标签处理类必须扩展以下两个类之一
>   * `javax.servlet.jsp. tagext .TagSupport`
>   * `javax.servlet.jsp.tagext.BodyTagSupport`
> * `doStartTag`    Servlet容器遇到自定义标签的起始标志时调用该方法
> * `doEndTag`     Servlet容器遇到自定义标签的结束标志时调用该方法

## 11. Servlet3.0新特性

> * 基于注解（Annotation）的配置。
> * 异步支持（AsyncContext）
>   * 整页面刷新（meta）
>   *  Ajax异步请求（轮询，polling）
>   * 反向Ajax（Comet，长轮询，服务器端推技术：server-side pushing）

### 11.1 通过注解定义Servlet

> * ![image-20200325212346882](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200325212346882.png)
> * 通过`@WebInitParam`注解来指定初始化参数 
> * ![image-20200325212706887](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200325212706887.png)

### 11.2 通过注解定义Listener

> * ![image-20200325212824408](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200325212824408.png)

### 11.3 通过注解定义Filter

> * ![image-20200325212843625](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200325212843625.png)

### 11.4 动态注册Servlet

> * ```java
>   @WebListener()
>   public class ServletContextListener4Servlet3 implements ServletContextListener {
>   
>       @Override
>       public void contextInitialized(ServletContextEvent sce) {
>           System.out.println("context init");
>           //动态注册servlet
>           ServletContext servletContext = sce.getServletContext();
>           //将ThirdServlet注册成一个Servlet对象
>           ServletRegistration.Dynamic thirdServlet = servletContext.addServlet("ThirdServlet", ThirdServlet.class);
>           //设置thirdServlet的初始参数
>           thirdServlet.setInitParameter("hello", "world");
>           //为ThirdServlet设置访问路径
>           thirdServlet.addMapping("/ThirdServlet");
>       }
>   
>       @Override
>       public void contextDestroyed(ServletContextEvent sce) {
>           System.out.println("context destroy");
>       }
>   }
>   ```

### 11.5 文件上传

> * ```jsp
>    <form action="MyFileUpLoadServlet" method="post" enctype="multipart/form-data">
>           file:<input type="file" name="myFile"><br>
>           <input type="submit" value="submit">
>       </form>
>   ```
>
> * ```java
>   @WebServlet(name = "MyFileUpLoadServlet",urlPatterns = {"/MyFileUpLoadServlet"})
>   @MultipartConfig()
>   public class MyFileUpLoadServlet extends HttpServlet {
>   
>       @Override
>       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>   
>           req.setCharacterEncoding("utf-8");
>           //获得上传文件对应的信息
>           Part part = req.getPart("myFile");
>           //获得请求头信息
>           String headInfo = part.getHeader("content-disposition");
>           //获得上传文件的名字
>           String filename = headInfo.substring(headInfo.lastIndexOf("=") + 2,headInfo.length() - 1);
>           //将上传的文件写入到指定的位置
>           part.write( "C:/Users/Administrator/IdeaProjects/JavaWeb01/" + filename);
>       }
>   }
>   ```

### 11.6 使用commons-fileupload.jar实现文件上传

> * commons-fileupload.jar 需要依赖 commons-io.jar 这个包，所以两个包我们都要引入
>
> * commons-fileupload.jar 和 commons-io.jar 包中，我们常用的类有哪些？
>
> * ```java
>   ServletFileUpload 类，用于解析上传的数据。
>       
>   FileItem 类，表示每一个表单项。
>       
>   boolean ServletFileUpload.isMultipartContent(HttpServletRequest request);  判断当前上传的数据格式是否是多段的格式。
>       
>   public List<FileItem> parseRequest(HttpServletRequest request)
>   解析上传的数据
>       
>   boolean FileItem.isFormField()
>   判断当前这个表单项，是否是普通的表单项。还是上传的文件类型。
>   true 表示普通类型的表单项
>   false 表示上传的文件类型
>       
>   String FileItem.getFieldName()
>   获取表单项的 name 属性值
>       
>   String FileItem.getString()
>   获取当前表单项的值。
>       
>   String FileItem.getName();
>   获取上传的文件名
>       
>   void FileItem.write( file );
>   将上传的文件写到 参数 file 所指向抽硬盘位置 。 
>   ```

#### 11.6.1 fileupload 类库的使用：

> * 上传文件的表单
>
> * ```jsp
>   <form action="http://192.168.31.74:8080/09_EL_JSTL/uploadServlet" method="post"
>   enctype="multipart/form-data">
>       用户名：<input type="text" name="username" /> <br>
>       头像：<input type="file" name="photo" > <br>
>       <input type="submit" value="上传">
>   </form>
>   ```
>
> * 解析上传的数据的代码：
>
> * ```java
>    @Override
>       protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>   
>           //1 先判断上传的数据是否多段数据（只有是多段的数据，才是文件上传的）
>           if(ServletFileUpload.isMultipartContent(req)){
>               //创建 FileItemFactory 工厂实现类
>               FileItemFactory fileItemFactory = new DiskFileItemFactory();
>               //创建用于解析上传数据的工具类 ServletFileUpload 类
>               ServletFileUpload servletFileUpload = new ServletFileUpload(fileItemFactory);
>               try {
>                   //解析上传的数据，得到每一个表单项 FileItem
>                   List<FileItem> list = servletFileUpload.parseRequest(req);
>                   //循环判断，每一个表单项，是普通类型，还是上传的文件
>                   for (FileItem item : list){
>                       if(item.isFormField()){
>                           //普通表单项
>                           System.out.println("表单项的 name 属性值：" + item.getFieldName());
>                           // 参数 UTF-8.解决乱码问题
>                           System.out.println("表单项的 value 属性值：" + item.getString("UTF-8"));
>                       }
>                       else {
>                           // 上传的文件
>                           System.out.println("表单项的 name 属性值：" + item.getFieldName());
>                           System.out.println("上传的文件名：" + item.getName());
>                           item.write(new File("C:/Users/Administrator/IdeaProjects/JavaWeb01/" + item.getName()));
>                       }
>                   }
>               } catch (Exception e) {
>                   e.printStackTrace();
>               }
>           }
>       }
>   ```

### 11.7 文件下载

> * 下载常用的api说明 
>
> * `response.getOutputStream();`
>
> * `servletContext.getResourceAsStream();`
>
> * `servletContext.getMimeType();`
>
> * `response.setContentType()`
>
> * `response.setHeader("Content-Disposition", "attachment; fileName=1.jpg");`
>
>   * **这个响应头告诉浏览器。这是需要下载的。而 attachment 表示附件，也就是下载的一个文件。fileName=后面，表示下载的文件名。完成上面的两个步骤，下载文件是没问题了。但是如果我们要下载的文件是中文名的话。你会发现，下载无法正确显示出正确的中文名。原因是在响应头中，不能包含有中文字符，只能包含 ASCII 码。**
>
> * ```java
>   @Override
>       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>   
>           //1.获得要下载的文件名
>           String downloadFileName = "2.jpg";
>           //2.读取要下载的文件内容
>           ServletContext servletContext = getServletContext();
>           //获得要下载的文件类型
>           String mimeType = servletContext.getMimeType("/upload/" + downloadFileName);
>           //3.告诉客户端返回的数据类型是什么
>           resp.setContentType(mimeType);
>           //4.告诉客户端收到的数据是用于下载的,attachment表示附件形式
>           resp.setHeader("Content-Disposition", "attachment; filename=" + downloadFileName);
>           /**
>            * "/"被服务器解析表示地址为: http:port/工程名/   映射到代码的web目录下
>            */
>           InputStream resource = servletContext.getResourceAsStream("/upload/" + downloadFileName);
>           //获取响应的输出流
>           OutputStream outputStream = resp.getOutputStream();
>           //读取输入流中全部的数据，给输出流，回显给客户端
>           IOUtils.copy(resource,outputStream);
>       }
>   ```

#### 11.7.1  解决 IE 和谷歌浏览器的 附件中文乱码

> * ```java
>   @Override
>       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
>   
>           //1.获得要下载的文件名
>           String downloadFileName = "2.jpg";
>           //2.读取要下载的文件内容
>           ServletContext servletContext = getServletContext();
>           //获得要下载的文件类型
>           String mimeType = servletContext.getMimeType("/upload/" + downloadFileName);
>           //3.告诉客户端返回的数据类型是什么
>           resp.setContentType(mimeType);
>           //4.还要告诉客户端收到的数据是用于下载使用（还是使用响应头）
>           // Content-Disposition响应头，表示收到的数据怎么处理
>           // attachment表示附件，表示下载使用
>           // filename= 表示指定下载的文件名
>           // url编码是把汉字转换成为%xx%xx的格式
>           if (req.getHeader("User-Agent").contains("Firefox")) {
>               // 如果是火狐浏览器使用Base64编码
>               resp.setHeader("Content-Disposition", "attachment; filename==?UTF-8?B?" + new BASE64Encoder().encode("中国.jpg".getBytes("UTF-8")) + "?=");
>           } else {
>               // 如果不是火狐，是IE或谷歌，使用URL编码操作
>               resp.setHeader("Content-Disposition", "attachment; filename=" + URLEncoder.encode("中国.jpg", "UTF-8"));
>           }
>           /**
>            * "/"被服务器解析表示地址为: http:port/工程名/   映射到代码的web目录下
>            */
>           InputStream resource = servletContext.getResourceAsStream("/upload/" + downloadFileName);
>           //获取响应的输出流
>           OutputStream outputStream = resp.getOutputStream();
>           //读取输入流中全部的数据，给输出流，回显给客户端
>           IOUtils.copy(resource,outputStream);
>       }
>   ```


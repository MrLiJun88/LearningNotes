# Ajax与Json学习

## 1.Ajax介绍

> Ajax 即“AsynchronousJavascript And XML”（异步 JavaScript 和 XML），是指一种创建交互式[网页](https://baike.baidu.com/item/网页/99347)应用的网页开发技术。
>
> Ajax = 异步 [JavaScript](https://baike.baidu.com/item/JavaScript) 和 XML 或者是 HTML（[标准通用标记语言](https://baike.baidu.com/item/标准通用标记语言/6805073)的子集）。
>
> Ajax 是一种用于创建快速动态网页的技术。
>
> Ajax 是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。
>
> 通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
>
> 传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。#

## 2.实现方式

> **XMLHttpRequest 是 AJAX 的基础。**
>
> 所有现代浏览器均支持 XMLHttpRequest 对象（IE5 和 IE6 使用 ActiveXObject）。
>
> XMLHttpRequest 用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。
>
> 所有现代浏览器（IE7+、Firefox、Chrome、Safari 以及 Opera）均内建 XMLHttpRequest 对象。
>
> 创建XMLHttpRequest对象 (所有现代浏览器（IE7+、Firefox、Chrome、Safari 以及 Opera）均内建 XMLHttpRequest 对象)
>
> ```javascript
> variable=new XMLHttpRequest();
> ```

> 老版本的 Internet Explorer （IE5 和 IE6）使用 ActiveX 对象：
>
> ```javascript
> variable=new ActiveXObject("Microsoft.XMLHTTP");
> ```

> 为了应对所有的现代浏览器，包括 IE5 和 IE6，请检查浏览器是否支持 XMLHttpRequest 对象。如果支持，则创建 XMLHttpRequest 对象。如果不支持，则创建 ActiveXObject ：
>
> ```javascript
> var xmlhttp;
> if (window.XMLHttpRequest)
>   {// code for IE7+, Firefox, Chrome, Opera, Safari
>   xmlhttp=new XMLHttpRequest();
>   }
> else
>   {// code for IE6, IE5
>   xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
>   }
> ```

## 3.向服务器发送请求

> 如需将请求发送到服务器，我们使用 XMLHttpRequest 对象的 open() 和 send() 方法：

```javascript
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();
```

![请求](C:\Users\Administrator\Desktop\ajax&json\images\请求.JPG)

## 4.GET还是POST？

> 与 POST 相比，GET 更简单也更快，并且在大部分情况下都能用。
>
> 然而，在以下情况中，请使用 POST 请求：
>
> - 无法使用缓存文件（更新服务器上的文件或数据库）
> - 向服务器发送大量数据（POST 没有数据量限制）
> - 发送包含未知字符的用户输入时，POST 比 GET 更稳定也更可靠

### 4.1 GET请求

```javascript
xmlhttp.open("GET","demo_get2.asp?fname=Bill&lname=Gates",true);
xmlhttp.send();
```

### 4.2 POST请求

```javascript
xmlhttp.open("POST","demo_post.asp",true);
xmlhttp.send();
```

> 如果需要像 HTML 表单那样 POST 数据，请使用 setRequestHeader() 来添加 HTTP 头。然后在 send() 方法中规定您希望发送的数据：

```javascript
xmlhttp.open("POST","ajax_test.asp",true);
xmlhttp.setRequestHeader("Content-type","application/x-www-form-urlencoded");
xmlhttp.send("fname=Bill&lname=Gates");
```

### 4.3 Async=true

> 当使用 async=true 时，请规定在响应处于 onreadystatechange 事件中的就绪状态时执行的函数：

```javascript
xmlhttp.onreadystatechange=function()
  {
  if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
  }
xmlhttp.open("GET","test1.txt",true);
xmlhttp.send();
```

### 4.3 Async=false

> 我们不推荐使用 async=false，但是对于一些小型的请求，也是可以的。
>
> 请记住，JavaScript 会等到服务器响应就绪才继续执行。如果服务器繁忙或缓慢，应用程序会挂起或停止。
>
> 注释：当您使用 async=false 时，请不要编写 onreadystatechange 函数 - 把代码放到 send() 语句后面即可：

```javascript
xmlhttp.open("GET","test1.txt",false);
xmlhttp.send();
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

## 5.服务器响应

> 如需获得来自服务器的响应，请使用 XMLHttpRequest 对象的 responseText 或 responseXML 属性。

![响应](C:\Users\Administrator\Desktop\ajax&json\images\响应.JPG)

### 5.1 responseText

> 如果来自服务器的响应并非 XML，请使用 responseText 属性。
>
> responseText 属性返回字符串形式的响应，因此您可以这样使用：

```javascript
document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
```

### 5.2 responseXML

> 如果来自服务器的响应是 XML，而且需要作为 XML 对象进行解析，请使用 responseXML 属性：
>
> 请求 [books.xml](https://www.w3school.com.cn/example/xmle/books.xml) 文件，并解析响应：

```javascript
xmlDoc=xmlhttp.responseXML;
txt="";
x=xmlDoc.getElementsByTagName("ARTIST");
for (i=0;i<x.length;i++)
  {
  txt=txt + x[i].childNodes[0].nodeValue + "<br />";
  }
document.getElementById("myDiv").innerHTML=txt;
```

## 6. readyState事件

> 当请求被发送到服务器时，我们需要执行一些基于响应的任务。
>
> 每当 readyState 改变时，就会触发 onreadystatechange 事件。
>
> readyState 属性存有 XMLHttpRequest 的状态信息。
>
> 下面是 XMLHttpRequest 对象的三个重要的属性：

![等待](C:\Users\Administrator\Desktop\ajax&json\images\等待.JPG)

> 在 onreadystatechange 事件中，我们规定当服务器响应已做好被处理的准备时所执行的任务。
>
> 当 readyState 等于 4 且状态为 200 时，表示响应已就绪：

```javascript
xmlhttp.onreadystatechange=function()
  {
  if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
  }
```

### 6.1 使用Callback函数

> callback 函数是一种以参数形式传递给另一个函数的函数。
>
> 如果您的网站上存在多个 AJAX 任务，那么您应该为创建 XMLHttpRequest 对象编写一个*标准*的函数，并为每个 AJAX 任务调用该函数。
>
> 该函数调用应该包含 URL 以及发生 onreadystatechange 事件时执行的任务（每次调用可能不尽相同）：

```html
<html>
<head>
<script type="text/javascript">
var xmlhttp;
function loadXMLDoc(url,cfunc)
{
if (window.XMLHttpRequest)
  {// code for IE7+, Firefox, Chrome, Opera, Safari
  xmlhttp=new XMLHttpRequest();
  }
else
  {// code for IE6, IE5
  xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
  }
xmlhttp.onreadystatechange=cfunc;
xmlhttp.open("GET",url,true);
xmlhttp.send();
}
function myFunction()
{
loadXMLDoc("/ajax/test1.txt",function()
  {
  if (xmlhttp.readyState==4 && xmlhttp.status==200)
    {
    document.getElementById("myDiv").innerHTML=xmlhttp.responseText;
    }
  });
}
</script>
</head>
<body>

<div id="myDiv"><h2>Let AJAX change this text</h2></div>
<button type="button" onclick="myFunction()">通过 AJAX 改变内容</button>

</body>
</html>
```

## 7.使用javascript实现ajax

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    
    <script type="text/javascript">
        function onClick() {
            <!-- 发送异步请求-->
            <!-- 创建核心对象-->
            var xmlhttp;
            if (window.XMLHttpRequest)
            {// code for IE7+, Firefox, Chrome, Opera, Safari
                xmlhttp=new XMLHttpRequest();
            }
            else
            {// code for IE6, IE5
                xmlhttp=new ActiveXObject("Microsoft.XMLHTTP");
            }
            xmlhttp.onreadystatechange=function()
            {
                if (xmlhttp.readyState==4 && xmlhttp.status==200)
                {
				    <!-- 获取服务器响应-->
                    var result =xmlhttp.responseText;
                }
            }
            <!-- 建立连接
             规定请求的类型、URL 以及是否异步处理请求。
             method：请求的类型；GET 或 POST
             url：文件在服务器上的位置
             async：true（异步）或 false（同步）
            -->
            // xmlhttp.open("GET","test1.txt",true);
            xmlhttp.open("GET","www.baidu.com",true);
            <!-- 将请求发送到服务器。
             string：仅用于 POST 请求
             即用GET方式发送请求参数写在open()方法的url中,send()是空参
             用POST方式发送请求参数写在send()方法中
            -->
            xmlhttp.send();
        }
    </script>
</head>
<body>
<input type="button" value="发送ajax请求" onclick="onClick()"/>
<div id="myDiv"></div>
</body>
</html>
```

## 8.实现JQuery实现ajax

### 8.1 $.ajax实现

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    
    <script src="https://code.jquery.com/jquery-3.1.1.min.js">
		function fun(){
        //使用ajax发送异步请求
		$.ajax({
		url:"ajaxServlet",
		type:"POST",
		date:{"username":"lijun","passwd":"123"},
		success:function(data){
		    alert(data);
		   },
        //如果请求响应出现错误，会执行的回调函数
		error:function(){
		    alert("出错了")
		   },
        //设置接受到的响应数据的格式
		dataType:"json"
		});
		}
    </script>
</head>
<body>
<input type="button" value="发送ajax请求" onclick="fun()"/>
<div id="myDiv"></div>
</body>
</html>
```

### 8.2 $.get()实现

> $.get() 方法通过 HTTP GET 请求从服务器上请求数据。
>
> 必需的 *URL* 参数规定您希望请求的 URL。
>
> 可选的 *callback* 参数是请求成功后所执行的函数名。
>
> ```javascript
> $.get(URL,【data】,【callback】,【type】);
> 
> $("button").click(function(){
>   $.get("demo_test.asp",function(data,status){
>     alert("Data: " + data + "\nStatus: " + status);
>   });
> });
> ```

```html
 <script src="https://code.jquery.com/jquery-3.1.1.min.js">
		function fun(){
		$.get("ajaxServlet",{username:"lijun"},function(data){
		   alter(data);
		},"json");
		}
    </script>
```

### 8.3 $.post()实现

```html
 <script src="https://code.jquery.com/jquery-3.1.1.min.js">
		function fun(){
		$.post("ajaxServlet",{username:"lijun"},function(data){
		   alter(data);
		},"json");
		}
    </script>
```

## 9.JSON学习

> **JSON：JavaScript 对象表示法（JavaScript Object Notation）。**
>
> **JSON 是存储和交换文本信息的语法。类似 XML。**
>
> **JSON 比 XML 更小、更快，更易解析。**
>
> 什么是JSON？
>
> - JSON 指的是 JavaScript 对象表示法（*J*ava*S*cript *O*bject *N*otation）
> - JSON 是轻量级的文本数据交换格式
> - JSON 独立于语言 *
> - JSON 具有自我描述性，更易理解
>
> \* JSON 使用 JavaScript 语法来描述数据对象，但是 JSON 仍然独立于语言和平台。JSON 解析器和 JSON 库支持许多不同的编程语言。

### 9.1 实例

```json
//这个 employee 对象是包含 3 个员工记录（对象）的数组。
{
"employees": [
{ "firstName":"Bill" , "lastName":"Gates" },
{ "firstName":"George" , "lastName":"Bush" },
{ "firstName":"Thomas" , "lastName":"Carter" }
]
}
```

### 9.2 JSON语法

> JSON 语法是 JavaScript 对象表示法语法的子集。
>
> - 数据在名称/值对中
> - 数据由逗号分隔
> - 花括号保存对象
> - 方括号保存数组

### 9.3 JSON名称/值对

>JSON 数据的书写格式是：名称/值对。
>
>名称/值对包括字段名称（在双引号中），后面写一个冒号，然后是值：

```json
"firstName" : "John"
```

### 9.4 JSON值

> JSON 值可以是：
>
> - 数字（整数或浮点数）
> - 字符串（在双引号中）
> - 逻辑值（true 或 false）
> - 数组（在方括号中）
> - 对象（在花括号中）
> - null

### 9.5 JSON对象

> JSON 对象在花括号中书写：
>
> 对象可以包含多个名称/值对：

```json
{ "firstName":"John" , "lastName":"Doe" }
```

### 9.6 JSON数组

> JSON 数组在方括号中书写：
>
> 数组可包含多个对象：

```json
//对象 "employees" 是包含三个对象的数组。每个对象代表一条关于某人（有姓和名）的记录。
{
"employees": [
{ "firstName":"John" , "lastName":"Doe" },
{ "firstName":"Anna" , "lastName":"Smith" },
{ "firstName":"Peter" , "lastName":"Jones" }
]
}
```

### 9.7 JSON使用JavaScript语法

> 因为 JSON 使用 JavaScript 语法，所以无需额外的软件就能处理 JavaScript 中的 JSON。
>
> 通过 JavaScript，您可以创建一个对象数组，并像这样进行赋值：

```json
var employees = [
{ "firstName":"Bill" , "lastName":"Gates" },
{ "firstName":"George" , "lastName":"Bush" },
{ "firstName":"Thomas" , "lastName": "Carter" }
];
```

### 9.8 获取JSON数据

* json对象.键名
* json对象["键名"]
* 数组对象[索引]

### 9.9 JSON数据遍历

```html
<script type="text/javascript">
var person = {
"name":"李俊",
"age":25,
"address":"nanjing",
"sex":"man"
};
var ps = [{"name":"李俊","age":25,"address":"nanjing","sex":"man"},
{"name":"wmy","age":22,"address":"xian","sex":"woman"},
{"name":"zhaoliu","age":96,"address":"beijing","sex":"man"}];

//遍历preson对象
for(var key in person){
  alert(key + " : " + person[key]);
}
//遍历ps数组中的对象
for(var i = 0;i < ps.length;i++){
    var p = ps[i];
	for(var key in p){
	alert(key + " : " + p[key]);
	}
}
</script>
```

## 10.JSON数据与Java对象的转换

### 10.1 Java对象转JSON

> JSON解析器：Jsonlib,Gson,fastjson,**jackson**
>
> 在pom.xml文件中配置

```xml
 <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.9</version>
    </dependency>
    
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-core</artifactId>
      <version>2.9.9</version>
    </dependency>
    
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.9</version>
    </dependency>
```

> 转换方法：objectMapper.writeValue(参数1,obj)
>
> ​                    参数1：
>
> ​                                File:将obj对象转换为json字符串，并保存到指定文件中
>
> ​                                Writer:将obj对象转换为json字符串，并写入到字符流中
>
> ​                                 OutputStream:将obj对象转换为json字符串，并写入到字节流中
>
> ​                   objectMapper.writeValueAsString(obj):将对象转为json字符串

```java

      /**创建Jackson核心对象 ObjectMapper*/
       ObjectMapper objectMapper = new ObjectMapper();

       File file = new File("json.txt");
       /**将解析后的json数据保存到指定的文件中*/
       objectMapper.writeValue(file,p1);
       objectMapper.writeValue(new FileWriter(new File("json2.txt")),p3);
```



### 10.2 注解

> * @JsonIgnore: 排除属性，不转换
> * @JsonFormat:属性值的格式化

```java
   @JsonFormat(pattern = "yyyy-MM-dd")
    private Date birthday;
```

### 10.4 集合类型的转换

> java中 list , map 类型的转换

```java
        /**创建Jackson核心对象 ObjectMapper*/
        ObjectMapper objectMapper = new ObjectMapper();
        /**将list集合转换成json数组形式*/
        String result = objectMapper.writeValueAsString(list);
        System.out.println(result);
```

```java
       /**创建Jackson核心对象 ObjectMapper*/
        ObjectMapper objectMapper = new ObjectMapper();
        /**将map转换成json对象形式*/
        String result = objectMapper.writeValueAsString(map);
        System.out.println(result);
```

### 10.5 JSON转Java对象

> objectMapper.readValue()

```java
/**创建Jackson核心对象 ObjectMapper*/
        ObjectMapper objectMapper = new ObjectMapper();
        String str = "{\"username\":\"lijun\",\"age\":24,\"address\":\"nanjing\",\"birthday\":\"2019-10-03\",\"gender\":true}";
        /**将json转换成Java对象*/
        Person person = objectMapper.readValue(str, Person.class);
        System.out.println(person);
```


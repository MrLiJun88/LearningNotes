# JavaScript学习

> * JavaScript 是网景（Netscape）公司开发的一种基于客户端浏览器、~~基于~~**面向对象、事件驱动**式的网页脚本语言。JavaScript语言的前身叫作Livescript。
> * JavaScript是一种解释性编程语言，其源代码在发往客户端执行之前不需经过编译，而是将文本格式的字符代码发送给客户，由浏览器解释执行。
> * JavaScript采用动态联编，即JavaScript的对象引用在运行时进行检查
> * 定义在函数外的变量一定是全局变量；定义在函数内的变量，如果声明了var，那该变量就是局部变量，**如果不声明var，那该变量就是全局变量**。

## 1. js中绑定事件

> * 在js中为某个对象（控件）绑定事件通常可以采取两种手段：
>
>   * ```html
>     <input type=”button” onclick=”clickHandler();”>
>     ```
>
>   * ````html
>     <input type=”button” id=”button1”>
>     
>     <script type=”text/javascript”>
>     var v = document.getElementById(“button1”);
>     v.onclick = clickHandler;
>     </script>
>     ````
>

## 2. Cookie对象

> * 是一种以文件（Cookie文件）的形式保存在客户端硬盘的Cookies文件夹中的用户数据信息（Cookie数据）。Cookie文件由所访问的Web站点建立，以长久的保存客户端与Web站点间的会话数据，并且**该Cookie数据只允许被所访问的Web站点进行读取**
>
> * Cookie文件的格式：
>
>   * NS：Cookie.txt
>   * IE：用户名@域名.txt
>
> * 写入Cookie：
>
>   * 格式：document.cookie = " 关键字 = 值 [ ; expires = 有效日期 ] [;...]"
>
>   * ```javascript
>     document.cookie = "name=lijun;expires=" + expireDay.toGMTString();
>     ```
>
> * 有两种类型的cookie
>
>   1） 持久性cookie，会被存储到客户端的硬盘上。
>
>   **2）** **会话Cookie：不会存储到客户端的硬盘上，而是放在浏览器进程所处的内存当中，当浏览器关闭则该会话cookie就销毁了**

## 3. Function对象

> *  **在JavaScript中，函数（function）就是对象。**
>
>   * ```javascript
>     		//下面的两个方法是等价的
>     		function add(number,number1){
>                 alert(number + 30)
>             }
>     
>             var add = function(number,number1){
>                 alert(number + 90)
>             }
>     ```
>
> * **在JavaScript中，没有方法（函数）重载的概念**，即，若一个方法有三个参数，可以实际给它传递一个参数，并且按顺序赋值。**反之亦然**
>
> * **在JavaScript中有一个Function对象，所有自定义的函数都是Function对象类型的。Function对象接收的所有参数都是字符串类型的，其中最后一个参数就是要执行的函数体，而前面的参数则是函数真正需要接收的参数。**
>
>   * ```javascript
>     		 /**
>              * number : 函数所接受的参数
>              * 最后一个参数是函数所要执行的函数体
>              * 所有的参数类型都是字符串形式
>              * @type {Function}
>              */
>             var add = new Function("number","alert(number + 20);");
>             //调用add
>             add(10);
>     ```
>
> * 在JavaScript中，每个函数都有一个隐含的对象arguments，表示给函数**实际**传递的参数。
>
>   * ```javascript
>     		function add(number1, number2) {
>                  //给函数传递参数的实际个数
>                 alert(arguments.length)//2
>                 alert(arguments[0]);//2
>                 alert(arguments[1]);//3
>             }
>             add(2,3)
>     ```
>
> * 在JavaScript中没有函数重载，但可以通过**实际参数的个数来模拟重载**
>
>   * ```javascript
>     		function add2() {
>                 if(1 == arguments.length){
>                     alert(arguments[0]);
>                 }
>                 else if(2 == arguments.length){
>                     alert(arguments[0] + arguments[1]);
>                 }
>                 else if(3 == arguments.length){
>                     alert(arguments[0] + arguments[1] + arguments[2]);
>                 }
>             }
>             add2(2,2,2);
>     ```
>
> * **每一个函数对象都有一个length属性，表示该函数期望接收的参数格式。它与函数的arguments不同，arguments.length表示函数实际接收的参数格式**
>
>   * ```javascript
>     	var add = function (num,num2,num3) {
>                 alert(num + num2 + num3);
>             }
>             //对象名.length:代表这个函数期望接收的参数个数
>             //arguments.length:代表这个函数实际接收的参数个数
>             alert(add.length);//3
>     ```

## 4. 原始数据类型

> * JavaScript中有五种原始数据类型：**Undefined、Null、Boolean、Number以及String。**
>
> * Undefined数据类型的值只有一个：undefined。
>
> * Null数据类型的值只有一个：null。
>
> * Boolean数据类型的值有两个：true和false。**在JavaScript中除了null和undefined其他都是true**
>
> * JavaScript中没有char数据类型。
>
> * typeof是一元运算符，后跟变量的名称，用于获取变量的数据类型，其返回值有5个：undefined、boolean、number、string以及object。
>
> * **在JavaScript中，如果函数没有声明返回值，那么会返回undefined。**
>
> * null与undefined的关系：undefined实际上是从null派生出来的
>
>   * ```javascript
>     alert(undefined == null);//true
>     ```

## 5. 强制类型转换

> * 在JavaScript中有3种强制类型转换：**Boolean(value)，Number(value)，String(value)**。
>
> * 在JavaScript，所有对象都是从Object对象继承过来的。Object中的属性是不可枚举的(propertyIsEnumerable返回false)，因此无法通过for…in语句得到其中的属性。
>
> * 在JavaScript中，**可以动态添加对象的属性，也可以动态删除对象的属性。**
>
>   * ```javascript
>     		var object = new Object();
>             alert(object.username);//undefined
>             //动态为对象添加属性
>             // object.username = "lijun";
>             object["username"] = "lijun"
>             alert(object.username);//lijun
>             //删除已经定义的属性
>             delete object.username;
>             alert(object.username)//undefined
>     ```
>
> * 

## 6. 常用的定义对象

> * 在JavaScript中定义对象最多的方式
>
>   * ````javascript
>     		var object = {
>                 username:"lijun",
>                 password:"kiss",
>                 address:"南京",
>                 sex:true
>             };
>             alert(object.username);
>             alert(object.password);
>             alert(object.sex);
>     ````

## 7. 数组

> * 数组的定义常用的两种方式
>
>   * ```javascript
>     		var array = new Array();
>             array.push(1);
>             alert(array.length);
>     
>             var array2 = [1,2,3,4];
>             alert(array2.length);
>     ```
>
> * **对于JavaScript数组的sort方法来说，它会先将待排序的内容转换为字符串（调用toString()方法），按照字符串的先后顺序进行排序**。
>
>   * ```javascript
>     		var array2 = [1,3,25,11];
>             array2.sort();//1,11,25,3
>     ```
>
>   * 自定义排序方法对数组进行排序
>
>     ````javascript
>     		//使用匿名函数对数组进行排序
>             array.sort(function (num1,num2) {
>                 var temp1 = parseInt(num1);
>                 var temp2 = parseInt(num2);
>     
>                 if(temp1 < temp2){
>                     return -1;
>                 }
>                 else if(temp1 == temp2){
>                     return 0;
>                 }
>                 else {
>                     return 1;
>                 }
>             });
>     ````

## 8. 定义对象的几种方式

> * JavaScript中定义对象的几种方式（JavaScript中没有类的概念，只有对象）：

### 8.1 基于已有对象扩充其属性和方法

> * ```javascript
>   		var object = new Object();
>           object.name = "lijun";
>           object.sayName = function (name) {
>               this.name = name;
>               alert(this.name);
>           }
>           //调用sayName()方法
>           object.sayName("wangwu");
>   ```

### 8.2 工厂方式

> * ```javascript
>    		//工厂方式
>           function createObject() {
>               var object = new Object();
>               object.username = "hello world";
>               object.password = "lisi";
>               object.get = function () {
>                   alert(this.username + " , " + this.password);
>               }
>               return object;
>           }
>   
>           var object1 = createObject();
>           var object2 = createObject();
>   
>           object1.get();
>           object2.get();
>   ```
>
> * 带参数的构造方法
>
>   * ```javascript
>     		//工厂方式
>             function createObject(username,password) {
>                 var object = new Object();
>                 object.username = username;
>                 object.password = password;
>                 object.get = function () {
>                     alert(this.username + " , " + this.password);
>                 }
>                 return object;
>             }
>     
>             var object1 = createObject("lijun","123");
>             var object2 = createObject("wangwu","456");
>     ```
>
> * **让一个函数对象被多个对象所共享，而不是每一个对象拥有一个函数对象。**
>
>   * ```javascript
>     		//将方法定义在外面
>     		function get() {
>                 alert(this.username + " , " + this.password);
>             }
>     
>             function createObject(username,password) {
>                 var object = new Object();
>                 object.username = username;
>                 object.password = password;
>                 //对象的方法指向外部定义好的方法
>                 object.get = get;
>                 return object;
>             }
>     
>             var object1 = createObject("lijun","123");
>             var object2 = createObject("wangwu","456");
>     ```

### 8.3 构造函数方式 

> * ````javascript
>   		 //构造函数方式
>           function Person() {
>               //使用new的方式生成对象时，在第一行代码前，js引擎会为我们生成一个对象
>               this.username = "lijun";
>               this.password = "123";
>   
>               this.getInfo = function () {
>                   alert(this.username + " : " + this.password);
>               }
>               //此处会隐含一个return，用于将之前生成的对象返回
>           }
>           var person = new Person();
>           person.getInfo();
>   ````
>
> * 可以在构造对象时传递参数
>
>   * ```javascript
>     		//构造函数方式
>             function Person(username,password) {
>                 //使用new的方式生成对象时，在第一行代码前，js引擎会为我们生成一个对象
>                 this.username = username;
>                 this.password = password;
>     
>                 this.getInfo = function () {
>                     alert(this.username + " : " + this.password);
>                 }
>                 //此处会隐含一个return，用于将之前生成的对象返回
>             }
>             var person = new Person("wangwu","kiss");
>             person.getInfo();
>     ```

### 8.4 原型（“prototype”）方式

> * ```javascript
>    		//使用原型方式定义对象,prototype是Object对象中的属性
>           //所以每个对象都会拥有prototype属性
>           function Person() {
>   
>           }
>   
>           Person.prototype.username = "zhangsan";
>           Person.prototype.password = "kiss";
>           Person.prototype.getInfo = function () {
>               alert(this.username + " : " + this.password);
>           }
>   
>           var person1 = new Person();
>           var person2 = new Person();
>           person1.username = "lijun"
>           person1.getInfo();
>           person2.getInfo();
>   ```
>
> * **单纯使用原型方式定义对象无法在构造函数中为属性赋初值，只能在对象生成后再去改变属性值**。
>
> * **如果使用原型方式对象，那么生成的所有对象会共享原型中的属性，这样一个对象改变了该属性也会反应到其他对象当中。但对于js中的五种原始数据类型除外。**
>
> * 使用**原型+构造函数**方式来定义对象，对象之间的属性互不干扰，各个对象间共享同一个方法
>
>   * ```javascript
>      	    /**
>              * 使用原型+构造函数方式来定义对象
>              * 将属性放地构造方法当中
>              * 将方法通过原型去定义
>              */
>             function Person() {
>                 this.username = new Array();
>                 this.password = "123";
>             }
>             Person.prototype.getInfo = function () {
>                 alert(this.username + " : " + this.password);
>             }
>             var person1 = new Person();
>             var person2 = new Person();
>             person1.username.push("zhangsan");
>             person1.username.push("lisi");
>             person1.password = "84399";
>             person1.sex = 996;
>             person1.getInfo();
>     
>             person2.username.push("wangwu");
>             person2.username.push("java");
>             person2.getInfo();
>     ```

### 8.5 动态原型方法

> *  在构造函数中通过标志量让所有对象共享一个方法，而每个对象拥有自己的属性。
>
>   * ```javascript
>     		function Person() {
>                 this.username = "zhangsan";
>                 this.password = "123";
>                 /**
>                  * 通过设置一个标识量，让方法仅仅在第一次调用时创建
>                  */
>                 if(typeof Person.flag == "undefined"){
>                     alert("invoked");
>                     Person.prototype.getInfo = function () {
>                         alert(this.username + " : " + this.password);
>                     }
>                     Person.flag = true;
>                 }
>             }
>     
>             var p1 = new Person();
>             var p2 = new Person();
>             p2.username = "lijun";
>             p2.password = "kiss";
>     
>             p1.getInfo();
>             p2.getInfo();
>     ```

## 9. JavaScript中的继承

### 9.1 对象冒充

> * ```javascript
>   		//继承第一种方式：对象冒充
>           function Parent(username) {
>               this.username = username;
>               this.sayHello = function () {
>                   alert(this.username);
>               }
>           }
>   
>           function Child(username, password) {
>               //这三行代码是最关键的
>               this.method = Parent;
>               this.method(username);
>               delete this.method;
>   
>               this.password = password;
>               this.sayWorld = function () {
>                   alert(this.password);
>               }
>           }
>           var parent = new Parent("zhangsan");
>           var child = new Child("lisi","1234");
>           parent.sayHello();
>           //子类会拥有父类的方法
>           child.sayHello();
>           child.sayWorld();
>   ```

### 9.2 call方法方式

> * **call方法是Function对象中的方法，因此我们定义的每个函数都拥有该方法**。可以通过函数名来调用call方法，**call方法的第一个参数会被传递给函数中的this，从第2个参数开始，逐一赋值给函数中的参数。**
>
>   * ```javascript
>      		 /**
>              * call()方法方式，Function对象自带的方法
>              */
>             function test(str,str2) {
>                 alert(this.name + " : " + str + " : " + str2);
>             }
>     
>             var object = new Object();
>             object.name = "zhangsan";
>             //test.call()相当于调用了test函数
>             //call()中第一个参数永远传递给了this对象，后面的参数会逐一向方向后的参数赋值
>             test.call(object,"hello javascript","hahah");//将object赋给了this
>     ```
>
> * d
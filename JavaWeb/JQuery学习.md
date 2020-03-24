# JQuery学习

## 1. jQuery选择器

> * 基本选择器
>
>   * $(“#id”)，根据id值找到匹配的元素，最多只返回一个元素（如果该元素存在），如果不存在，则返回一个空的jQuery对象。
>   * $(“.class”)，根据css的class属性来返回一个集合，无论该css类是否真的存在，只要定义在元素中就能被jQuery查询到。
>
> * 层次选择器
>
> * 过滤选择器
>
> * 表单选择器
>
>   * ```javascript
>          		//选取class为test的元素下的隐藏子元素
>                 alert($(".test :hidden").length);//5
>                 //选取class为test并且具有隐藏属性的元素
>                 alert($(".test:hidden").length);//3
>     ```

## 2.常用的正则表达式

> * **1.在input框中只能输入金额，其实就是只能输入最多有两位小数的数字**
>
>   * ````javascript
>     //第一种在input输入框限制 
>      <input type="text" maxlength="8" id="amount">元 
>     //第二种对于表单动态增加的方式，只能在js方法中验证。 
>      var amount=$("#amount").val(); 
>      if (amount.search(/^\d*(?:\.\d{0,2})?$/)==-1) { 
>        alert("金额格式不对，最多有两位小数"); 
>        return false; 
>     }
>     ````
>
> * **验证邮箱格式**
>
>   * ```javascript
>     var reg=/\w+([-+.]\w+)*@\w+([-.]\w+)*\.\w+([-.]\w+)*/; 
>      var email=$("#email").val(); 
>      if(!reg.test(email) ){ 
>         alert("请输入符合规范的邮箱账号！"); 
>         return false; 
>        } 
>     ```
>
> * **密码采用数字、字母、特殊字符且长度为8-20位** 
>
>   * ```javascript
>     /^(?=.*[0-9])(?=.*[a-zA-Z])(?=.*[^a-zA-Z0-9]).{8,20}$
>     ```
>
> * **电话号码**
>
>   * ```javascript
>     var reg = /^[1][0-9]{10}$/; 
>     ```
>
> * **判断是否是汉字**
>
>   * ```javascript
>     var reg = /^[\u4e00-\u9fa5]{0,}$/; 
>     ```
>
> * **是否为字母:true:是，false:不是**
>
>   * ```javascript
>     var charPattern=/^[a-zA-Z]*$/; //是否为字母 
>     ```
>
> * **判断是否为数字**
>
>   * ```javascript
>     var numPattern=/^\d*$/; //数字的正则表达式 
>     ```
>
> * **整数的正则表达式**
>
>   * ```javascript
>     var intPattern=/^0$|^[1-9]\d*$/; //整数的正则表达式 
>     ```
>
> * **是否为字母和数字**
>
>   * ```javascript
>     var flagPattern=/^[a-zA-Z0-9]*$/; //是否为字母和数字 
>     ```
>
> * **检验18位身份证号码** 
>
>   * ```javascript
>     /^(^[1-9]\d{7}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])\d{3}$)|(^[1-9]\d{5}[1-9]\d{3}((0\d)|(1[0-2]))(([0|1|2]\d)|3[0-1])((\d{4})|\d{3}[Xx])$)
>     ```
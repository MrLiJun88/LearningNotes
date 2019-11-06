# SpringMVC学习

## 1. Filter与拦截器

### 1.1 Filter

>  依赖于servlet容器。在实现上基于函数回调，可以对几乎所有请求进行过滤，但是缺点是一个过滤器实例只能在容器初始化时调用一次。使用过滤器的目的是用来做一些过滤操作，获取我们想要获取的数据，比如：在过滤器中修改字符编码；在过滤器中修改HttpServletRequest的一些参数，包括：过滤低俗文字、危险字符等 。

### 1.2 拦截器

>  依赖于web框架，在SpringMVC中就是依赖于SpringMVC框架。在实现上基于[Java](http://www.07net01.com/tags-Java-0.html)的反射机制，属于面向切面[编程](http://www.07net01.com/)（AOP）的一种运用。由于拦截器是基于web框架的调用，因此可以使用Spring的依赖注入（DI）进行一些业务操作，同时一个拦截器实例在一个controller生命周期之内可以多次调用。但是缺点是只能对controller请求进行拦截，对其他的一些比如直接访问静态资源的请求则没办法进行拦截处理 。

## 2. SpringMVC 异常处理

### 2.1 异常处理类

> 当Controller层方法处理发生指定异常时，会自动跳转到这个异常处理方法中，执行异常处理方法

```java
/**
 * @ControllerAdvice : 告诉SpringMVC这是一个处理异常类
 */
@ControllerAdvice
public class handlerMyException {

    /**@ExceptionHandler : 告诉SpringMVC这是一个处理异常的方法，并可以指定这个方法可以处理哪些异常
     * 要携带异常信息不能给方法参数写 model
     * 直接返回 ModelAndView 类型即可
     * */
    @ExceptionHandler(value = {ArithmeticException.class,NullPointerException.class})
    public ModelAndView handleMyException(Exception exception){
        System.out.println("方法出现了异常... " + exception);
        ModelAndView mv = new ModelAndView("error");
        /**将错误信息保存，传递给错误页面*/
        mv.addObject("errorInfo",exception);
        return mv;
    }
}
```

### 2.2 自定义异常类

> 

```java
/***
 * 自定义异常类
 */
@ResponseStatus(reason = "用户名错误",code = HttpStatus.NOT_FOUND)
public class MyException extends RuntimeException{

}
```

### 2.3 通过xml配置异常处理

```xml
    <!-- 通过SimpleMappingExceptionResolver，配置异常-->
    <bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
        <property name="exceptionMappings">
            <!--
            key:异常的全类名
            value:要去的错误页面
            -->
            <props>
                <prop key="java.lang.ArithmeticException">error</prop>
            </props>
        </property>
        <!-- exceptionAttribute：绑定的错误信息，可以传递给页面-->
        <property name="exceptionAttribute" value="exceptionAttribute"></property>
    </bean>
```

## 3. SpringMVC 运行流程

> 客户端请求被DispatcherServlet(前端控制器)接收
>
> 1.  DispatcherServlet请求HandlerMapping查询Handler
>
> 2. HandlerMapping根据请求URL查找Handler，将Handler和HandlerInterceptor以HandlerExecutionChain的形式一并返回给DispatcherServlet
>
> 3. DispatcherServlet请求HandlerAdapter执行Handler
>
> 4. HandlerAdapter调用Handler的方法做业务逻辑处理
>
> 5. HandlerAdapter处理完Handler会生成一个ModelAndView对象
>
> 6. 将ModelAndView对象返回给DispatcherServlet
>
> 7. DispatcherServlet将获取的ModelAndView对象传给ViewResolver视图解析器，请求进行视图解析
>
> 8. ViewResolver将逻辑视图解析成物理视图View，返回给DispatcherServlet
>
> 9. DispatcherServlet根据View进行视图渲染(将模型数据填充到视图中)
>
> 10. DispatcherServlet将渲染后的视图响应给客户端

![流程](C:\Users\Administrator\Desktop\笔记\SpringMVC\images\流程.jpg)

### 3.1 简略图

![简略](C:\Users\Administrator\Desktop\笔记\SpringMVC\images\简略.jpg)

## 4. SpringMVC与Spring整合

## 5. 监听器


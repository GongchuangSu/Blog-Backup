title: Servlet 3.0新增注释
layout: post
date: 6/21/2016 9:42:07 PM   
comments: true
type: "categories"
categories: 
- Java Web
tags: 
- servlet
- 注释

---
新增注释是Servlet 3.0中的重大革新之一。通过使用注释就无需在web.xml文件中对Servlet或者过滤器进行配置。

> 新增的注释支持：Servlet 3.0的部署描述文件web.xml的顶层标签 <web-app> 有一个 metadata-complete 属性，该属性指定当前的部署描述文件是否是完全的。
> 如果设置为 true，则容器在部署时将只依赖部署描述文件，忽略所有的注解
> 如果不配置该属性，或者将其设置为 false，则表示启用注解支持

Servlet 3.0新增的注释有`@WebServlet`、`@WebFilter`、`@WebListener`和`@WebInitParam`等。

<!--more-->

# @WebServlet
@WebServlet注释定义在Servlet的类声明之前，用于定义Servlet组件。使用该注释，就无需在web.xml文件中对Servlet进行配置。@WebServlet主要属性如下表所示：

|属性名|类型|描述|
|:--|:--|:--|
|name|String|指定 Servlet 的 name 属性，等价于 <servlet-name>。如果没有显式指定，则该 Servlet 的取值即为类的全限定名|
|value|String[]|该属性等价于 urlPatterns 属性。两个属性不能同时使用|
|urlPatterns|String[]|指定一组 Servlet 的 URL 匹配模式。等价于 <url-pattern> 标签|
|loadOnStartup|int|指定 Servlet 的加载顺序，等价于 <load-on-startup> 标签|
|initParams|WebInitParam[]|指定一组 Servlet 初始化参数，等价于 <init-param> 标签|
|asyncSupported|boolean|声明 Servlet 是否支持异步操作模式，等价于 <async-supported> 标签|
|description|String|该 Servlet 的描述信息，等价于 <description> 标签|
|displayName|String|该 Servlet 的显示名，通常配合工具使用，等价于 <display-name> 标签|

> 注意：以上所有属性均为可选属性，但`value`和`urlPatterns`其中一个必存在，若两个同时存在，一般忽略`value`取值。

例：
**SimpleServlet.java**
```java
@WebServlet(urlPatterns = {"/simple"}, asyncSupported = true, 
loadOnStartup = -1, name = "SimpleServlet", displayName = "ss", 
initParams = {@WebInitParam(name = "username", value = "tom")}) 
public class SimpleServlet extends HttpServlet{ … }
```
其等价于以下的**web.xml**文件配置
```xml
<servlet>
    <display-name>ss</display-name>
    <servlet-name>SimpleServlet</servlet-name>
    <servlet-class>footmark.servlet.SimpleServlet</servlet-class>
    <load-on-startup>-1</load-on-startup>
    <async-supported>true</async-supported>
    <init-param>
        <param-name>username</param-name>
        <param-value>tom</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>SimpleServlet</servlet-name>
    <url-pattern>/simple</url-pattern>
</servlet-mapping>
```

# @WebFilter
@WebFilter注释器用于声明过滤器，该注释将会在部署时被容器处理，容器根据具体的属性配置将相应的类部署为过滤器。

@WebFilter主要属性如下表所示：

|属性名|类型|描述|
|:--|:--|:--|
|filterName|String|指定过滤器的 name 属性，等价于 <filter-name>|
|value|String[]|该属性等价于 urlPatterns 属性。但是两者不应该同时使用|
|urlPatterns|String[]|指定一组过滤器的 URL 匹配模式。等价于 <url-pattern> 标签|
|servletNames|String[]|指定过滤器将应用于哪些 Servlet。取值是 @WebServlet 中的 name 属性的取值，或者是 web.xml 中 <servlet-name> 的取值|
|dispatcherTypes|DispatcherType|指定过滤器的转发模式。具体取值包括：ASYNC、ERROR 、FORWARD、INCLUDE、REQUEST|
|initParams|WebInitParam[]|指定一组过滤器初始化参数，等价于 <init-param> 标签|
|asyncSupported|boolean|声明过滤器是否支持异步操作模式，等价于 <async-supported> 标签|
|description|String|该过滤器的描述信息，等价于 <description> 标签|
|displayName|String|该过滤器的显示名，通常配合工具使用，等价于 <display-name> 标签|

> 注意：以上所有属性均为可选属性，但`value`、`urlPatterns`和`servletNames`三者必须至少包含一个，若`value`和`urlPatterns`两个同时存在，一般忽略`value`取值。

例：
**LessThanSixFilter.java**
```java
@WebFilter(servletNames = {"SimpleServlet"},filterName="SimpleFilter") 
public class LessThanSixFilter implements Filter{...}
```
其等价于以下的**web.xml**文件配置
```xml
<filter> 
    <filter-name>SimpleFilter</filter-name> 
    <filter-class>LessThanSixFilter</filter-class> 
</filter> 
<filter-mapping> 
    <filter-name>SimpleFilter</filter-name> 
    <servlet-name>SimpleServlet</servlet-name> 
</filter-mapping>
```

# @WebListener
@WebListener注释器用于声明监听器，还可以用于充当给定Web应用上下文中各种Web应用事件的监听器的类。被@WebListener标注的类必须实现以下至少一个接口：

- ServletContextListener
- ServletContextAttributeListener
- ServletRequestListener
- ServletRequestAttributeListener
- HttpSessionListener
- HttpSessionAttributeListener

@WebListener的常用属性

|属性名|类型|是否可选|描述|
|:--|:--|:--|:--|
|value|String|是|该监听器的描述信息|

例：
**SimpleListener.java**
```java
@WebListener("This is only a demo listener") 
public class SimpleListener implements ServletContextListener{...}
```
其等价于以下的**web.xml**文件配置
```xml
<listener> 
    <listener-class>footmark.servlet.SimpleListener</listener-class> 
</listener>
```

# @WebInitParam
@WebInitParam注释等价于web.xml文件中的<servlet>和<filter>的<init-parm>子标签，该注释通常不单独使用，而是配合@WebServlet和@WebFilter使用。其作用是为Servlet或者过滤器指定初始化参数。
@WebInitParam主要属性如下表所示：

|属性名|类型|是否可选|描述|
|:--|:--|:--|:--|
|name|String|否|指定参数的名字，等价于 <param-name>|
|value|String|否|指定参数的值，等价于 <param-value>|
|description|String|是|关于参数的描述，等价于 <description>|

# 参考资料
1. [Java Web从入门到精通](http://item.jd.com/11078111.html)
2. [Servlet 3.0 新特性详解](https://www.ibm.com/developerworks/cn/java/j-lo-servlet30/)

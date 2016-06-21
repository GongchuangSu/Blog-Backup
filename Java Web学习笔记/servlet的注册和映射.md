title: servlet的注册和映射
layout: post
date: 6/14/2016 5:22:15 PM   
comments: true
type: "categories"
categories: 
- Java Web
tags: 
- servlet
- 注册
- 映射

---

> Servlet程序需要在web.xml中注册和映射，才可以被Servlet引擎(容器)加载和被外界访问。

# Servlet注册
**xml代码:**
```xml
<web-app>  
  ...  
   <servlet>
      <servlet-name>HelloWeb</servlet-name>
      <servlet-class>
         org.springframework.web.servlet.DispatcherServlet
      </servlet-class>
      <load-on-startup>1</load-on-startup>
   </servlet>  
  ...  
</web-app>
```

<!--more-->

在 web.xml 文件中，servlet 元素及其子元素用来注册 Servlet。

|元素|功能|说明|
|:--|:--|:--|
|servlet-name|指定Servlet的名称，在同一个Web应用程序中，其名称必须是唯一的|必须包含|
|servlet-class|指定Servlet类的完整限定名(包含包名)|必须包含(servlet-class或jsp-file)|
|jsp-file|指定在Web应用程序中的JSP文件的完整路径，该路径以斜杠(/)开始。|必须包含(servlet-class或jsp-file)|
|init-param|定义Servlet的初始化参数。如果使用了init-param元素，则必须包含param-name和param-value元素，可以包含零个或多个description元素|可以有零个或多个|
|load-on-startup|指定当Web应用程序启动时，Servlet被加载的次序，先加载数值小的Servlet，再加载数值大的Servlet|可以有零个或一个|
|description|为Servlet指定一个文本描述|可以有零个或多个|

# Servlet映射
**xml代码:**
```xml
<web-app>  
  ...  
   <servlet-mapping>
      <servlet-name>HelloWeb</servlet-name>
      <url-pattern>/</url-pattern>
   </servlet-mapping>
  ...  
</web-app>
```
在 web.xml 文件中，servlet-mapping 元素用来在Servlet和URL样式之间定义一个映射。它包含两个元素servlet-name和url-pattern。
- servlet-name：Servlet名称，其必须是在servlet元素中声明过的Servlet名称
- url-pattern：指定对应于Servlet的URL路径

> 注意：在Servlet2.5规范中，允许servlet-mapping元素中的url-pattern子元素出现多次，之前的规范只允许一个servlet-mapping元素包含一个url-pattern子元素。

## 如何映射请求到Servlet？
在配置了Servlet与URL样式之间的映射之后，当Servlet容器接收到一个请求时，它首先确定该请求应该由哪一个Web应用程序来响应。这是通过比较请求URL的开始部分与Web应用程序的上下文路径来确定的。映射到Servlet的路径是请求URL路径减去上下文路径。下面的URL路径映射规则按顺序使用找到第一个匹配后，将不会进一步尝试匹配。
- 容器尝试对请求的路径(减去上下文路径后的路径)和Servlet映射的路径(url-pattern参数所指定的路径样式)进行精确匹配，如果匹配成功，则调用这个Servlet来处理请求
- 容器试着匹配最长的路径前缀，以斜杠(/)为路径分隔符，按照路径树逐级递减匹配，选择最长匹配的Servlet来处理请求
- 如果请求的URL路径最后有扩展名，如.jsp，Servlet容器将会试着匹配处理这个扩展名的Servlet
- 如果按照前面三条规则没有找到匹配的Servlet，容器将会调用Web应用程序默认的Servlet来对请求进行处理；如果没有定义默认的Servlet，容器将会向客户端发送HTTP 404错误信息（请求资源不存在）

## 映射规范
在web应用部署中描述符中，以下语法用于定义映射：
- 以‘/’字符开始，以‘/*’后缀结尾的字符串用于路径匹配
- 以‘*.’开始的字符串用于扩展名映射
- 单独的‘/’字符，用于默认的Servlet
- 所有其他的字符被用于**精确匹配**，如‘/login’

下面列出了几个请求映射的例子：
**Servlet映射**

|URL形式|对应的Servlet|
|:--|:--|
|/foo/bar/*|servlet1|
|/baz/*|servlet2|
|/catalog|servlet3|
|*.bop|servlet4|

**实际请求映射的结果**

|去掉上下文路径后的剩余路径|对应的Servlet|
|:--|:--|
|/foo/bar/index.html|servlet1|
|/foo/bar/index.bop|servlet1|
|/baz|servlet2|
|/baz/index.html|servlet2|
|/catalog|servlet3|
|/catalog/index.html|默认的servlet|
|/catalog/racecar.bop|servlet4|
|/index.bop|servlet4|

> 注意：在/catalog/index.html和/catalog/racecar.bop的情况下，由于不是精确匹配，不使用映射到“/catalog”的servlet。

# 参考资料
1. [Java Servlet Specification(Version 3.0)](http://download.oracle.com/otn-pub/jcp/servlet-3.0-fr-eval-oth-JSpec/servlet-3_0-final-spec.pdf?AuthParam=1465962194_b465e14b8f728b101b62101288a7e652)
2. [Servlet JSP深入详解：基于Tomcat的Web开发](http://item.jd.com/10066883.html)
3. [O'Reilly：Head First Servlets and JSP（第2版）](http://item.jd.com/10480831.html)
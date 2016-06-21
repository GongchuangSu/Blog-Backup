title: JSP中的四种范围属性
layout: post
date: 6/21/2016 9:39:15 AM    
comments: true
type: "categories"
categories: 
- Java Web
tags: 
- JSP
- page
- request
- session
- application

---
JSP中的对象，包括用户创建的对象(如JavaBean对象)和JSP的隐含对象，都有一个范围属性。范围属性规定了这些对象的作用域，定义了在什么时间内，在哪一个JSP页面中可以被访问。在JSP中，有四种范围属性：
- page：在一个jsp页面内有效，跳转后无效
- request：在一次服务请求范围内有效，可以跨越forward前后两页
- session：在一次会话范围内有效，即从浏览器打开到浏览器关闭这个过程有效
- application：在整个Web应用程序运行期间有效

下面进行详细介绍。

<!--more-->

# page范围
具有page范围的对象被绑定到javax.servlet.jsp.PageContext对象中。在这个范围内的对象，只能在创建对象的页面中访问。

- 可以调用pageContext对象的getAttribute()方法来访问具有这种范围类型的对象(或通过EL表达式`${pageScope.XXX}`来访问)
- 可以调用pageContext对象的setAttribute()方法将对象保存到page范围中

page范围内的对象，在客户端每次请求jsp页面时被创建，在页面向客户端发送响应或请求被转发(forward)到其他资源后被删除。
例1：
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>Insert title here</title>  
</head>  
<body>  
<%  
pageContext.setAttribute("scope", "page");    
%>  
Scope：${pageScope.scope} 
</body>  
</html> 
```
输出结果：
```html
Scope：page
```

例2：
**main.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>Insert title here</title>  
</head>  
<body>  
<%  
pageContext.setAttribute("scope", "page");    
%>  
<jsp:forward page="test.jsp"/> 
</body>  
</html> 
```
**test.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${pageScope.scope} 
</body>  
</html> 
```
输出结果：
```html
Scope：
```
对比例1和例2可以发现，page范围内的对象只能在其创建的页面内访问，其它页面内无效。

# request范围
具有request范围的对象被绑定到javax.servlet.http.HttpServletRequest对象中，其在一次服务请求范围内有效，可以跨越forward前后两页。

- 可以调用request对象的getAttribute()方法来访问具有这种范围类型的对象(或通过EL表达式`${requestScope.XXX}`来访问)
- 可以调用request对象的setAttribute()方法将对象保存到request范围中 
- 在调用forward()方法转向的页面或调用include()方法包含的页面中，都可以访问这个范围内的对象
- 通过跳转进入的页面不能访问这个范围内的对象

例1：
**main.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>Insert title here</title>  
</head>  
<body>  
<%  
request.setAttribute("scope", "request");    
%>  
<jsp:forward page="test.jsp"/> 
</body>  
</html>  
```
**test.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${requestScope.scope} 
</body>  
</html>  
```
输出结果：
```html
Scope：request
```

例2：
**main.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>Insert title here</title>  
</head>  
<body>  
<%  
request.setAttribute("scope", "request");    
%>  
<a href="test.jsp">获取request内容</a>
</body>  
</html>  
```
**test.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${requestScope.scope} 
</body>  
</html>  
```
输出结果：
```html
Scope：
```
由例2可知，通过跳转进入的页面，不能访问原页面中request范围内的对象。

# session范围
具有session范围的对象被绑定到javax.servlet.http.HttpSession对象中，不管跳转至哪个页面，都可以取得该对象。

- 可以调用session对象的getAttribute()方法来访问具有这种范围类型的对象(或通过EL表达式`${sessionScope.XXX}`来访问)
- 可以调用session对象的setAttribute()方法将对象保存到session范围中 
- JSP容器为每一次会话创建一个HttpSession对象，在会话期间，可以访问session范围内的对象

例：
**main.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>Insert title here</title>  
</head>  
<body>  
<%  
session.setAttribute("scope", "session");    
%>  
<a href="test.jsp">跳转至test1：获取session内容</a> <br>
<a href="test1.jsp">跳转至test2：获取session内容</a>
</body>  
</html> 
```
**test.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${sessionScope.scope}
</body>  
</html>  
```
**test1.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${sessionScope.scope} 
</body>  
</html>   
```
输出结果：不管点击[跳转至test1：获取session内容](http://localhost:8080/test.jsp) 还是点击[跳转至test2：获取session内容](http://localhost:8080/test1.jsp)，都会得到同一个结果
```html
Scope：session
```
说明这两个jsp页面都可以取得main.jsp页面的session对象，而且取得的值是一样的。

> 会话(session)：所谓当前会话，就是指从用户打开浏览器开始，到用户关闭浏览器这中间的过程。只要用户不关闭浏览器，放在会话中的变量就可以被当前会话中的所有请求使用。

# application范围
具有application范围的对象被绑定在javax.servlet.ServletContext中，在Web应用程序运行期间，所有的页面都可以访问在这个范围内的对象。

- 可以调用application对象的getAttribute()方法来访问具有这种范围类型的对象(或通过EL表达式`${applicationScope.XXX}`来访问)
- 可以调用application对象的setAttribute()方法将对象保存到application范围中 
- 要释放application资源，只能重新启动服务器

例：
**main.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>Insert title here</title>  
</head>  
<body>  
<%  
application.setAttribute("scope", "application");    
%>  
<a href="test.jsp">跳转至test1：获取application内容</a> <br>
<a href="test1.jsp">跳转至test2：获取application内容</a>
</body>  
</html> 
```
**test.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${applicationScope.scope}
</body>  
</html>   
```
**test1.jsp**
```html
<%@page language="java" contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>   
<html>  
<head>  
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">  
<title>中转页</title>  
</head>  
<body>    
Scope：${applicationScope.scope} 
</body>  
</html>    
```
输出结果：运行main.jsp后，不管浏览器是否关闭过，其他所有页面都可以再次访问application范围内的对象，因为这些值是保存在服务器上的。
```html
Scope：session
```

# 参考资料
1. [《Servlet JSP深入详解 基于Tomcat的Web开发》](http://item.jd.com/10066883.html)
2. [JSP九大内置对象和四种属性范围解读](http://blog.csdn.net/evankaka/article/details/46877371)
title: JSP中的九大隐含对象
layout: post
date: 6/21/2016 2:09:27 PM   
comments: true
type: "categories"
categories: 
- Java Web
tags: 
- JSP
- 隐含对象
- 内置对象

---
> JSP隐含对象（又称内置对象），是指可以不加声明和创建就可以在JSP页面脚本（Java程序片和Java表达式）中使用的成员变量。

在JSP中一共预先定义了九个隐含对象，分别为request, response, pageContext, session, application, out, config, page和exception。
JSP隐含对象的类型如下表所示：

|隐含对象|类型|作用域|
|:--|:--|:--|
|request|javax.servlet.http.HttpServletRequest|request|
|response|javax.servlet.http.HttpServletResponse|page|
|pageContext|javax.servlet.jsp.PageContext|page|
|session|javax.servlet.http.HttpSession|session|
|application|javax.servlet.ServletContext|application|
|out|javax.servlet.jsp.JspWriter|page|
|config|javax.servlet.ServletConfig|page|
|page|java.lang.Object|page|
|exception|java.lang.Throwable|page|

<!--more-->

# request
request对象代表的是javax.servlet.http.HttpServletRequest类的对象，它封装了由客户端生成的HTTP请求的所有细节，主要包括HTTP头信息、系统信息、请求方式和请求参数等。通过request对象提供的相应方法可以处理客户端浏览器提交的HTTP请求中的各项参数。
request获取客户端信息的常用方法如下表所示：

|方法|功能|
|:--|:--|
|getHeader(String name)|获得HTTP协议定义的文件头信息|
|getHeaders(String name)|返回指定名字的request Header的所有值，其结果是一个枚举型的实例|
|getHeadersNames()|返回所有request Header的名字，其结果是一个枚举型的实例|
|getMethod()|获得客户端向服务器端传送数据的方法，如get、post、header、trace等|
|getProtocol()|获得客户端向服务器端传送数据所依据的协议名称|
|getRequestURI()|获得发出请求字符串的客户端地址，不包括请求的参数|
|getRequestURL()|获得发出请求字符串的客户端地址|
|getRealPath()|返回当前请求文件的绝对路径|
|getRemoteAddr()|获取客户端的IP地址|
|getRemoteHost()|获取客户端的主机名|
|getServerName()|获取服务器的名字|
|getServerPath()|获取客户端所请求的脚本文件的文件路径|
|getServerPort()|获取服务器的端口号|
|getParameter()|获取客户端通过表单提交过来的参数值|
|getScheme()|获取请求使用的模式（协议）名字|

例：
**request.jsp**
```html
<%@ page contentType="text/html;charset=UTF-8" %>
<html>
<head><title> request内置对象的实例 </title></head>
<body>
<form aciton="request.jsp">
Get request results:
<input type="text" name="myname">
<input type="submit" name="get value">
</form>
获取客户提交信息的方式:<%=request.getMethod()%> <br>
获取请求信息中的协议名名字和版本号:<%=request.getProtocol()%> <br>
获取发出请求字符串的客户端地址:<%=request.getRequestURI()%> <br>
获取发出请求字符串的客户端地址:<%=request.getRequestURL()%> <br>
获取提交请求的客户端的IP地址:<%=request.getRemoteAddr()%> <br>
获取服务器的端口号:<%=request.getServerPort()%> <br>
获取接受请求的服务器名称:<%=request.getServerName()%> <br>
获取客户端所请求的脚本文件的文件路径:<%=request.getServletPath()%> <br>
获取Http协议定义的文件头信息Host的值:<%=request.getHeader("host")%> <br>
获取Http协议定义的文件头信息User-Agent的值:<%=request.getHeader("user-agent")%> <br>
获取Http协议定义的文件头信息accept-language的值:<%=request.getHeader("accept-language")%> <br>
获取请求文件的绝对路径:<%=request.getRealPath("request.jsp")%> <br> 
获取客户端通过表单提交过来的参数值:<%=request.getParameter("myname")%> <br>
获取客户端的主机名:<%=request.getRemoteHost()%> <br>
获取请求中使用的模式（协议）名字:<%=request.getScheme()%> <br>
</body>
</html>
```
运行结果：
```html
获取客户提交信息的方式:GET 
获取请求信息中的协议名名字和版本号:HTTP/1.1 
获取发出请求字符串的客户端地址:/request.jsp 
获取发出请求字符串的客户端地址:http://localhost:8080/request.jsp 
获取提交请求的客户端的IP地址:127.0.0.1 
获取服务器的端口号:8080 
获取接受请求的服务器名称:localhost 
获取客户端所请求的脚本文件的文件路径:/request.jsp 
获取Http协议定义的文件头信息Host的值:localhost:8080 
获取Http协议定义的文件头信息User-Agent的值:Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/50.0.2661.102 Safari/537.36 
获取Http协议定义的文件头信息accept-language的值:zh-CN,zh;q=0.8,en;q=0.6 
获取请求文件的绝对路径:D:\***\我的文档\Tomcat\apache-tomcat-8.0.35\webapps\ROOT\request.jsp 
获取客户端通过表单提交过来的参数值:request 
获取客户端的主机名:127.0.0.1 
获取请求中使用的模式（协议）名字:http
```

# response
response对象用于响应客户请求，向客户端输出信息。它封装了JSP产生的响应，并发送到客户端以响应客户端的请求。
response对象提供了多个方法用来处理HTTP响应，比较常用的方法如下：

|方法|功能|
|:--|:--|
|setContentLength(int len)|设置响应头的长度|
|setContentType(String type)|设置HTTP响应的contentType中的MIME类型|
|getOutputStream()|返回一个Servlet的输出流|
|getWriter()|返回一个PrintWriter对象|
|getCharacterEncoding()|获得此时响应所采用的字符编码类型|
|sendRedirect(String location)|将请求重新定位到一个不同的URL（页面）上|
|setHeader(String headername, String value)|使用指定的头名字以及相应的值来设置头信息|
|addheader(String headername, String value)|把指定的头名字以及相应值添加到头信息当中去|

# session
session在网络中被称为会话，它是javax.servlet.http.HttpSession.session类的对象。由于HTTP协议是一种无状态协议，也就是当一个客户端向服务器发出请求，服务器接收请求，并返回响应后，该连接就结束了，而服务器是不保存相关的信息。为了弥补这一缺点，HTTP协议提供了session。通过session可以在应用程序的Web页面间进行跳转时，保存用户的状态，使整个用户会话一直存在下去，直到关闭浏览器。

> 如果在一个会话中，客户端长时间不向服务器发出请求，session对象就会自动消失。这取决于服务器，Tomcat服务器默认为30分钟。

session对象的常用方法如下表所示：

|方法|功能|
|:--|:--|
|getId()|返回唯一的标识，这些标识为每个session而产生|
|getCreationTime()|返回session被创建的时间，最小单位为千分之一秒|
|getLastAccessedTime()|返回客户端最后一次与会话相关联的请求时间|
|getMaxInactiveInterval()|以秒为单位返回会话内两个请求最大时间间隔|
|setMaxInactiveInterval()|以秒为单位设置session的有效时间|
|getAttribute(String key)|通过给定的关键字获取一个存储在session中相对应的信息|
|setAttribute(String key, Object obj)|提供一个关键词和一个对象值，然后存在session当中|
|removeAttribute(String name)|从session会话中移除指定的绑定对象|
|invalidate()|销毁session|

> 注意：session中保存和查找的信息不能是基本数据类型，如int、double等，而必须是java对象，如Integer、Double等。

具体例子可参考[JSP中的四种范围属性](http://gongchuangsu.com/2016/06/21/Java%20Web%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/JSP%E4%B8%AD%E7%9A%84%E5%9B%9B%E7%A7%8D%E8%8C%83%E5%9B%B4%E5%B1%9E%E6%80%A7/)。

# application
application是javax.servlet.ServletContext类的对象。该对象用于保存所有应用程序中的公有数据。它在服务器启动时自动创建，在服务器停止时销毁。当application对象没有被销毁时，所有用户都可以共享该application对象。

application对象的常用方法如下表所示：

|方法|功能|
|:--|:--|
|getInitParameter(String name)|返回已命名的应用程序初始化参数值|
|getAttributeNames()|返回所有已定义的应用程序初始化参数名的枚举|
|getAttribute(String name)|从application对象中获取指定对象名|
|setAttribute(String name, Object obj)|保存一个对象信息，并指定给一个名称|
|removeAttribute(String name)|从application对象中去掉指定名称的属性|
|getServletInfo()|返回JSP引擎的相关信息|
|getRealPath(String path)|返回虚拟路径的真实路径|
|getContext(String URLPath)|返回执行Web应用的application对象|
|getMajorVersion()|返回服务器所支持的Servlet API最大版本号|
|getMinorVersion()|返回服务器所支持的Servlet API最小版本号|
|getMineType(String file)|返回指定文件的MIME类型|
|getResource(String path)|返回指定资源的URL路径|
|getResourceAsStream(String path)|返回指定资源的输入流|
|getRequestDispatcher(String URLPath)|返回指定资源的RequestDispatcher对象|
|getServlet(String name)|返回指定名称的Servlet|
|getServlets()|返回所有的Servlet，返回类型为枚举型|
|getServletNames()|返回所有的Servlet名称，返回类型为枚举型|
|log(String msg)|把指定信息写入到Servlet的日志文件中|
|log(String msg, Throwable throwable)|把栈轨迹以及给出的Throwable异常的说明信息写入Servlet的日志文件|

具体例子可参考[JSP中的四种范围属性](http://gongchuangsu.com/2016/06/21/Java%20Web%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0/JSP%E4%B8%AD%E7%9A%84%E5%9B%9B%E7%A7%8D%E8%8C%83%E5%9B%B4%E5%B1%9E%E6%80%A7/)。

# out
out对象用于在Web浏览器内输出信息，并且管理应用服务器上的输出缓冲区。out对象可以输出各种数据类型的数据，在输出非字符串类型的数据时，会自动转换为字符串进行输出。

out对象的常用方法如下表所示：

|方法|功能|
|:--|:--|
|print()|在页面中打印出字符串信息，不换行|
|println()|在页面中打印出字符串信息，并且换行|
|clear()|清除缓冲区中的内容|
|clearBuffer()|清除当前缓冲区中的内容|
|flush()|刷新流|
|getBufferSize()|获取缓冲区大小|
|isAutoFlush()|检测当前缓冲区已满时是自动清空还是抛出异常|
|close()|关闭输出流|

# config
config对象主要用于取得服务器的配置信息。当一个Servlet初始化时，容器把某些信息通过config对象传递给这个Servlet。

config对象的常用方法如下表所示：

|方法|功能|
|:--|:--|
|getServletContext()|获取Servlet上下文|
|getInitParameter(String name)|返回初始化参数的值|
|getInitParameterNames()|返回包含了Servlet初始化所需要的所有参数，返回类型是枚举型|
|getServletName()|获取Servlet服务器名|

# page
page对象代表JSP本身，只有在JSP页面内才是合法的。page对象本质上是包含当前Servlet接口引用的变量，可以看作是this关键字的别名。

page对象的常用方法如下表所示：

|方法|功能|
|:--|:--|
|getClass()|返回当前Object的类|
|hashCode()|返回此Object的哈希代码|
|toString()|把当前Object类转换成字符串|
|equals(Object o)|比较该对象和指定对象是否相等|
|copy(Object o)|把该对象复制到指定的对象当中去|
|clone()|对该对象进行克隆|

# exception 
exception对象用来处理JSP文件执行时发生的所有错误和异常，只有在page指令中设置为isErrorPage属性值为true的页面中才可以被使用，在一般的JSP页面中使用该对象将无法编译JSP文件。

exception对象的常用方法如下表所示：

|方法|功能|
|:--|:--|
|getMessage()|返回exception对象的异常信息字符串|
|getLocalizedMessage()|返回本地化的异常错误信息|
|toString()|返回关于异常错误的简单信息描述|
|fillInStackTrace()|重写异常错误的栈执行轨迹|

# pageContext
pageContext对象是一个比较特殊的对象。它相当于页面中所有其他对象功能的最大集成者，即使用它可以访问到本页面中所有其他对象，例如request、response以及application等。
pageContext对象常用方法如下表所示：

|方法|功能|
|:--|:--|
|getRequest()|获取当前页面中的request对象|
|getResponse()|获取当前页面中的response对象|
|getPage()|获取当前页面中的page对象|
|getSession()|获取当前页面中的session对象|
|getOut()|获取当前页面中的out对象|
|getException()|获取当前页面中的exception对象|
|getServletConfig()|获取当前页面中的config对象|
|getServletContext()|获取当前页面中的application对象|
|setAttribute(String name)|给指定的属性名设置属性值|
|getAttribute(String name)|根据属性名找到相应的属性值|
|setAttribute(String name, Object obj, int scope)|在给定的范围内设置相应的属性值|
|getAttribute(String name, int scope)|在指定的范围内获取到相应的属性值|
|findAttribute(String name)|寻找一个属性并返回，如果没有找到则返回一个null|
|removeAttribute(String name)|通过属性名删除掉某个属性|
|removeAttribute(String name, int scope)|在指定的某个范围内删除某个属性|
|getAttributeScope(String scope)|返回某属性的作用域|
|getAttributeNamesInScope(int scope)|返回指定范围内的所有属性名的枚举|
|release()|释放掉pageContext()所占的所有资源|
|forward(String relativeURLpath)|使用当前页面重导到另一个页面|
|include(String relativeURLpath)|使用当前位置包含的另一个页面|

> pageContext对象在实际JSP开发中一般很少使用，因为request和response等对象均为内置对象，如果通过pageContext来调用这些对象比较麻烦，都可以直接调用相关方法实现具体的功能。

# 参考资料
1. [Java Web从入门到精通](http://item.jd.com/11078111.html)
2. [《Servlet JSP深入详解 基于Tomcat的Web开发》](http://item.jd.com/10066883.html)
3. [JSP九大内置对象](http://www.importnew.com/19128.html)
title: SpEL运算符
layout: post
date: 6/22/2016 5:32:06 PM    
comments: true
type: "categories"
categories: 
- Spring
tags: 
- SpEL运算符

---

![](http://i.imgur.com/UlRR8cf.jpg)

<!--more-->

SpEL提供了几种运算符，这些运算符可以用在SpEL表达式中的值上。

|运算符类型|运算符|
|:--|:--|
|算术运算|+、-、*、/、%、^|
|关系运算|<、>、==、<=、>=、lt、gt、eq、le、ge|
|逻辑运算|and、or、not、&#124;|
|条件运算|?:(ternary)、?:(Elvis)|
|正则表达式|matches|

----------
**数值运算**
```xml
<!-- +运算符：两个数字相加 -->
<property name="adjustedAmount" value="#{counter.total + 42}"/>
<!-- +运算符：用于连接字符串 -->
<property name="fullName" value="#{performer.firstName + ' ' + performer.lastName}"/>
<!-- -运算符：两个数字相减 -->
<property name="adjustedAmount" value="#{counter.total - 20}"/>
<!-- *运算符：乘法运算 -->
<property name="circumference" value="#{2 * T(java.lang.Math).PI * circle.radius}"/>
<!-- /运算符：除法运算 -->
<property name="average" value="#{counter.total / counter.count}"/>
<!-- %运算符：求余运算 -->
<property name="remainder" value="#{counter.total % counter.count}"/>
<!-- ^运算符：乘方运算 -->
<property name="area" value="#{T(java.lang.Math).PI * circle.radius ^ 2}"/>
```
> 注意：+运算符可以执行字符串连接。

----------
**比较值**
比较两个值是否相等，可以使用"=="运算符：
```xml
<!-- 假设equal属性为布尔属性 -->
<property name="equal" value="#{counter.total == 100}"/>
```
类似的，其他的关系运算符可以用于比较不同的值。
特别注意：由于小于等于和大于等于这两个符号在XML中有特殊意义，所以在Spring的XML配置文件中使用这两个符号时，会报错。当在XML中使用SpEL时，最好对这些运算符使用SpEL的文本替代方法。

|运算符|符号|文本类型|
|:--:|:--:|:--:|
|等于|==|eq|
|小于|<|lt|
|小于等于|<=|le|
|大于|>|gt|
|大于等于|>=|ge|

----------
**逻辑表达式**
```xml
<!-- and 运算符 -->
<property name="largeCircle" value="#{shape.kind == 'circle' and shape.perimeter gt 10000}"/>
<!-- ! 运算符 -->
<property name="outOfStock" value="#{!product.availiable}"/>
<!-- not 运算符 -->
<property name="outOfStock" value="#{not product.availiable}"/>
```
|运算符|操作|
|:--:|:--|
|and|逻辑AND运算操作，只有运算符两边都是true，表达式才能是true|
|or|逻辑OR运算操作，只要运算符的任意一边是true，表达式就会是true|
|not或!|逻辑NOT运算操作，对运算结果求反|

----------
**条件表达式**
```xml
<!-- ?:三元运算符 -->
<property name="song" value="#{kenny.song != null ? kenny.song : 'Greensleeves'}"/>
```
如果kenny.song值为空，则赋值kenny.song ，否则赋值'Greensleeves'。这里'Greensleeves'的引用重复两次，可简化表达式如下：
```xml
<!-- ?:三元运算符 -->
<property name="song" value="#{kenny.song != null ? 'Greensleeves'}"/>
```
当以这种方式使用时，“?:”通常被称为elvis运算符，而第一种方式则称为ternary运算符。

----------
**SpEL的正则表达式**
SpEL通过matches运算符来支持表达式中的模式匹配。
```xml
<!-- 判断一个字符串是否是有效的邮件地址 -->
<property name="validEmail" value="#{admin.email matches '[a-zA-Z0-9.-%+-]+@[a-zA-Z0-9.-]+\\.com'}"/>
```
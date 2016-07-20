title: 使用SpEL表达式装配Bean
layout: post
date: 6/22/2016 6:42:06 PM    
comments: true
type: "categories"
categories: 
- Spring
tags: 
- SpEL表达式

---

![](http://i.imgur.com/5OykS70.jpg)

<!--more-->

**字面值：**
在`<property>`元素的`value`属性中使用`#{}`界定符把这个值装配到Bean的属性中
```xml
<property name="count" value="#{5}"/>
...
<!--浮点型数字-->
<property name="frequence" value="#{89.7}"/>
<!--科学计数法-->
<property name="capacity" value="#{1e4}"/>
<!--布尔型-->
<property name="enable" value="#{false}"/>
```
`#{}`标记会提示Spring这个标记里的内容是SpEL表达式，它们还可以与非SpEL表达式的值混用：
```xml
<property name="count" value="The value is #{5}"/>
```
String类型的字面值可以使用单引号或双引号作为字符串的界定符：
```xml
<property name="name" value="#{'Chuck'}"/>
<!--或-->
<property name="name" value='#{"Chuck"}'/>
```
----------
**引用Bean、Properties和方法**
在SpEL表达式中使用Bean ID将一个Bean装配到另一个Bean的属性中：
```xml
<property name="instrument" value="#{saxophone}"/>
<!--其等价于-->
<property name="instrument" ref="saxophone"/>
```
在SpEL表达式中使用Bean的引用来获取Bean的属性：
```xml
<bean id="carl"
      class="com.springinaction.springidol.Instrumentalist">
	<property name="song" vaule="#{kenny.song}"/>
</bean>
```
其等价于以下代码
```java
Instrumentalist carl = new Instrumentalist();
carl.setSong(kenny.getSong());
```
在SpEL表达式中使用Bean的引用来调用Bean的方法，假设这里有一个songSelector Bean，该Bean有一个selectSong()方法：
```xml
<property name="song" value="#{songSelector.selectSong()}"/>
```
在SpEL表达式中使用null-safe存取器避免抛出空指针异常(NullPointerException)：
```xml
<!--没有使用null-safe存取器-->
<property name="song" value="#{songSelector.selectSong().toUpperCase()}"/>
<!--使用null-safe存取器-->
<property name="song" value="#{songSelector.selectSong()?.toUpperCase()}"/>
```
如果没有使用null-safe存取器，则selectSong()返回null值会导致空指针异常，而使用null-safe存取器，即使用`?.`运算符代替点`.`来访问toUpperCase()方法，在访问右方法之前，该运算符会确保左边项的值不会为null。如果为null，则SpEL不再尝试调用toUpperCase()方法。

----------
**操作类：**
在SpEL中，使用T()运算符会调用类作用域的方法和常量。
在SpEL表达式中使用T()运算符将指定类的静态常量装配到Bean的一个属性中：
```xml
<!--只需简单引用Math类的PI常量即可-->
<property name="multiplier" value="#{T(java.lang.Math).PI}"/>
```
使用T()运算符调用静态方法：
```xml
<!--将一个随机数（在0到1之间）装配到Bean的一个属性中-->
<property name="randomNumber" value="#{T(java.lang.Math)random()}"/>
```
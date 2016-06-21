title: String常用方法总结
layout: post
date: 2016-05-09 19:16:43  
comments: true
categories: 
- Java
tags: 
- Java
- String

---

![](http://i.imgur.com/M5OpRZ3.jpg)

<!--more-->
# getChars
```java
/***
 * 将此字符串中的字符复制到目标字符数组
 * @param 字符串中要复制的第一个字符的索引
 * @param 字符串中要复制的最后一个字符之后的索引(不包括该字符)
 * @param 目标数组
 * @param 目标数组中的起始偏移量
 * 说明：以下任一项为true将会抛出IndexOutOfBoundsException异常
 *   1.srcBegin为负
 *   2.srcBegin大于 srcEnd
 *   3.srcEnd大于此字符串的长度
 *   4.dstBegin为负
 *   5.dstBegin+(srcEnd-srcBegin)大于 dst.length
 */
public void getChars(int srcBegin, int srcEnd, char dst[], int dstBegin) {
    if (srcBegin < 0) {
        throw new StringIndexOutOfBoundsException(srcBegin);
    }
    if (srcEnd > value.length) {
        throw new StringIndexOutOfBoundsException(srcEnd);
    }
    if (srcBegin > srcEnd) {
        throw new StringIndexOutOfBoundsException(srcEnd - srcBegin);
    }
    System.arraycopy(value, srcBegin, dst, dstBegin, srcEnd - srcBegin);
}
```

# valueOf
该方法有以下几种形式，分别对应不同的输入类型
```java
/** 返回 Object 参数的字符串表示形式 */
public static String valueOf(Object obj) {
 return (obj == null) ? "null" : obj.toString();
}

/** 
  * 返回 boolean 参数的字符串表示形式
  * 说明：如果参数为 true，则返回一个等于 "true" 的字符串；
  *       否则，返回一个等于 "false" 的字符串。
  */
public static String valueOf(boolean b) {
    return b ? "true" : "false";
}

/** 返回 char 参数的字符串表示形式 */
public static String valueOf(char c) {
    char data[] = {c};
    return new String(data, true);
}

/** 返回 int 参数的字符串表示形式 */
public static String valueOf(int i) {
    return Integer.toString(i);
}

/** 返回 long 参数的字符串表示形式 */
public static String valueOf(long l) {
    return Long.toString(l);
}

/** 返回 float 参数的字符串表示形式 */
public static String valueOf(float f) {
    return Float.toString(f);
}

/** 返回 double 参数的字符串表示形式 */
public static String valueOf(double d) {
    return Double.toString(d);
}

/** 
  * 返回 char 数组参数的特定子数组的字符串表示形式
  * 说明：字符数组的内容已被复制，后续修改不会影响新创建的字符串
  */
public static String valueOf(char data[], int offset, int count) {
    return new String(data, offset, count);
}
```

# intern
**源码：**
```java
/*
Returns a canonical representation for the string object. 

A pool of strings, initially empty, is maintained privately by the class String. 

When the intern method is invoked, if the pool already contains a string equal to
this String object as determined by the equals(Object) method, then the string 
from the pool is returned. Otherwise, this String object is added to the pool 
and a reference to this String object is returned. 

It follows that for any two strings s and t, s.intern() == t.intern() is true 
if and only if s.equals(t) is true. 

All literal strings and string-valued constant expressions are interned. 
String literals are defined in section 3.10.5 of the The Java™ Language Specification.

Returns:
a string that has the same contents as this string, 
but is guaranteed to be from a pool of unique strings.
*/
public native String intern();
```
由源码可知，当调用`intern`方法时，如果字符串资源池中已经包含一个等于该String对象的字符串(通过equals(object)方法确定)，则返回资源池中相等字符串的引用；否则，将该String对象添加到资源池中，并返回该String对象的引用。
例1：
```java
public class StringDemo {
    public static void main(String[] args) {
        String s1 = new String("1");
        s1.intern();
        String s2 = "1";
        System.out.println(s1 == s2);

        String s3 = new StringBuilder("1").append("1").toString();
        s3.intern();
        String s4 = "11";       
        System.out.println(s3 == s4);    	
    }
}
/* output
false
true
*/
```
例2：
```java
public class StringDemo {
    public static void main(String[] args) {
      String str1 = new StringBuilder("hello").append("string").toString();
      System.out.println(str1.intern() == str1);
      String str2 = new StringBuilder("ja").append("va").toString();
      System.out.println(str2.intern() == str2);   	
    }
}
/* output
true
false
*/
```
问题待解决。。。

# 参考资料
1. [深入解析String#intern](http://tech.meituan.com/in_depth_understanding_string_intern.html)
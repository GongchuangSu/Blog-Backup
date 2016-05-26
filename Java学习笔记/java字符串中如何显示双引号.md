title: java字符串中如何显示双引号
layout: post
date: 2016-04-18 20:40:43  
comments: true
categories: 
- Java
tags: 
- Java
- String
- 双引号

---

使用转义字符：
```java
// args[0]:"is"
public class Test {
    public static void main(String[] args) {
        String str1 = "\"boy\"";                      //字符串两边含有双引号  
        String str2 = "He \"is\" a boy";              //字符串中间含有双引号 
        String str3 = "He \"" + args[0] + "\" a boy"; //字符串中间含有双引号  
        String str4 = "\\boy";                        //使用转义字符还可以使字符串包含其他字符  

        System.out.println("str1:" + str1);
        System.out.println("str2:" + str2);
        System.out.println("str3:" + str3);
        System.out.println("str4:" + str4);
    }
}
/* output:
str1:"boy"
str2:He "is" a boy
str3:He "is" a boy
str4:\boy
*/
```

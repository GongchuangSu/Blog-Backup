title: 字节序（Endianness）
layout: post
date: 2016-03-22 12:57:43  
comments: true
type: "categories"
categories: 
- 计算机系统
tags: 
- 字节序
- 大端序（big-endian）
- 小端序（little-endian）

---
# 简介
<blockquote class="blockquote-center">“endian”一词来源于乔纳森·斯威夫特的小说格列佛游记。小说中，小人国为水煮蛋该从大的一端（Big-End）剥开还是小的一端（Little-End）剥开而争论，争论的双方分别被称为“大端派”和“小端派”。</blockquote>

字节序，又称端序，尾序（Endianness）。在计算机科学邻域，是跨越多字节的程序对象的存储规则。

<!--more-->

在几乎所有的机器上，多字节对象都被存储为连续的字节序列。例如，在C语言中，一个类型为`int`的变量`x`地址为`0x100`，那么，x的四个字节将被存储在存储器的`0x100，0x101, 0x102, 0x103`位置。而存储地址内的排列则有两个通用规则。一个多位的整数将按照其存储地址的最低或最高字节排列。如果最低有效位在最高有效位的前面，则称**小端序**；反之则称**大端序**。
下面以内存中0x0A0B0C0D的存放方式为例，分别有以下几种方式：

# 大端序（big-endian）
**数据以8bit为单位:**
![](http://i.imgur.com/iiJmkVI.png)
![](http://i.imgur.com/JJXwuof.png)

# 小端序（little-endian）
**数据以8bit为单位:**
![](http://i.imgur.com/h5sdFfY.png)
![](http://i.imgur.com/xbTT7mt.png)

# 大端小端转换方法
Big-Endian转换成Little-Endian:
```cpp
#define BigtoLittle16(A) ((((uint16)(A) & 0xff00) >> 8) | (((uint16)(A) & 0x00ff) << 8))
#define BigtoLittle32(A) ((((uint32)(A) & 0xff000000) >> 24) | (((uint32)(A) & 0x00ff0000) >> 8) | \
             (((uint32)(A) & 0x0000ff00) << 8) | (((uint32)(A) & 0x000000ff) << 24))
```

# 大端小端检测方法

```c
int i = 1;   
    char *p = (char *)&i;   
    if(*p == 1)     
          printf("Little Endian"); 
    else
          printf("Big Endian");
```
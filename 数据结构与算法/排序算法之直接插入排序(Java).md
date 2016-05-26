title: 排序算法之直接插入排序(Java)
layout: post
date: 2016-03-28 19:55:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 直接插入排序

---
# 直接插入排序简介
**直接插入排序**（Insertion Sort）是一种简单直观的排序算法。其工作原理是通过构建有序序列，对于未排序数据，在已排序序列中从后向前扫描，找到相应位置并插入。

<!--more-->

# 直接插入排序过程
- 1.从第一个元素开始，该元素可以认为已经被排序
- 2.取出下一个元素，在已经排序的元素序列中从后向前扫描
- 3.如果该元素（已排序）大于新元素，将该元素移到下一个位置
- 4.重复步骤3，直到找到已排序的元素小于或者等于新元素的位置
- 5.将新元素插入到该位置后
- 6.重复步骤2~5

![](http://7xsc5j.com1.z0.glb.clouddn.com/%E7%9B%B4%E6%8E%A5%E6%8F%92%E5%85%A5%E6%8E%92%E5%BA%8F%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现
```java
/**
 * @description 对顺序表L作直接插入排序
 * @author GongchuangSu
 * @since 2016.03.28
 * @version v1.0
 */
public class InsertSort {
	public static void main(String[] args) {
    	// sqList[0]用作哨兵或临时变量
        int[] sqList = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        insertSort(sqList);
        // 输出打印
        for (int i = 1; i < sqList.length; i++)
            System.out.println(String.valueOf(sqList[i]));
    }

	/**
     * 功能：直接插入排序法
     */
    private static void insertSort(int[] sqList) {
        int i, j;
        for (i = 2; i <= sqList.length - 1; i++) {
            if (sqList[i] < sqList[i - 1]) {
                sqList[0] = sqList[i];
                for (j = i - 1; sqList[j] > sqList[0]; j--)
                    sqList[j + 1] = sqList[j];
                sqList[j + 1] = sqList[0];
            }
        }
    }
}

```

# 算法分析
## 时间复杂度
在最好情况下，即要排序的数组本身就是有序的，则其时间复杂度为$\Theta(n)$；
在最坏的情况下，其时间复杂度为$\Theta(n^2)$。

## 空间复杂度
很明显，其空间复杂度为$\Theta(1)$。

## 稳定性
直接插入排序是稳定的。

title: 排序算法之简单选择排序(Java)
layout: post
date: 2016-03-29 11:32:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 简单选择排序

---
# 选择排序简介
选择排序（Selection Sort）是一种简单直观的排序算法。首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

<!--more-->

# 简单选择排序过程
简单排序过程很简单，其具体流程如下图所示：

![](http://7xsc5j.com1.z0.glb.clouddn.com/%E7%AE%80%E5%8D%95%E9%80%89%E6%8B%A9%E6%8E%92%E5%BA%8F%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现
```java
/**
 * @description 对顺序表L作简单选择排序
 * @author GongchuangSu
 * @since 2016.03.29
 * @version v1.0
 */
public class SelectSort {
    public static void main(String[] args) {
        // sqList[0]用作哨兵或临时变量
        int[] sqList = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        selectSort(sqList);
        // 输出打印
        for (int i = 1; i < sqList.length; i++)
            System.out.println(String.valueOf(sqList[i]));
    }

    /**
     * 功能：简单选择排序
     */
    private static void selectSort(int[] sqList) {
        int i, j, min;
        for (i = 1; i < sqList.length - 1; i++) {
            // 将当前下标定义为最小值下标
            min = i;
            for (j = i + 1; j <= sqList.length - 1; j++) {
                // 如果有小于当前最小值的关键字，则将此关键字的下标赋值给min
                if (sqList[min] > sqList[j])
                    min = j;
            }
            // 如果min不等于i，说明找到比sqList[i]更小的值，则进行交换
            if (i != min)
                sqList[i] = returnFirst(sqList[min], sqList[min] = sqList[i]);
        }
    }

    /**
     * 功能：用于两个数据的交换，相当于C/C++中的swap()函数
     */
    private static int returnFirst(int x, int y) {
        return x;
    }
}

```

# 算法分析
## 时间复杂度
简单选择排序的主要优点与数据移动有关。如果某个元素位于正确的最终位置上，则它不会被移动。
就**交换次数**而言，在最好情况下，交换次数为`0`；在最坏的情况下，交换次数为`n-1`。
无论最好最坏情况，其**比较次数**都是一样多的，基于最终的排序时间是比较和交换的次数总和，其时间复杂度为$\Theta(n^2)$。
在所有的完全依靠交换去移动元素的排序方法中，选择排序属于非常好的一种。

## 空间复杂度
很明显，其空间复杂度为$\Theta(1)$。

## 稳定性
选择排序是稳定的。

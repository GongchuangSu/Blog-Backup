title: 排序算法之希尔排序(Java)
layout: post
date: 2016-04-01 18:55:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 希尔排序

---
# 希尔排序简介
希尔排序（Shell Sort），也称递减增量排序算法，是`Donald Shell`在1959年提出来的一种排序算法。它是插入排序的一种更高效的改进版本。

<!--more-->

# 希尔排序思想
希尔排序通过将比较的全部元素分为几个区域来提升插入排序的性能。这样可以让一个元素可以一次性地朝最终位置前进一大步。然后算法再取越来越小的步长进行排序，算法的最后一步就是普通的插入排序，但是到了这步，需排序的数据几乎是已经排好的了。

# 步长序列
步长的选择是希尔排序的重要部分。只要最终步长为1任何步长序列都可以工作。算法最开始以一定的步长进行排序。然后会继续以一定步长进行排序，最终算法以步长为1进行排序。

# 希尔排序过程
具体排序过程如下图所示：
![](http://7xsc5j.com1.z0.glb.clouddn.com/%E5%B8%8C%E5%B0%94%E6%8E%92%E5%BA%8F%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现
```java
/**
 * @description 对顺序表L作希尔排序
 * @author GongchuangSu
 * @since 2016.04.01
 * @version v1.0
 */
public class ShellSort {
    public static void main(String[] args) {
        // sqList[0]用作哨兵或临时变量
        int[] sqList = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        shellSort(sqList);
        // 输出打印
        for (int i = 1; i < sqList.length; i++)
            System.out.println(String.valueOf(sqList[i]));
    }

    /**
     * 功能：希尔排序
     */
    private static void shellSort(int[] sqList) {
        int i, j;
        int increment = sqList.length - 1;
        do {
        	// 增量序列
            increment = increment / 3 + 1;
            for (i = increment + 1; i < sqList.length; i++) {
                if (sqList[i] < sqList[i - increment]) {
                	// 需将sqList[i]插入有序增量子表
                    sqList[0] = sqList[i]; // 暂存sqList[0]
                    // 记录后移，查找插入位置
                    for (j = i - increment; j > 0 && sqList[0] < sqList[j]; j -= increment)
                        sqList[j + increment] = sqList[j];
                    sqList[j + increment] = sqList[0];
                }
            }
        } while (increment > 1);
    }
}
```

# 算法分析
## 时间复杂度
在最好的情况下，其时间复杂度为$\Theta(n^1.3)$；
在最坏的情况下，其时间复杂度为$\Theta(n^2)$。

## 空间复杂度
很明显，其空间复杂度为$\Theta(1)$。

## 稳定性
希尔排序是不稳定的。

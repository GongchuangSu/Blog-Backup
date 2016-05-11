title: 排序算法之归并排序(Java)
layout: post
date: 2016-03-27 17:26:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 归并排序

---
<blockquote class="blockquote-center">若人们不相信数学简单，只因他们未意识到生命之复杂。
—— 约翰·冯·诺伊曼</blockquote>

# 归并排序简介
归并排序(Merge Sort)，是创建在归并操作上的一种有效的排序算法。1945年由约翰·冯·诺伊曼首次提出。该算法是采用分治法(Divide and Conquer)的一个非常典型的应用，且各层分治递归可以同时进行。

<!--more-->

## 归并操作
归并操作(merge)，也叫归并算法，是指将两个**已经排序**的序列合并成一个序列的操作。
归并操作有两种方法可实现：**迭代法**和**递归法**。本篇主要介绍性能最优的迭代法。

## 归并排序思想
假设初始序列含有n个记录，则可以看成n个有序的子序列，每个子序列的长度为1,然后两两归并，得到$\lceil n/2 \rceil$（$\lceil x \rceil$表示不小于x的最小整数）个长度为2或1的有序子序列；再两两归并，···，如此重复，直至得到一个长度为n的有序序列为止，这种排序方法称为2路归并排序。

# 归并排序过程
具体过程示意图：
![](http://7xsc5j.com1.z0.glb.clouddn.com/%E5%BD%92%E5%B9%B6%E6%8E%92%E5%BA%8F%E8%BF%AD%E4%BB%A3%E8%BF%87%E7%A8%8B.png)

# 算法实现
```java
/**
 * @description 对顺序表作归并非递归排序
 * @author GongchuangSu
 * @since 2016.03.27
 * @version v1.0
 */
public class MergeSort {
    public static void main(String[] args) {
    	// SR[0]和TR[0]用作哨兵或临时变量
        int[] SR = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        int[] TR = new int[10];
        TR[0] = 1000;
        int k = 1;
        while (k < SR.length) {
            MergePass(SR, TR, k, SR.length - 1);
            k = 2 * k;
            MergePass(TR, SR, k, SR.length - 1);
            k = 2 * k;
        }
        for (int i = 0; i < TR.length; i++) {
            System.out.println( String.valueOf(SR[i]));
        }
    }

    /**
     * 功能：将SR[]中相邻长度为s的子序列两两归并到TR[]
     */
    private static void MergePass(int SR[], int TR[], int s, int n) {
        int i = 1;
        int j;
        while (i <= n - 2 * s + 1) {
            Merge(SR, TR, i, i + s - 1, i + 2 * s - 1);
            i = i + 2 * s;
        }
        if (i < n - s + 1) // 归并最后两个序列
            Merge(SR, TR, i, i + s - 1, n);
        else // 若最后只剩下单个子序列
            for (j = i; j <= n; j++)
                TR[j] = SR[j];
    }

    /**
     * 功能：将有序的SR[i...m]和SR[m+1...n]归并为有序的TR[i...n]
     */
    private static void Merge(int SR[], int TR[], int i, int m, int n) {
        int j, k, l;
        // 将SR中记录由小到大归并入TR
        for (j = m + 1, k = i; i <= m && j <= n; k++) {
            if (SR[i] < SR[j]) {
                TR[k] = SR[i];
                i = i + 1;
            } else {
                TR[k] = SR[j];
                j = j + 1;
            }
        }
        // 将剩余的SR[i...m]复制到TR
        if (i <= m) {
            for (l = 0; l <= m - i; l++)
                TR[k + l] = SR[i + l];
        }
        // 将剩余的SR[j..n]复制到TR
        if (j <= n) {
            for (l = 0; l <= n - j; l++)
                TR[k + l] = SR[j + l];
        }
    }
}

```
输出结果：
```java
1000
10
20
30
40
50
60
70
80
90
```

# 算法分析
## 时间复杂度
对于递归法，总的时间复杂度为$\Theta(n\log n)$，而且是其最好、最坏、平均的时间性能。
对于迭代法，其最优时间复杂度为$\Theta(n)$。

## 空间复杂度
对于递归法，其空间复杂度为$\Theta(n + \log n)$。
对于迭代法，其空间复杂度为$\Theta(n)$。

## 稳定性
归并排序是一种稳定的排序方法。



title: 排序算法之堆排序(Java)
layout: post
date: 2016-04-03 11:38:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 堆排序

---
# 堆排序简介
堆排序（Heap Sort），是指利用堆这种数据结构所设计的一种排序算法。
**堆排序的基本思想**：将待排序的序列构造成一个大顶堆。此时，整个序列的最大值就是堆顶的根结点。将它与末尾元素交换，此时末尾元素就是最大值，然后将剩余的`n-1`个序列重新构造成一个堆，这样就会得到n个元素中的次小值。如此反复执行，便能得到一个有序序列。

<!--more-->

# 堆结点的访问
通常堆是通过一维数组来实现的。在数组起始位置为0的情形下：
- 父节点i的左子节点在位置(2*i+1)
- 父节点i的右子节点在位置(2*i+2)
- 子节点i的父节点在位置floor((i-1)/2)

# 堆排序过程
具体排序过程如下图所示：
![](http://7xsc5j.com1.z0.glb.clouddn.com/%E5%A0%86%E6%8E%92%E5%BA%8F%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现
```java
/**
 * @description 对顺序表L作堆排序
 * @author GongchuangSu
 * @since 2016.04.01
 * @version v1.0
 */
public class HeapSort {
    public static void main(String[] args) {
        // sqList[0]用作哨兵或临时变量
        int[] sqList = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        heapSort(sqList);
        // 输出打印
        for (int i = 1; i < sqList.length; i++)
            System.out.println(String.valueOf(sqList[i]));
    }

    /**
     * 功能：对顺序表L进行堆排序
     */
    private static void heapSort(int[] sqList) {
        int i;
        /*
         * 第一步：将sqList构建成一个大顶堆
         */
        for (i = (sqList.length - 1) / 2; i > 0; i--)
            heapAdjust(sqList, i, sqList.length - 1);
        /*
         * 第二步：将堆顶记录和当前未经排序子序列的最后一个记录交换，并且再调整其成为大顶堆
         */
        for (i = sqList.length - 1; i > 1; i--) {
            sqList[i] = returnFirst(sqList[1], sqList[1] = sqList[i]);
            heapAdjust(sqList, 1, i - 1);
        }
    }

    /**
     * 功能：已知sqList[s..m]中记录的关键字除sqList[s]外均满足堆的定义
     *       该函数调整sqList[s]的关键字，使sqList[s..m]成为一个大顶堆
     */
    private static void heapAdjust(int[] sqList, int s, int m) {
        int temp, j;
        temp = sqList[s];
        // 沿关键字较大的孩子结点向下筛选
        for (j = 2 * s; j <= m; j *= 2) {
            if (j < m && sqList[j] < sqList[j + 1])
                ++j; // j为关键字中较大的记录的下标
            if (temp >= sqList[j])
                break;
            sqList[s] = sqList[j];
            s = j;
        }
        sqList[s] = temp; // 插入
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
由于堆排序对原始记录的排序状态并不敏感，因此它无论是最好、最坏和平均时间复杂度均为$\Theta(nlogn)$

## 空间复杂度
很明显，其空间复杂度为$\Theta(1)$。

## 稳定性
由于记录的比较和交换是跳跃式的，所以堆排序是不稳定的。

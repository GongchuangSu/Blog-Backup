title: 排序算法之快速排序(Java)
layout: post
date: 2016-03-28 17:05:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 快速排序

---
<blockquote class="blockquote-center">There are two ways of constructing a software design: One way is to make it so simple that there are obviously no deficiencies, and the other way is to make it so complicated that there are no obvious deficiencies. The first method is far more difficult.
—— C.A.R. Hoare (Designer of QuickSort)</blockquote>

# 快速排序简介
快速排序（Quick Sort）是由东尼·霍尔所发展的的一种排序算法，被列为20世纪十大算法之一。其发明者——东尼·霍尔（Tony Hoare），出生于斯里兰卡可伦坡，英国计算机科学家，图灵奖得主。

<!--more-->

# 快速排序思想
**快速排序（Quick Sort）**的基本思想：通过一趟排序将待排记录分割成独立的两部分，其中一部分记录的关键字均比另一部分记录的关键字小，则可分别对这两部分记录继续进行排序，以达到整个序列有序的目的。

# 快速排序过程
假设需要排序的数组为`sqList = {1000, 50, 10, 90, 30, 70, 40, 80, 60, 20}`
快速排序最关键的`partition`函数：先选取当中的一个关键字，比如选择第一个关键字50，然后想尽办法将它放到一个位置，使得它的左边的值都比它小，右边的值都比它大，这样的关键字称为枢轴（`pivot`）。
在选取枢轴时，我们可通过**三数取中法**进行优化，即取三个关键字先进行排序，将中间数作为枢轴，一般是取左端、右端和中间三个数，也可随机选取。
**快速排序过程示意图：**
![](http://7xsc5j.com1.z0.glb.clouddn.com/%E5%BF%AB%E9%80%9F%E6%8E%92%E5%BA%8F%E8%BF%87%E7%A8%8B.png)
上面示意图只是一个递归过程，进行多次即可得到有序的数组。
**`partition`函数具体实现：**
```java
/**
 * 功能：选取关键点，使其成为枢轴，并返回其所在位置
 */
private static int partition(int[] sqList, int low, int high) {
    int pivotkey;
    // 三数取中
    int m = low + (high - low) / 2;
    if (sqList[low] > sqList[high])
        sqList[low] = returnFirst(sqList[high], sqList[high] = sqList[low]);
    if (sqList[m] > sqList[high])
        sqList[m] = returnFirst(sqList[high], sqList[high] = sqList[m]);
    if (sqList[m] > sqList[low])
        sqList[low] = returnFirst(sqList[m], sqList[m] = sqList[low]);
    pivotkey = sqList[low];
    sqList[0] = pivotkey; // 将枢轴关键字备份到sqList[0]
    while (low < high) {
        while (low < high && sqList[high] >= pivotkey)
            high--;
        sqList[low] = sqList[high]; // 采用替换而不是交换的方式进行操作，将比枢轴记录小的记录替换到低端
        while (low < high && sqList[low] <= pivotkey)
            low++;
        sqList[high] = sqList[low]; // 采用替换而不是交换的方式进行操作，将比枢轴记录					
    }
    sqList[low] = sqList[0]; // 将枢轴数值替换回sqList[low]	
    return low; // 返回枢轴所在位置
}
```

# 快速排序复杂度分析
## 时间复杂度
快速排序的时间性能取决于快速排序递归的深度。
在最优情况下，`partition`每次都划分得很均匀，则其时间复杂度为$\Theta(n\log n)$；
在最坏情况下，待排序的序列为正序或逆序，则其时间复杂度为$\Theta(n^2)$。

## 空间复杂度
在最优情况下，其空间复杂度为$\Theta(\log n)$。
在最坏情况下，其空间复杂度为$\Theta(n)$。

## 稳定性
由于关键字的比较是跳跃性的，所以快速排序是一种不稳定的排序方法。

# 算法实现
```java
/**
 * @description 对顺序表L作快速排序
 * @author GongchuangSu
 * @since 2016.03.28
 * @version v1.0
 */
public class QuickSort {
    private static final int MAX_LENGTH_INSERT_SORT = 7; // 数组长度阈值

    public static void main(String[] args) {
    	// sqList[0]用作哨兵或临时变量
        int[] sqList = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        QSort(sqList, 1, sqList.length - 1);
        // 输出打印
        for (int i = 1; i < sqList.length; i++)
            System.out.println(String.valueOf(sqList[i]));
    }

    private static void QSort(int[] sqList, int low, int high) {
        int pivot;
        if ((high - low) > MAX_LENGTH_INSERT_SORT) { // 当high - low大于常数时用快速排序
            while (low < high) {
                // 将sqList[low...high]一分为二，并算出枢轴值pivot
                pivot = partition(sqList, low, high);
                QSort(sqList, low, pivot - 1); // 对低子表递归排序
                low = pivot + 1;
            }
        } else { // 当high - low小于等于常数时用直接插入排序
            insertSort(sqList);
        }
    }

    /**
     * 功能：选取关键点，使其成为枢轴，并返回其所在位置
     */
    private static int partition(int[] sqList, int low, int high) {
        int pivotkey;
        // 三数取中
        int m = low + (high - low) / 2;
        if (sqList[low] > sqList[high])
            sqList[low] = returnFirst(sqList[high], sqList[high] = sqList[low]);
        if (sqList[m] > sqList[high])
            sqList[m] = returnFirst(sqList[high], sqList[high] = sqList[m]);
        if (sqList[m] > sqList[low])
            sqList[low] = returnFirst(sqList[m], sqList[m] = sqList[low]);
        pivotkey = sqList[low];
        sqList[0] = pivotkey; // 将枢轴关键字备份到sqList[0]
        while (low < high) {
            while (low < high && sqList[high] >= pivotkey)
                high--;
            sqList[low] = sqList[high]; // 采用替换而不是交换的方式进行操作，将比枢轴记录小的记录替换到低端
            while (low < high && sqList[low] <= pivotkey)
                low++;
            sqList[high] = sqList[low]; // 采用替换而不是交换的方式进行操作，将比枢轴记录					
        }
        sqList[low] = sqList[0]; // 将枢轴数值替换回sqList[low]	
        return low; // 返回枢轴所在位置
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

    /**
     * 功能：用于两个数据的交换，相当于C/C++中的swap()函数
     */
    private static int returnFirst(int x, int y) {
        return x;
    }
}
```
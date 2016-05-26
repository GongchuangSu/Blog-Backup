title: 排序算法之冒泡排序(Java)
layout: post
date: 2016-03-29 10:22:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 排序算法
- 冒泡排序

---
# 冒泡排序简介
**冒泡排序（Bubble Sort）**是一种简单的排序算法。它重复的走访过要排序的数列，一次比较两个元素，如果反序则交换，重复地进行直到不再需要交换。

<!--more-->

# 冒泡排序过程
- 1.比较相邻的元素，如果第一个比第二个大，就进行交换
- 2.对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数
- 3.针对所有的元素重复以上的步骤，除了最后一个
- 4.持续每次对越来越少的元素重复上面的步骤，知道没有任何一对数字需要比较
仔细分析，这种算法的效率是非常低的。我们可进行简单的改进，元素交换不限于相邻的两个元素，部分流程图如下图所示：
![](http://7xsc5j.com1.z0.glb.clouddn.com/%E5%86%92%E6%B3%A1%E6%8E%92%E5%BA%8F%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现
```java
/**
 * @description 对顺序表L作冒泡排序
 * @author GongchuangSu
 * @since 2016.03.29
 * @version v1.0
 */
public class BubbleSort {
    public static void main(String[] args) {
        // sqList[0]用作哨兵或临时变量
        int[] sqList = {
            1000, 50, 10, 90, 30, 70, 40, 80, 60, 20
        };
        bubbleSort(sqList);
        // 输出打印
        for (int i = 1; i < sqList.length; i++)
            System.out.println(String.valueOf(sqList[i]));
    }

    /**
     * 功能：冒泡排序法
     */
    private static void bubbleSort(int[] sqList) {
        int i, j;
        boolean flag = true;
        for (i = 1; i < sqList.length - 1 && flag; i++) {
            flag = false;
            for (j = sqList.length - 2; j >= i; j--) {
                if (sqList[j] > sqList[j + 1]) {
                    // 交换sqList[j]和sqList[j+1]的值
                    sqList[j] = returnFirst(sqList[j + 1], sqList[j + 1] = sqList[j]);
                    // 如果有数据交换，则flag为true
                    flag = true;
                }
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

# 算法分析
## 时间复杂度
在最好情况下，即要排序的数组本身就是有序的，则其时间复杂度为$\Theta(n)$；
在最坏的情况下，即要排序的数组是逆序的，其时间复杂度为$\Theta(n^2)$。

## 空间复杂度
很明显，其空间复杂度为$\Theta(1)$。

## 稳定性
冒泡排序是稳定的。

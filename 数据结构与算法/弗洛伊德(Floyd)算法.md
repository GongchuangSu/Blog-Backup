title: 弗洛伊德(Floyd)算法
layout: post
date: 2016-04-18 18:47:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 最短路径
- 算法
- 弗洛伊德(Floyd)算法

---
<center>
![enter image description here](http://7xsc5j.com1.z0.glb.clouddn.com/Floyd.jpg)
</center>

<!--more-->

# 简介
罗伯特•弗洛伊德（Robert W．Floyd）计算机科学家，图灵奖得主，前后断言法的创始人，堆排序算法和Floyd-Warshall算法的创始人之一。第一次把“不确定性”概念引入程序，并且在分析理论、程序设计语言的逻辑与语义、自动程序验证、自动程序综合、算法分析以及编译器方面做出了巨大贡献，被授予第十三届图灵奖。

Floyd-Warshall算法，中文亦称弗洛伊德算法，是解决任意两点间的最短路径的一种算法，可以正确处理有向图或负权的最短路径问题，同时也被用于计算有向图的传递闭包。

# 算法流程
弗洛伊德算法的原理是动态规划。假设图G中顶点个数为N，则需要对矩阵S进行N次更新。
- 初始时，矩阵S中顶点a[i][j]的距离为顶点i到顶点j的权值；如果i和j不相邻，则a[i][j]=∞。
- 接下来开始，对矩阵S进行N次更新。第1次更新时，如果"a[i][j]的距离" > "a[i][0]+a[0][j]"(a[i][0]+a[0][j]表示"i与j之间经过第1个顶点的距离")，则更新a[i][j]为"a[i][0]+a[0][j]"。
- 同理，第k次更新时，如果"a[i][j]的距离" > "a[i][k]+a[k][j]"，则更新a[i][j]为"a[i][k]+a[k][j]"。
- 更新N次之后，操作完成！

# 算法实现(Java)
```java
/**
 * @description 最短路径之弗洛伊德(Floyd)算法(即求网图G中各顶点i到其余顶点j的最短路径prev[i][j]及带权长度dist[i][j])
 *              输入 --> G：无向有权图                          
 *              输出 --> 最短距离数组及长度
 * @author      GongchuangSu
 * @since       2016.04.18
 * @version     v1.0
 */
public class Floyd {
    WeightedGraph G;
    int vlen;     // 顶点个数
    int[][] prev; // 前驱顶点数组
    int[][] dist; // 最短路径数组
    private static int INF = Integer.MAX_VALUE;

    public void floyd(WeightedGraph G) {
        this.G = G;
        vlen = G.size();
        prev = new int[vlen][vlen];
        dist = new int[vlen][vlen];

        // 初始化前驱顶点数组和最短路径数组
        for (int i = 0; i < vlen; i++) {
            for (int j = 0; j < vlen; j++) {
                dist[i][j] = G.getWeight(i, j); // 顶点i到顶点j的路径长度为i到j的权值
                prev[i][j] = j;                 // 顶点i到顶点j的最短距离是经过顶点j
            }
        }

        // 三重循环，找出所有顶点到所有顶点的最短距离
        for (int k = 0; k < vlen; k++) {
            for (int v = 0; v < vlen; v++) {
                for (int w = 0; w < vlen; w++) {
                	// 如果经过k顶点的路径比原两点间路径更短，则将当前两点间权值设为更小的一个
                    int temp = (dist[v][k] == INF || dist[k][w] == INF) ? INF : (dist[v][k] + dist[k][w]);
                    if (dist[v][w] > temp) {
                        dist[v][w] = temp;
                        prev[v][w] = prev[v][k];
                    }
                }
            }
        }
    }

    /**
     * 功能：打印最短路径数组和前驱顶点数组
     */
    public void print() {
        System.out.printf("Floyd:\n");
        System.out.printf("最短路径数组:\n");
        for (int i = 0; i < vlen; i++) {
            for (int j = 0; j < vlen; j++)
                System.out.printf("%2d ", dist[i][j]);
            System.out.printf("\n");
        }
        System.out.printf("前驱顶点数组:\n");
        for (int i = 0; i < vlen; i++) {
            for (int j = 0; j < vlen; j++)
                System.out.printf("%d ", prev[i][j]);
            System.out.printf("\n");
        }
    }

    /**
     * 功能：根据起点source和终点target输出最短路径
     */
    public void getPath(int source, int target) {
        System.out.printf("(%d,%d)最短路径为：\n", source, target);
        System.out.printf("%d", target);
        int k = target;
        while (prev[k][source] != k) {
            System.out.printf(" <-- %d", prev[k][source]);
            k = prev[k][source];
        }
    }
}
```
源代码下载：[弗洛伊德(Floyd)算法](https://github.com/GongchuangSu/Data_Structures_and_Algorithms/tree/master/%E5%9B%BE%28Graph%29/%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84/%E5%BC%97%E6%B4%9B%E4%BC%8A%E5%BE%B7%28Floyd%29%E7%AE%97%E6%B3%95)

## 时间复杂度
由算法代码中的循环嵌套可得知其算法时间复杂度为$O(n^3)$。

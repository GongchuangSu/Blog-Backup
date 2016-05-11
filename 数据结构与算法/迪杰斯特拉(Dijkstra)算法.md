title: 迪杰斯特拉(Dijkstra)算法
layout: post
date: 2016-04-18 17:24:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 最短路径
- 算法
- 迪杰斯特拉(Dijkstra)算法

---
![enter image description here](http://7xsc5j.com1.z0.glb.clouddn.com/675px-Edsger_Wybe_Dijkstra.jpg)
<!--more-->
# 简介
迪杰斯特拉(Dijkstra)算法是由荷兰计算机科学家艾兹赫尔·戴克斯特拉提出。迪杰斯特拉算法使用了广度优先搜索解决非负权有向图的单源最短路径问题，算法最终得到一个最短路径树。

# 算法流程
- 初始时，S只包含起点s；U包含除s外的其他顶点，且U中顶点的距离为"起点s到该顶点的距离"[例如，U中顶点v的距离为(s,v)的长度，然后s和v不相邻，则v的距离为∞]。
- 从U中选出"距离最短的顶点k"，并将顶点k加入到S中；同时，从U中移除顶点k。
- 更新U中各个顶点到起点s的距离。之所以更新U中顶点的距离，是由于上一步中确定了k是求出最短路径的顶点，从而可以利用k来更新其它顶点的距离；例如，(s,v)的距离可能大于(s,k)+(k,v)的距离。
- 重复步骤(2)和(3)，直到遍历完所有顶点。

具体流程如下图所示：
![enter image description here](http://7xsc5j.com1.z0.glb.clouddn.com/%E8%BF%AA%E6%9D%B0%E6%96%AF%E7%89%B9%E6%8B%89%28Dijkstra%29%E7%AE%97%E6%B3%95%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现(Java)
```java
/**
 * @description 最短路径之迪杰斯特拉(Dijkstra)算法，即计算某顶点到其它各个顶点的最短路径及带权长度
 *              输入 -->    G：无向有权图
 *                      start:起始顶点(即计算顶点s到其它顶点的最短距离)                            
 *              输出 --> 最短距离数组及长度
 * @author      GongchuangSu
 * @since       2016.04.18
 * @version     v1.0
 */

public class Dijkstra {
    int vlen;       // 顶点个数
    boolean[] flag; // flag[i]=true表示顶点start到顶点i的最短路径已求得
    private static int INF = Integer.MAX_VALUE;
    int start;      // 起始顶点
    int[] prev;     // 前驱顶点数组
    int[] dist;     // 最短路径数组

    public void dijkstra(WeightedGraph G, int start) {
        this.start = start;
        vlen = G.size();
        flag = new boolean[vlen];
        prev = new int[vlen];
        dist = new int[vlen];

        // 初始化
        for (int i = 0; i < vlen; i++) {
            flag[i] = false;
            prev[i] = start;
            dist[i] = G.getWeight(start, i);
        }

        // 初始化起始顶点
        flag[start] = true;
        dist[start] = 0;

        // 开始主循环，每次求得起始顶点到其它某个顶点的最短距离
        int k = 0;
        for (int i = 0; i < vlen; i++) {
            int min = INF;                   // 当前所知离起始顶点的最近距离
            for (int j = 0; j < vlen; j++) { // 寻找离起始顶点最近的顶点
                if (flag[j] == false && dist[j] < min) {
                    min = dist[j];
                    k = j;
                }
            }

            flag[k] = true; // 将当前找到的最近的顶点标记

            // 修正当前最短路径及距离
            for (int j = 0; j < vlen; j++) {
                int temp = (G.getWeight(k, j) == INF ? INF : (min + G.getWeight(k, j)));
                if (!flag[j] && (temp < dist[j])) {
                    dist[j] = temp;
                    prev[j] = k;
                }
            }
        }
    }

    /**
     * 功能：打印最短路径数组和前驱顶点数组
     */
    public void print() {
        System.out.printf("Dijkstra(%d):\n", start);
        System.out.printf("最短路径数组:\n");
        for (int i = 0; i < vlen; i++)
            System.out.printf("%d ", dist[i]);
        System.out.print("\n");
        System.out.print("前驱顶点数组:\n");
        for (int i = 0; i < vlen; i++)
            System.out.printf("%d ", prev[i]);
        System.out.printf("\n");
    }
    
    /**
     * 功能：根据起点source和终点target输出最短路径
     */
    public void getPath(int target){
    	System.out.printf("(%d,%d)最短路径为：\n" ,start ,target);
    	System.out.printf("%d", target);
    	int k = target;
    	while(prev[k] != k){
    		System.out.printf(" <-- %d", prev[k]);
    		k = prev[k];
    	}
    	System.out.printf("\n");
    }
}
```
源代码下载：[迪杰斯特拉(Dijkstra)算法](https://github.com/GongchuangSu/Data_Structures_and_Algorithms/tree/master/%E5%9B%BE%28Graph%29/%E6%9C%80%E7%9F%AD%E8%B7%AF%E5%BE%84/%E8%BF%AA%E6%9D%B0%E6%96%AF%E7%89%B9%E6%8B%89%28Dijkstra%29%E7%AE%97%E6%B3%95)

## 时间复杂度
由算法代码中的循环嵌套可得知其算法时间复杂度为$O(n^2)$。

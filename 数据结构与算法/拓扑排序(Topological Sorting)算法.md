title: 拓扑排序算法(Topological Sorting)
layout: post
date: 2016-04-19 15:35:43  
comments: true
type: "categories"
categories: 
- 数据结构与算法
tags: 
- 算法
- 拓扑排序算法

---

# 拓扑排序(Topological Sorting)
在图论中，由一个有向无环图的顶点组成的序列，当且仅当满足下列条件时，称为该图的一个拓扑排序：
- 每个顶点出现且只出现一次；
- 若A在序列中排在B的前面，则在图中不存在从B到A的路径。

<!--more-->

用顶点表示活动，用弧表示活动之间的优先关系，这样的有向图为顶点表示活动的网，称为**AOV网（Activity On Vertex Network）**。
另外，AOV网是一种有向无回路的图。那么，每个AOV网都至少存在一个拓扑排序。

# 拓扑排序算法
对AOV网进行拓扑排序的基本思路如下：
- 从AOV网中选择一个入度为0的顶点输出
- 删除此顶点，并删除以此顶点为尾的弧（更新顶点入度）
- 继续重复前面两个步骤，直到全部定点输出

大概流程图如下图所示：
![enter image description here](http://7xsc5j.com1.z0.glb.clouddn.com/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95%E6%B5%81%E7%A8%8B%E5%9B%BE.png)

# 算法实现
```java
/**
 * @description 拓扑排序算法（TopologicalSort）
 *          输入：领接表表示的有向图
 *          输出：若图无回路，则输出拓扑排序序列并返回OK
 *                若图有回路，则返回ERROR.     
 * @author  GongchuangSu
 * @since   2016.04.19
 * @version v1.0
 */
import java.util.LinkedList;
import java.util.Queue;
import java.util.Stack;

public class TopologicalSort {
    Stack < Integer > stack; // 用于存储入度为0的顶点的下标
    Queue < Integer > queue; // 用于存储拓扑排序结果
    int vlen;                // 顶点个数
    int[] indegree;          // 入度数组
    int count;               // 统计输出顶点个数

    public String topologicalSort(ListDG listDG) {
        stack = new Stack < Integer > ();
        queue = new LinkedList < Integer > ();
        this.vlen = listDG.vlen;
        indegree = new int[vlen];

        // 统计每个顶点的入度数
        for (int i = 0; i < vlen; i++) {
            ListDG.EdgeNode edgeNode = new ListDG.EdgeNode();
            edgeNode = listDG.vertexNodeList[i].firstedge;
            while (edgeNode != null) {
                indegree[edgeNode.adjvex]++;
                edgeNode = edgeNode.next;
            }
        }

        // 将入度为0的顶点入栈
        for (int i = 0; i < vlen; i++) {
            if (indegree[i] == 0)
                stack.push(i);
        }

        while (!stack.isEmpty()) {
            int top = stack.pop(); // 出栈
            queue.offer(top);
            count++;               // 统计输出顶点个数
            ListDG.EdgeNode edgeNode = new ListDG.EdgeNode();
            edgeNode = listDG.vertexNodeList[top].firstedge;
            while (edgeNode != null) {
                indegree[edgeNode.adjvex]--;        // 与此顶点连接的各顶点入度减1
                if (indegree[edgeNode.adjvex] == 0) // 判断连接顶点的入度是否为0，若为0，则入栈
                    stack.push(edgeNode.adjvex);
                edgeNode = edgeNode.next;
            }
        }

        // 判断是否存在环
        if (count != vlen) {
            System.out.print("Graph has a cycle.\n");
            return "Error";
        } else {
            System.out.print(queue);
            return "OK";
        }
    }
}
```
源代码下载：[拓扑排序](https://github.com/GongchuangSu/Data_Structures_and_Algorithms/tree/master/%E5%9B%BE%28Graph%29/%E6%8B%93%E6%89%91%E6%8E%92%E5%BA%8F)

# 时间复杂度
对于一个具有n个顶点e条弧的AOV网来说，将入度为0的顶点入栈的时间复杂度为$O(n)$，之后，每个顶点进一次栈，出一次栈，入度减1的操作共执行了e次，则整个算法的时间复杂度为$O(n+e)$。

---
layout: post
tagline: "Graph Traversal"
description: "图的遍历问题"
---

##图论的一些思考（一）

BY queati

<br>

最近用C把《算法导论》上的图论部分实现了一下，把一些感想写下来：

图论主要分为五个问题：

* 图的遍历
* 最小生成树
* 单源最短路径
* 所有节点对最短路径
* 流网络

目前主要讨论下前四个问题。

<br>

###图的遍历 Graph traverse [wikipedia](http://en.wikipedia.org/wiki/Graph_traversal)

关于图的遍历，相信大家对DFS和BFS都不会陌生，它们分别以深度优先和广度优先对节点进行访问。

<br>

**Depth First Search (DFS) [wikipedia](http://en.wikipedia.org/wiki/Depth-first_search) [code](https://github.com/queati/algorithms/blob/master/Graph/depth_first_search.c)**

使用DFS对图进行遍历的时候和走没有出口的迷宫差不多，如果能在迷宫的任何位置作上标记，那么每走到一个地方都作上一个时间标记，每次走到一个分叉口的时候，选择一个没有走过的路径，如果没有这样的路径，选择所有路径中时间标记最小的继续前进。

同样在进行DFS遍历时，如果能对每个节点标记访问开始时间和标记结束时间，如：

<br>
 
    function DFS(vertex s)
    {
    	//记录节点s访问开始时间
    	for vertex u that exist an edge from s to u
    		if u had not been visited
    			DFS(u)
    	//记录节点s访问结束时间
    }

<br>

可以从这些时间中得到图的以下信息：

* 是否**存在回路**
* 图的**拓扑排序**
 
**是否存在回路**：在遍历结束后可以对图的每一条边（s->u）进行判断，如果u的访问结束时间大于s的访问结束时间，那么图中存在回路；

**拓扑排序**：如果图中不存在回路，只要将节点按照访问结束时间非增序排序就可以得到图的拓扑排序结果。

<br>

**为什么能用节点的访问结束时间作为衡量这两种信息的指标？**

1. 如果图中**存在一条由u到v的路径，且不存在由v到u的路径**：

	此时由函数DFS(vertex)可以看出：
	* 如果先执行DFS(u)，则DFS(v)一定是在DFS(u)的执行过程或它的子过程中被调用，所以DFS(v)结束返回的时间一定比DFS(u)早；
	* 如果先执行DFS(v)，DFS(v)结束返回之前不会调用DFS(u)，此时DFS(v)的结束返回时间仍旧比DFS(u)早；
	* 由此能看出不管DFS先访问那个节点，u的访问结束时间一定比v的小；
	* 所以当图无环时节点访问结束时间可以作为拓扑排序的因素，同时也能看出节点的开始访问时间不能作为拓扑排序的因素。
	
2. 如果图中**存在由u到v的路径，且存在由v到u的路径**：

	由1中的讨论可知:
	* 如果先执行DFS(u)，则DFS(v)一定是在DFS(u)的执行过程或它的子过程中被调用，所以DFS(v)结束返回的时间一定比DFS(u)早；
	* 如果先执行DFS(v)，则DFS(u)一定是在DFS(v)的执行过程或它的子过程中被调用，所以DFS(u)结束返回的时间一定比DFS(v)早；
	* 此时节点的访问时间可以作为图是有包含环路的依据；
	* 包含环路的图不存在拓扑排序

3. 如果图中**不存在由u到v的路径，且不存在由v到u的路径**：

	此时u的结束访问时间可能大于v的，也可能小于v的。
	
<br>
	
**Breadth First Search (BFS) [wikipedia](http://en.wikipedia.org/wiki/Breadth-first_search) [code](https://github.com/queati/algorithms/blob/master/Graph/breadth_first_search.c)**

使用DFS对图进行遍历时，有些类似于树从根节点开始的层次遍历，遍历过程中依次访问离source节点最小跳数为0，1，2，……的节点。

如果不考虑边的权重，那么DFS可以用来寻找单源最短路径（跳数）。

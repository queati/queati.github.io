---
layout: post
tagline: "minimum spanning tree"
description: "图的最小生成树"
---

##图论的一些思考（二）

BY queati

<br>

###最小生成树 Minimum Spanning Tree [wikipedia](http://en.wikipedia.org/wiki/Minimum_spanning_tree)

<br>


> Given a connected, undirected graph, a spanning tree of that graph is a subgraph that is a tree and connects all the vertices together.
> 
>  A minimum spanning tree (MST) or minimum weight spanning tree is then a spanning tree with weight less than or equal to the weight of every other spanning tree. More generally, any undirected graph (not necessarily connected) has a minimum spanning forest, which is a union of minimum spanning trees for its connected components.

<br>

历史上第一个查找最小生成树的算法由捷克科学家Otakar Boruvka在1926年提出, Boruvka算法最早被提出用来解决摩拉维亚市电力网络部署。

####Boruvka's Algorithm [wikipedia](http://en.wikipedia.org/wiki/Bor%C5%AFvka%27s_algorithm) [code](https://github.com/queati/algorithms/blob/master/Graph/boruvka.c)

> Boruvka's algorithm is an algorithm for finding a minimum spanning tree in a graph for which all edge weights are distinct.
> 
> The algorithm begins by first examining each vertex and adding the cheapest edge from that vertex to another in the graph, without regard to already added edges, and continues joining these groupings in a like manner until a tree spanning all vertices is completed.

Brouvka算法每次去找一个分量到其它分量的最小权重边，然后将此边加入到森林T中，最后对于所有的边，如果两个端点属于不同的分量，合并它们。

	Boruvka(T)
	初始化森林T为一系列包含单个节点的树（每个节点看成一个单独的分量）
	while T中分量数 > 1 {
		对于T中的每个分量C，找到由分量C到任意一个不同于C的分量C'的最短边
		选择这条边。
		合并有边相连的分量。
	}
	
在一轮迭代前，若T中包含n个分量，对于每个分量都需要选择一条边，情况最坏能选择n/2条不同的边，情况最好时选择n-1条不同的边（直接找出最后的MST）。所以迭代的次数为O(log|V|)次，对于每次迭代，需要判断所有的边，所以该算法的时间复杂度为O(|E|log|V|)。

**边的权值为什么需要不同？**
考虑如下情况，分量C和分量C‘间存在两条最小权重的边e和e’，那么可能出现一轮迭代中同时选中e和e‘两条边的情况，从而形成了环，不符合MST的定义。

<br>

####Kruskal's Algorithm [wikipedia](http://en.wikipedia.org/wiki/Kruskal%27s_algorithm) [code](https://github.com/queati/algorithms/blob/master/Graph/kruskal.c)

> Kruskal's algorithm is a greedy algorithm in graph theory that finds a minimum spanning tree for a connected weighted graph. 

Kruskal算法是一种基于贪心的MST查找算法，维护一个森林T，T属于一棵MST的边的子集，每次找出一条边e，使得T+e仍属于一棵MST的边的子集。

	Kruskal(G)
	T = empty set
	将所有边按照权值升序排序
	for e contains in E {
		if e + T 不包含回路
			T += e
	}
	

<br>

####Reverse-delete Algorithm [wikipedia](http://en.wikipedia.org/wiki/Reverse-delete_algorithm) [code](https://github.com/queati/algorithms/blob/master/Graph/reverse_delete.c)

> The Reverse-delete algorithm is an algorithm in graph theory used to obtain a minimum spanning tree from a given connected, edge-weighted graph.
 
 Reverse-delete算法和Kruskal算法的思路非常相似，可以看成Kruskal的逆过程，它先讲节点按照降序进行排序，然后再按照这个顺序判断能否删除边。可以理解为每次删除大的边，而Kruskal可以理解成每次选择小的边。
 
 	Reverse-delete(G)
 	T = 所有边组成的森林
 	将所有边按照权值降序排序
 	for e contains in T {
 		if 删除e后在T中e的两个端点能连通
 			T -= e
 	}
 	
<br>
 	
####Prim's Algorithm [wikipedia](http://en.wikipedia.org/wiki/Prim%27s_algorithm) [code](https://github.com/queati/algorithms/blob/master/Graph/prim.c)

> Prim's algorithm is a greedy algorithm that finds a minimum spanning tree for a connected weighted undirected graph. 

	Prim(G)
	V = empty set
	while()


<br>


####贪心算法与拟阵

不难看出，上面四种算法均使用了贪心的思想。
在Boruvka，Kruskal和Prim算法中，通过每次生长MST的一条或多条边，直至生成最后的MST。

	GENERIC-MST(G, w)
	A = empty set
	while A 不是一棵MST
		在图中找到一条相对集合A安全的边e(u,v)
		A += (u,v)
		
这三种算法的不同之处就在于如何寻找安全边。

对于Reverse-delete算法，它其实是上述过程的反过程，集合A初始时包含所有的边，然后从中每次删除一条边，直至生成最后的MST。

<br>

####Boruvka，Kruskal与Reverse-delete

这三种算法的思想非常类似：

Boruvka算法不需要对所有边进行排序，每一轮迭代需要对所有边进行判断，然后从中选取多条边；

Kruskal算法需要对所有边进行排序，每一轮迭代只需要对一条边进行判断，由于它需要判断这条边的两个端点之间是否存在一条路径，所以它需要使用不相交集合数据结构来实现这种判断，最后确实是否选取这条边；

Reverse-delete可以看成时Kruskal算法的逆过程。

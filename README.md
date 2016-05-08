# Algorithms / Assignment 1

---

**鲍一心 3014216002 计算机科学与技术学院 2014 级 1 班**

***

#### 1 Weighted Staged Directed Graph

​	1) 

| g[s] |            g[i]             |
| :--: | :-------------------------: |
| = 0  | = min(g[j] + d(j, i), g[i]) |

​	2) The C++ code is as follows:

```c++
//
//  shortestPath.cpp
//
//
//  Created by Bao Yixin on 5/1/16.
//  Copyright © 2016 TwTStudio. All rights reserved.
//

#include <iostream>
using namespace std;
#define INF 2147483647


struct adjVertexNode {
    int adjVertexPos;
    int weight;
    adjVertexNode *next;
};

struct vertexNode {
    int name;
    int distance;
    bool visited;
    vertexNode *path;
    adjVertexNode *list;
};

struct graph {
    struct vertexNode vertexNode[30];
    int numberOfVertices;
    int numberOfEdges;
};

//初始化图
void initGraph (graph &g) {
    int headOfEdge, tailOfEdge, weightOfEdge;
    adjVertexNode* adjNode;
    //输入顶点数和边数
    cout << "Please input numberOfVertices and numberOfEdges" <<endl;
    cin >> g.numberOfVertices >> g.numberOfEdges;
    //输入顶点名字
    cout << "Please input the names of the vertices:" <<endl;
    for (int i=0; i<g.numberOfVertices; ++i)
    {
        cin >> g.vertexNode[i].name;
        g.vertexNode[i].list=NULL;
    }
    //输入每条边的信息: 起点与终点, 以及权值
    cout << "Please input the head, tail and weight of each edge:" <<endl;
    for (int j=0; j<g.numberOfEdges; ++j)
    {
        cin >>headOfEdge >>tailOfEdge >> weightOfEdge;
        adjNode = new adjVertexNode;
        adjNode->weight = weightOfEdge;
        adjNode->adjVertexPos = tailOfEdge-1;
        adjNode->next=g.vertexNode[headOfEdge-1].list;
        g.vertexNode[headOfEdge-1].list=adjNode;
    }
}

//销毁图
void deconstructGraph (graph &g) {
    for (int i=0; i<g.numberOfVertices; ++i)
    {
        adjVertexNode* tempNode=NULL;
        while(g.vertexNode[i].list!=NULL)
        {
            tempNode = g.vertexNode[i].list;
            g.vertexNode[i].list = g.vertexNode[i].list->next;
            delete tempNode;
        }
    }
}

//找到最近点
struct vertexNode* findNearestVertex(graph &g)
{
    int nearest = INF;
    vertexNode* sp = NULL;

    for (int i=0; i<g.numberOfVertices; ++i)
    {
        if (!g.vertexNode[i].visited && g.vertexNode[i].distance < nearest)
        {
            nearest = g.vertexNode[i].distance;
            sp = &(g.vertexNode[i]);
        }
    }
    return sp;
}

void Dijkstra(graph& g, vertexNode &node) {
    int i;
    for (i = 0; i < g.numberOfVertices; ++i) {
        g.vertexNode[i].visited = false;
        g.vertexNode[i].distance = INF;
        g.vertexNode[i].path = NULL;
    }
    node.distance = 0;
    for (i = 0; i < g.numberOfVertices; ++i) {
        vertexNode *v = findNearestVertex(g);
        if (v == NULL)
            break;
        v->visited = true;
        adjVertexNode *head = v->list;
        while (head != NULL) {
            vertexNode *w = &g.vertexNode[head->adjVertexPos];
            if (!(w->visited)) {
                if (v->distance + head->weight < w->distance) {
                    w->distance = v->distance + head->weight;
                    w->path = v;
                }
            }
            head = head->next;
        }
    }
}

void getPath(graph &g, vertexNode *startNode, vertexNode *endNode) {
    if (startNode != endNode && endNode->path == NULL) {
        cout << "There is no shortest path from " << startNode->name << " to " << endNode->name << endl;
    }
    else {
        if (endNode->path != NULL) {
            getPath(g, startNode, endNode->path);
            cout << " ";
        }
        cout << endNode->name;
    }
}

int main() {
    int nameOfStartNode, nameOfEndNode;
    graph g;
    initGraph(g);
    cout << "Please input the name of the startNode and endNode:" << endl;
    cin >> nameOfStartNode >> nameOfEndNode;
    vertexNode& startNode = g.vertexNode[nameOfStartNode-1];
    vertexNode& endNode = g.vertexNode[nameOfEndNode-1];
    Dijkstra(g, startNode);
    cout << "The shortest path from node" << startNode.name << " to node" << endNode.name << " is:" << endl;
    getPath(g, &startNode, &endNode);
    deconstructGraph(g);
  	return 0;
}
```





#### 2 Knapsack problem

​	1) The Optimal SubStructure is: 

| step |                induction                 |
| :--: | :--------------------------------------: |
|  a   | Suppose that (x<sub>1</sub>, x<sub>2</sub>, x<sub>3</sub>,  …, x<sub>n</sub>) is the optimal solution to the original problem P<sub>0<sub> |
|  b   | (y<sub>1</sub>, y<sub>2</sub>, y<sub>3</sub>, …, y<sub>n</sub>) is the optimal solution to its subproblem P' with item 1 |
|  c   | We know then that (y<sub>1</sub>, y<sub>2</sub>, y<sub>3</sub>, …, y<sub>n-1</sub>, x<sub>n</sub>) is no worse than (x<sub>1</sub>, x<sub>2</sub>, x<sub>3</sub>,  …, x<sub>n</sub>) |

​	2)

**First of all we define that**:

​	The optimal value >> f(i, y, z);

​	f(n, 0, 0) = 0;

​	if (w<sub>1n</sub> < c<sub>1</sub> && w<sub>2n</sub> < c<sub>2</sub>) {

​		f(n, w<sub>1n</sub>, w<sub>2n</sub>) = p<sub>n</sub>;

​	} else {

​		f(n, w<sub>1n</sub>, w<sub>2n</sub>) = 0;

​	}

**and** f(1, c<sub>1</sub>, c<sub>2</sub>) is our goal

**Then**

​	if (w<sub>1i</sub> > y || w<sub>2i</sub> > z) {

​		//item i cannot be included

​		f(i, y, z) = f(i+1, y, z);

​	}

​	if (w<sub>1i</sub> <= y && w<sub>2i</sub> <= z) {

​		//item i <u>might</u> be included

​		case 0 // included

​			f(i, y, z) = f(i+1, y-w<sub>1i</sub>, z-w<sub>2i</sub>) + p<sub>i</sub>;

​		case 1 // not included

​			f(i, y, z) = f(i+1, y, z);

​	}

**Final step**

​	return max of f(i, y, z);

​	

#### 3 Matrix Chain Multiplication

1) The Optimal Order of the Matrix Chain Multiplication is:

$$

\underline{(\underline{(M_1, M_2)}, \underline{(\underline{(M_3, M_4)}, \underline{(M_5, M_6)})})}

$$
2) 

|   **／**   |                induction                 |
| :-------: | :--------------------------------------: |
|  c(i, i)  | need 0 reference to other table entries  |
| c(i, i+1) | 1 way to divide it into 2 parts, therefore we need 2 references to other table entries, and because the number of  c(i, i+1) is (n-1), the total number of references we need is 2(n-1) |
|    ...    |                    …                     |
| c(i, i+k) | k ways to divide it into 2 parts, therefore we need 2k references to other table entries, and because the number of c(i, i+k) is (n-k), the total number of references we need is 2k(n-1) |

Therefore the total number of references we need adds up to:
$$
Sum = {{2\sum\limits_{i=1}^{n-1}}i(n-i) = 2(ni-i^2)=2(\frac{(n-1)n}{2}-\frac{(2n-1)(n-1)n}{6})=\frac{n^3-n}{3}}
$$



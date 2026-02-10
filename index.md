
# P1073 [NOIP 2009 提高组] 最优贸易

## 题目背景

本题原题数据极弱，Subtask 0 中的测试点为原题测试点，Subtask 1 中的测试点为 Hack 数据。

## 题目描述

在一个有向图中，找一条从 $1$ 到 $n$ 的路径，并在这条路径上选出两个先后经过的点 $i$ 和 $j$，使得 $price[j] - price[i]$ 最大。

## 输入格式

第一行包含 $2$ 个正整数 $n$ 和 $m$，中间用一个空格隔开，分别表示城市的数目和道路的数目。

第二行 $n$ 个正整数，每两个整数之间用一个空格隔开，按标号顺序分别表示这 $n$ 个城市的商品价格。

接下来 $m$ 行，每行有 $3$ 个正整数 $x,y,z$，每两个整数之间用一个空格隔开。如果 $z=1$，表示这条道路是城市 $x$ 到城市 $y$ 的单向道路；如果 $z=2$，表示这条道路为城市 $x$ 和城市 $y$ 之间的双向道路。

## 输出格式

一个整数，表示最多能赚取的旅费。如果没有进行贸易，则输出 $0$。

## 输入输出样例 #1

### 输入 #1

```
5 5 
4 3 5 6 1 
1 2 1 
1 4 1 
2 3 2 
3 5 1 
4 5 2 
```

### 输出 #1

```
5
```

## 说明/提示

【数据范围】

输入数据保证 $1$ 号城市可以到达 $n$ 号城市。

对于 $10\%$ 的数据，$1\leq n\leq 6$。

对于 $30\%$ 的数据，$1\leq n\leq 100$。

对于 $50\%$ 的数据，不存在一条旅游路线，可以从一个城市出发，再回到这个城市。

对于 $100\%$ 的数据，$1\leq n\leq 100000$，$1\leq m\leq 500000$，$1\leq  x,y\leq  n$，$1\leq  z\leq  2$，$1\leq $ 各城市的编号 $\leq  n$。

水晶球价格 $\leq 100$。

NOIP 2009 提高组 第三题




1498

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
//typedef long long ll;
const int N=2e4+5;
struct node{
	int v,w;	
};
int m,n,dis[N][2],ans;
bool vis[N];
vector<node>g[N];
void spfa(int st)
{
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	dis[st][0]=0;
	queue<int>q;
	q.push(st);
	vis[st]=1;
	while(!q.empty())
	{
		int u=q.front();
		q.pop();
		vis[u]=0;
		for(auto it:g[u])
		{
			int v=it.v;
			int w=it.w;
			if(dis[v][0]>dis[u][0]+w)
			{
				dis[v][1]=dis[v][0];
				dis[v][0]=dis[u][0]+w;
				if(!vis[v])
					vis[v]=1,q.push(v);
			}
			if(dis[v][1]>dis[u][0]+w&&dis[u][0]+w>dis[v][0])
			{
				dis[v][1]=dis[u][0]+w;
				if(!vis[v])
					vis[v]=1,q.push(v);
			}
			if(dis[v][1]>dis[u][1]+w)
			{
				dis[v][1]=dis[u][1]+w;
				if(!vis[v])
					vis[v]=1,q.push(v);
			}
		}
	}
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=m;i++)
	{
		int u,v,w;
		cin>>u>>v>>w;
		g[u].push_back({v,w});
		g[v].push_back({u,w});
	}
	spfa(1);
	cout<<dis[n][1]; 
	return 0;
}
```

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

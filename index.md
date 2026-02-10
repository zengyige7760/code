1497：农场派对
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
//typedef long long ll;
const int N=2e4+5;
struct node{
	int v,w;	
};
int m,n,x,dis1[N],dis2[N],ans;
bool vis[N];
vector<node>g1[N];
vector<node>g2[N];
void spfa1(int st)
{
	memset(dis1,0x3f,sizeof(dis1));
	memset(vis,0,sizeof(vis));
	dis1[st]=0;
	queue<int>q;
	q.push(st);
	vis[st]=1;
	while(!q.empty())
	{
		int u=q.front();
		q.pop();
		vis[u]=0;
		for(auto it:g1[u])
		{
			int v=it.v;
			int w=it.w;
			if(dis1[v]>dis1[u]+w)
			{
				dis1[v]=dis1[u]+w;
				if(!vis[v])
				{
					q.push(v);
					vis[v]=1;
				}
			}
		}
	}
}
void spfa2(int st)
{
	memset(dis2,0x3f,sizeof(dis2));
	memset(vis,0,sizeof(vis));
	dis2[st]=0;
	queue<int>q;
	q.push(st);
	vis[st]=1;
	while(!q.empty())
	{
		int u=q.front();
		q.pop();
		vis[u]=0;
		for(auto it:g2[u])
		{
			int v=it.v;
			int w=it.w;
			if(dis2[v]>dis2[u]+w)
			{
				dis2[v]=dis2[u]+w;
				if(!vis[v])
				{
					q.push(v);
					vis[v]=1;
				}
			}
		}
	}
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m>>x;
	for(int i=1;i<=m;i++)
	{
		int u,v,w;
		cin>>u>>v>>w;
		g1[u].push_back({v,w});
		g2[v].push_back({u,w});
	}
	spfa1(x);
	spfa2(x);
	for(int i=1;i<=n;i++)
	{
		ans=max(ans,dis1[i]+dis2[i]);
	}
	cout<<ans;
	return 0;
}
```

1496
```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
//typedef long long ll;
struct node
{
	int v,w;	
};
const int N=1e4+5;
int m,n,k,dis[N],a[N],b[N],c[N];
bool vis[N];
bool check(int mid)
{
	vector<node>g[N];
	for(int i=1;i<=m;i++)
	{
		if(c[i]<=mid)
		{
			g[a[i]].push_back({b[i],0});
			g[b[i]].push_back({a[i],0});
		}
		else
		{
			g[a[i]].push_back({b[i],1});
			g[b[i]].push_back({a[i],1});
		}
	}
	memset(dis,0x3f,sizeof(dis));
	memset(vis,0,sizeof(vis));
	dis[1]=0;
	queue<int>q;
	q.push(1);
	vis[1]=1;
	while(!q.empty())
	{
		int u=q.front();
		q.pop();
		vis[u]=0;
		for(auto it:g[u])
		{
			int v=it.v;
			int w=it.w;
			if(dis[v]>dis[u]+w)
			{
				dis[v]=dis[u]+w;
				if(!vis[v])
				{
					q.push(v);
					vis[v]=1;
				}
			}
		}
	}
	return dis[n]<=k;
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m>>k;
	for(int i=1;i<=m;i++)
	{
		cin>>a[i]>>b[i]>>c[i];
	}
	int l=0,r=1e9;
	while(l<r)
	{
		int mid=(l+r)/2;
		if(check(mid))
		{
			r=mid;
		}
		else
		{
			l=mid+1; 
		}
	}
	if(l==1000000000)
		l=-1;
	cout<<l;
	return 0;
}
```

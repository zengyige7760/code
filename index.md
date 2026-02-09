1552：【例 1】点的距离


```cpp


#include<bits/stdc++.h>
using namespace std;
//#define int long long
//typedef long long ll;
const int N=5e5+10;
inline int read() {int x = 0, f = 1;char ch = getchar();while(ch < '0' || ch > '9') {if(ch == '-') f = -1;ch = getchar();}while(ch >= '0' && ch <= '9') {x = x * 10 + ch - '0';ch = getchar();}return x * f;}
int n,m,s,dep[N],fa[N][21];
vector<int>g[N];
void init(int u,int father)
{
	dep[u]=dep[father]+1;
	fa[u][0]=father;
	for(int i=1;i<=20;i++)
	{
		fa[u][i]=fa[fa[u][i-1]][i-1];	
	}
	for(auto v:g[u])
	{
		if(v!=father)
		{
			init(v,u);
		}
	}

}
int lca(int u,int v)
{
	if(dep[u]<dep[v])
		swap(u,v);
	for(int i=20;i>=0;i--)
	{
		if(dep[fa[u][i]]>=dep[v])
			u=fa[u][i];
		if(u==v)
			return u;
	}
	for(int i=20;i>=0;i--)
	{
		if(fa[u][i]!=fa[v][i])
		{
			u=fa[u][i];
			v=fa[v][i];
		}
	}
	return fa[u][0];
}
int dis(int u,int v)
{
	return dep[u]+dep[v]-2*dep[lca(u,v)];
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n;
	for(int i=1;i<=n-1;i++)
	{
		int u,v;
		cin>>u>>v;
		g[u].push_back(v);
		g[v].push_back(u);
	}
	cin>>m;
	init(1,0);
	for(int i=1;i<=m;i++)
	{
		int u,v;
		cin>>u>>v;
		cout<<dis(u,v)<<'\n';
	}
	return 0;
}

```

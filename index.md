
1515：网络协议

```cpp
#include<bits/stdc++.h>
using namespace std;
//#define int long long
//typedef long long ll;
const int N=5e4+10;
int n,dfn[N],low[N],co[N],num,col,out[N],de[N],ans1,d0,o0;
stack<int>st;
vector<int>g[N];
void Tarjan(int u)
{
	dfn[u]=low[u]=++num;
	st.push(u);
	for(auto v:g[u]){
		if(!dfn[v]){
			Tarjan(v);
			low[u]=min(low[u],low[v]);
		}
		else{
			if(!co[v])
				low[u]=min(low[u],dfn[v]);
		}
	}
	if(low[u]==dfn[u]){
		co[u]=++col;
		while(st.top()!=u){
			co[st.top()]=col;
			st.pop();
		}
		st.pop();
	}
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		int x;
		while(cin>>x&&x!=0)
		{
			g[i].push_back(x);
		}
	}
	for(int i=1;i<=n;i++)
	{
		if(!dfn[i])
		{
			Tarjan(i);
		}
	}
	for(int u=1;u<=n;u++)
	{
		for(auto v:g[u])
		{
			if(co[u]!=co[v])
			{
				de[co[v]]++;
				out[co[u]]++;
			}
		}
	}
	for(int i=1;i<=col;i++)
	{
		if(!de[i])
		{
			ans1++;
			d0++;
		}
		if(!out[i])
		{
			o0++;
		}
	}
	cout<<ans1<<'\n'<<(col==1?0:max(d0,o0));
    return 0;
}
```


1518


```cpp

#include<bits/stdc++.h>
using namespace std;
#define int long long
//typedef long long ll;
const int N=5e5+10;
int s,p,jiu[N],n,m,out[N],money[N],dfn[N],low[N],co[N],num,col,si[N],de[N],sum[N],sum_money[N],ans;
stack<int>st;
vector<int>old[N];
vector<int>g[N];
void Tarjan(int u)
{
	dfn[u]=low[u]=++num;
	st.push(u);
	for(auto v:old[u])
	{
		if(!dfn[v])
		{
			Tarjan(v);
			low[u]=min(low[u],low[v]);
		}
		else
		{
			if(!co[v])
			{
				low[u]=min(low[u],dfn[v]);
			}
		}
	}
	if(low[u]==dfn[u])
	{
		co[u]=++col;
		sum_money[col]+=money[u];
		while(st.top()!=u)
		{
			sum_money[col]+=money[st.top()];
			co[st.top()]=col;
			st.pop();
		}
		st.pop();
	}
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=m;i++)
	{
		int x,y;
		cin>>x>>y;
		old[x].push_back(y);
	}
	for(int i=1;i<=n;i++)
	{
		cin>>money[i];
	}
	cin>>s>>p;
	for(int i=1;i<=n;i++)
	{
		if(!dfn[i])
		{
			Tarjan(i);
		}
	}
	vector<pair<int,int> >edge;
	for(int u=1;u<=n;u++)
	{
		for(auto v:old[u])
		{
			if(co[u]!=co[v])
			{
				edge.push_back({co[u],co[v]});
			}
		}
	}
	sort(edge.begin(),edge.end());
	edge.erase(unique(edge.begin(),edge.end()),edge.end());
	for(auto i:edge)
	{
		g[i.first].push_back(i.second);
        de[i.second]++;
	}
	for(int i=1;i<=p;i++)
	{
		int x;
		cin>>x;
		jiu[co[x]]=1;
	}
	queue<int>q;
	int st=co[s];
	memset(sum,-1,sizeof(sum));
	sum[st]=sum_money[st];
	for(int i=1;i<=col;i++)
	{
		if(!de[i])
		{
			q.push(i);
		}
	}
	while(!q.empty())
	{
		int u=q.front();
		q.pop();
		for(auto v:g[u])
		{
			if(sum[u]!=-1)
				sum[v]=max(sum[v],sum[u]+sum_money[v]);
			de[v]--;
			if(!de[v])
				q.push(v); 
		}
	}
	for(int i=1;i<=col;i++)
	{
		if(jiu[i]==1)
			ans=max(ans,sum[i]);
	}
	cout<<ans;
    return 0;
}

```



1519


```cpp

#include<bits/stdc++.h>
using namespace std;
//#define int long long
//typedef long long ll;
const int N=1e5+10;
int partner(int x)
{
	return x%2==0?x-1:x+1;
}
int n,m,dfn[N],low[N],co[N],num,col,de[N];
stack<int>st;
vector<int>g[N];
void Tarjan(int u)
{
	dfn[u]=low[u]=++num;
	st.push(u);
	for(auto v:g[u]){
		if(!dfn[v]){
			Tarjan(v);
			low[u]=min(low[u],low[v]);
		}
		else{
			if(!co[v])
				low[u]=min(low[u],dfn[v]);
		}
	}
	if(low[u]==dfn[u]){
		co[u]=++col;
		while(st.top()!=u){
			co[st.top()]=col;
			st.pop();
		}
		st.pop();
	}
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=m;i++)
	{
		int u,v;
		cin>>u>>v;
		g[u].push_back(partner(v));
		g[v].push_back(partner(u));
	}
	for(int i=1;i<=2*n;i++)
	{
		if(!dfn[i])
		{
			Tarjan(i);
		}
	}
	for(int i=1;i<=2*n;i+=2)
	{
		if(co[i]==co[i+1])
		{
			cout<<"NIE";
			return 0;
		}
	}
	for(int i=1;i<=2*n;i+=2)
	{
		if(co[i]<co[i+1])
		{
			cout<<i<<'\n';
		}
		else
		{
			cout<<i+1<<'\n';
		}
	}
    return 0;
}

```

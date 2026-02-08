
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

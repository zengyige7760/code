**LCA**


P4180 [BJWC2010] 严格次小生成树


### 第一步：构建基准（求最小生成树）

我们要找“次小”，必须先知道“最小”是谁。

1. 使用 **Kruskal 算法**，把所有边按权值从小到大排序。
2. 利用并查集，依次挑选不形成环的边，直到连通所有点。
3. **关键动作**：
* 记录最小生成树的总权值 `sum`。
* **标记**哪些边是“树边”，哪些是“非树边”。
* 用选出的树边建图（用你习惯的 `vector<Node> g[N]`）。



---

### 第二步：建立“跳跃表”（倍增预处理）

由于我们要频繁查询树上两点路径间的最值，直接暴力找会超时，所以要用**倍增**。

1. 跑一遍 **DFS**，确定每个点的深度 `dep[u]` 和它的父节点 `fa[u][0]`。
2. 在跳跃的同时，维护两个关键信息：
* `g1[u][i]`：节点  向上跳  步路程中的**最大边权**。
* `g2[u][i]`：这段路程中的**严格次大边权**。


3. **合并逻辑**（这是老师最常考的点）：
* 最大值就是两段最大值里取大的。
* 次大值：如果两段最大值相等，就取两段次大值里大的；如果最大值不等，就取“较小的那个最大值”和“两个次大值”这三者中的最大值。



---

### 第三步：路径切片查询（寻找替换目标）

对于任何一条**非树边** ，权重为 ，它一旦加入就会形成环。我们要从  到  的原路径中踢走一根边。

1. 利用类似 LCA 的过程，把  和  提升到同一高度，再一起跳向公共祖先。
2. 在跳跃的过程中，不断收集路径上的 `g1` 和 `g2`，最终汇总出整条路径上的**全局最大值 ** 和**全局严格次大值 **。

---

### 第四步：严格次小的决策（分情况讨论）

拿到路径最值后，我们要根据新边  来做判断，确保结果“严格”大于最小生成树：

1. **情况一**：如果 。
* 我们踢掉最长的那根边 。
* 这时候总权值的增加量 。


2. **情况二**：如果 。
* 踢掉  后总权值没变，不符合“严格”。
* 所以我们要踢掉**严格次大**的那根边 。
* 这时候总权值的增加量 。



---

### 第五步：汇总答案

1. 遍历**所有**非树边，对每一条边都执行第三步和第四步。
2. 在所有算出的增加量  中，找到那个**最小的 **。
3. **最终答案** = `sum + Δ_min`。

```cpp

#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
const int N = 1e5 + 10;
const int M = 3e5 + 10;
const ll INF = 1e18; // 严格次小可能很大，用 long long 的极大值


struct Edge {
    int u, v, w;
    bool is_tree; // 标记是否在最小生成树中
    bool operator<(const Edge &o) const { return w < o.w; }
} e[M];

struct Node {
    int v, w;
};
vector<Node> g[N]; // 你的 vector 存图风格

int n, m, dep[N], fa[N][21], p[N];
ll g1[N][21], g2[N][21]; // g1[u][i] 记录 u 到 2^i 祖先路径上的最大权值，g2 记录严格次大权值

// 并查集找祖先
int find(int x) { return p[x] == x ? x : p[x] = find(p[x]); }

// 核心：合并两个区间的最大和严格次大值
// 我们有四个待选值（两个区间的最大和次大），从中选出全局最大和严格小于全局最大的次大
void update_vals(ll &m1, ll &m2, ll v1, ll v2) {
    if (v1 > m1) {
        m2 = max(m1, v2); // 原来的最大值退居二位，或者取新区间的次大
        m1 = v1;          // 更新最大值
    } else if (v1 < m1) {
        m2 = max(m2, v1); // v1 小于最大值，尝试更新次大值
    } else {
        m2 = max(m2, v2); // v1 等于最大值，只能尝试用次大值 v2 更新
    }
}

// 你的 init 风格，增加了权值倍增的维护
void init(int u, int father, int w) {
    dep[u] = dep[father] + 1;
    fa[u][0] = father;
    g1[u][0] = w;
    g2[u][0] = -INF; // 初始次大值设为负无穷
    
    for(int i = 1; i <= 20; i++) {
        fa[u][i] = fa[fa[u][i-1]][i-1];
        // 关键：当前区间 [u, 2^i] 是由 [u, 2^(i-1)] 和 [fa, 2^(i-1)] 拼接而成
        g1[u][i] = g1[u][i-1];
        g2[u][i] = g2[u][i-1];
        update_vals(g1[u][i], g2[u][i], g1[fa[u][i-1]][i-1], g2[fa[u][i-1]][i-1]);
    }
    
    for(auto it : g[u]) {
        if(it.v != father) init(it.v, u, it.w);
    }
}

// 查询路径 u->v 上的最大和严格次大权值
pair<ll, ll> query(int u, int v) {
    ll m1 = -INF, m2 = -INF;
    if(dep[u] < dep[v]) swap(u, v);
    
    // 先跳到同一高度，顺便更新最大/次大值
    for(int i = 20; i >= 0; i--) {
        if(dep[fa[u][i]] >= dep[v]) {
            update_vals(m1, m2, g1[u][i], g2[u][i]);
            u = fa[u][i];
        }
    }
    if(u == v) return {m1, m2};
    
    // 一起向上跳，跳到 LCA 的下一层
    for(int i = 20; i >= 0; i--) {
        if(fa[u][i] != fa[v][i]) {
            update_vals(m1, m2, g1[u][i], g2[u][i]);
            update_vals(m1, m2, g1[v][i], g2[v][i]);
            u = fa[u][i];
            v = fa[v][i];
        }
    }
    // 最后补上到 LCA 的最后两根边
    update_vals(m1, m2, g1[u][0], g2[u][0]);
    update_vals(m1, m2, g1[v][0], g2[v][0]);
    return {m1, m2};
}

int main() {
    n = read(); m = read();
    for(int i = 1; i <= m; i++) {
        e[i].u = read(); e[i].v = read(); e[i].w = read();
    }
    
    // 1. Kruskal 算法求最小生成树 (MST)
    sort(e + 1, e + m + 1);
    for(int i = 1; i <= n; i++) p[i] = i;

    ll mst_sum = 0;
    int cnt = 0;
    for(int i = 1; i <= m; i++) {
        int fu = find(e[i].u), fv = find(e[i].v);
        if(fu != fv) {
            p[fu] = fv;
            mst_sum += e[i].w;
            e[i].is_tree = true; // 标记树边
            g[e[i].u].push_back({e[i].v, e[i].w});
            g[e[i].v].push_back({e[i].u, e[i].w});
            if(++cnt == n - 1) break;
        }
    }

    // 2. 预处理倍增信息，根节点为 1
    init(1, 0, -INF);

    // 3. 枚举所有非树边，尝试替换
    ll min_delta = INF; // 记录最小的 (w - 路径边权)
    for(int i = 1; i <= m; i++) {
        if(e[i].is_tree) continue; // 跳过树边
        
        pair<ll, ll> res = query(e[i].u, e[i].v);
        
        // 严格次小的逻辑：
        // 如果非树边权 w 大于路径最大边权 m1，则 delta = w - m1
        if(e[i].w > res.first) {
            min_delta = min(min_delta, (ll)e[i].w - res.first);
        } 
        // 如果 w 等于 m1，则必须替换路径上的严格次大边权 m2
        else if(res.second != -INF) {
            min_delta = min(min_delta, (ll)e[i].w - res.second);
        }
    }

    // 输出最终答案：MST 的和 + 最小增量
    printf("%lld\n", mst_sum + min_delta);
    
    return 0;
}
```
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


1553：【例 2】暗的连锁

```cpp

#include<bits/stdc++.h>
using namespace std;
//#define int long long
//typedef long long ll;
const int N=2e5+10;
int n,m,dep[N],fa[N][21],cha[N],ans;
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
int dfs(int u,int father)
{
	int res=cha[u];
	for(auto v:g[u])
	{
		if(v==father)
			continue;
		int cnt=dfs(v,u);
		if(cnt==0)
			ans+=m;
		else if(cnt==1)
			ans++;
		res+=cnt;
	}
	return res;
} 
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<n;i++)
	{
		int u,v;
		cin>>u>>v;
		g[u].push_back(v);
		g[v].push_back(u);
	}
	init(1,0);
	for(int i=1;i<=m;i++)
	{
		int u,v;
		cin>>u>>v;
		cha[u]++;
		cha[v]++;
		cha[lca(u,v)]-=2;
	}
	dfs(1,0);
	cout<<ans;
	return 0;
}
```


1556：Dis

```cpp

#include<bits/stdc++.h>
using namespace std;
#define int long long
//typedef long long ll;
struct node{
	int v,w;
};
const int N=5e5+10;
int n,m,s,dep[N],fa[N][21],len[N];
vector<node>g[N];
void init(int u,int father)
{
	dep[u]=dep[father]+1;
	fa[u][0]=father;
	for(int i=1;i<=20;i++)
	{
		fa[u][i]=fa[fa[u][i-1]][i-1];	
	}
	for(auto it:g[u])
	{
		int v=it.v;
		int w=it.w;
		if(v!=father)
		{
			len[v]=len[u]+w;
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
	return len[u]+len[v]-2*len[lca(u,v)];
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n>>m;
	for(int i=1;i<=n-1;i++)
	{
		int u,v,w;
		cin>>u>>v>>w;
		g[u].push_back({v,w});
		g[v].push_back({u,w});
	}
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

1557：祖孙询问

```cpp

#include<bits/stdc++.h>
using namespace std;
//#define int long long
//typedef long long ll;
const int N=5e5+10;
inline int read() {int x = 0, f = 1;char ch = getchar();while(ch < '0' || ch > '9') {if(ch == '-') f = -1;ch = getchar();}while(ch >= '0' && ch <= '9') {x = x * 10 + ch - '0';ch = getchar();}return x * f;}
int n,m,s,dep[N],fa[N][21],root;
vector<int>g[N];
void dfs(int u,int father)
{
	dep[u]=dep[father]+1;
	fa[u][0]=father;
	for(int i=1;i<=19;i++)
	{
		fa[u][i]=fa[fa[u][i-1]][i-1];	
	}
	for(auto v:g[u])
	{
		if(v!=father)
		{
			dfs(v,u);
		}
	}

}
int lca(int u,int v)
{
	int f=2;
	if(dep[u]<dep[v])
		swap(u,v),f=1;
	for(int i=19;i>=0;i--)
	{
		if(dep[fa[u][i]]>=dep[v])
			u=fa[u][i];
	}
	if(u==v)
	{
		return f;
	}
	else
	{
		return 0;
	}
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		int u,v;
		cin>>u>>v;
		if(v==-1)
			root=u;
		else
		{
			g[u].push_back(v);
			g[v].push_back(u);
		}
	}
	cin>>m;
	dfs(root,0);
	for(int i=1;i<=m;i++)
	{
		int u,v;
		cin>>u>>v;
		cout<<lca(u,v)<<'\n';
	}
	return 0;
}

```

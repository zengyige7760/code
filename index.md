P5175 数列

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
//typedef long long ll;
const int N=1e2+10;
int T,n,a1,a2,x,y;
const int mod=1e9+7;
struct g
{
	int a[5][5];
	g() { memset(a, 0, sizeof(a)); }
};
g cheng(g a,g b)
{
	g sum;
	for(int i=1;i<=4;i++)
	{
		for(int j=1;j<=4;j++)
		{
			int temp=0;
			for(int k=1;k<=4;k++)
			{
				temp+=a.a[i][k]*b.a[k][j];
				if(temp>mod)
					temp%=mod;
			}
			sum.a[i][j]=temp;
		}
	}
	return sum;
}
g qpow(g a,int b)
{
	g ans;
	ans.a[1][1]=a1*a1%mod;
	ans.a[1][2]=a2*a2%mod;
	ans.a[1][3]=a1*a1%mod;
	ans.a[1][4]=a1*a2%mod;
	while(b)
	{
		if(b%2==1)
		{
			ans=cheng(ans,a);		
		}
		a=cheng(a,a);
		b/=2;
	}
	return ans;
}
signed main()
{
    ios::sync_with_stdio(false),cin.tie(0),cout.tie(0);
	cin>>T;
	while(T--)
	{
		cin>>n>>a1>>a2>>x>>y;
		g base;
		base.a[1][1]=base.a[2][1]=base.a[2][3]=1;
		base.a[2][2]=x*x%mod;
		base.a[2][4]=x;
		base.a[3][2]=y*y%mod;
		base.a[4][2]=2*x*y%mod;
		base.a[4][4]=y;
		cout<<qpow(base,n-1).a[1][1]<<'\n'; 
	}
    return 0;
}
```

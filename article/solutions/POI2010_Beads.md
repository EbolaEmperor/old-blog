[返回首页](https://EbolaEmperor.github.io)
# 【POI2010】Beads 题解

### 题解

·这题显然可以哈希乱搞

·正反做两遍哈希，然后暴力枚举k，对于每个k，算出所有子串的哈希值，丢进map里面，然后比较map的大小

·我们知道，k是从1~n枚举的，而对于每个k，有n/k个子串，所以时间复杂度是O((n+n/2+n/3+…+1)*log n)，根据调和级数可以知道，n+n/2+n/3+…+1=n log n，因此时间复杂度是O(n log² n)，是可以卡着过去的

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef unsigned long long ULL;
ULL a[200010],n;
ULL p[200010];
ULL hl[200010],hr[200010];

ULL getl(int l,int r){return hl[r]-hl[l-1]*p[r-l+1];}
ULL getr(int l,int r){return hr[l]-hr[r+1]*p[r-l+1];}

map<ULL,bool> f;
int ans[200010];

int main()
{
	scanf("%lld",&n);
	for(int i=1;i<=n;i++) scanf("%lld",a+i);
	p[0]=1;for(int i=1;i<=n;i++) p[i]=p[i-1]*n;
	for(int i=1;i<=n;i++) hl[i]=hl[i-1]*n+a[i];
	for(int i=n;i>=1;i--) hr[i]=hr[i+1]*n+a[i];
	int mx=0,cnt=0;
	for(int i=1;i<=n;i++)
	{
		f.clear();
		for(int j=1;j+i-1<=n;j+=i)
		{
			ULL l=getl(j,j+i-1);
			ULL r=getr(j,j+i-1);
			f[l*r]=1;
		}
		int sum=f.size();
		if(sum==mx) ans[++cnt]=i;
		if(sum>mx) mx=sum,ans[cnt=1]=i;
	}
	printf("%d %d\n",mx,cnt);
	for(int i=1;i<=cnt;i++) printf("%d ",ans[i]);
	return 0;
}
```
[返回首页](https://EbolaEmperor.github.io)
[返回专题](https://EbolaEmperor.github.io/special/Mobius)

# 【HAOI2011】Problem b 题解

先去看一下这题：[【POI2007】Zap](http://www.ebola.pro/article/solutions/poi2007_zap)

然后记那一题的答案为Ans(a,b,d)，那么根据容斥原理，这一题的答案就是Ans(b,d,k)-Ans(a-1,d,k)-Ans(b,c-1,k)+Ans(a-1,c-1,k)

能不用long long就别用long long ，听说用long long的被卡得很惨，我除了答案之外用的全是int，快到飞起

```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long LL;
const int N=50000;
int prime[N],tot=0;
int u[N+10];
bool mark[N+10];

void Init()
{
	u[1]=1;
	for(int i=2;i<=N;i++)
	{
		if(!mark[i]) prime[++tot]=i,u[i]=-1;
		for(int j=1;j<=tot&&i*prime[j]<=N;j++)
		{
			mark[i*prime[j]]=1;
			if(i%prime[j]) u[i*prime[j]]=-u[i];
			else{u[i*prime[j]]=0;break;}
		}
	}
	for(int i=1;i<=N;i++) u[i]+=u[i-1];
}

LL solve(int n,int m)
{
	if(n>m) swap(n,m);
	int div;LL ans=0;
	for(int i=1;i<=n;i=div+1)
	{
		div=min(n/(n/i),m/(m/i));
		ans+=1ll*(u[div]-u[i-1])*(n/i)*(m/i);
	}
	return ans;
}

int main()
{
	Init();
	int n,a,b,c,d,k;
	scanf("%d",&n);
	while(n--)
	{
		scanf("%d%d%d%d%d",&a,&b,&c,&d,&k);
		a=(a-1)/k;b/=k;c=(c-1)/k;d/=k;
		printf("%lld\n",solve(b,d)-solve(a,d)-solve(b,c)+solve(a,c));
	}
	return 0;
}
```

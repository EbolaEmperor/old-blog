# 中国剩余定理  学习笔记

### 背景介绍

今有士兵若干，三三数之余一，五五数之余二，七七数之余三，问士兵几何？

这是著名的韩信点兵问题。古人用它们的智慧得到了解决这类问题的口诀：

```
三人同行七十稀
五树梅花廿一枝
七子团圆月正半
除百零五便得知
```

这句口诀的意思是，设士兵有x人，那么x对3的余数乘上70，x对5的余数乘上21，x对7的余数乘上15，将上述结果相加后对105取模即为答案

套用这个口诀，可以得到答案就是(1\*70+2\*21+3\*15) mod 105 = 52

后人根据古人的研究成果，总结出了适用性更广的定理

### 提出问题

给定同余方程组，每个方程形如：x mod p = b，所有方程均满足p是质数，求解一个最小的x使得所有方程成立

### 大数翻倍法

有一个很好理解的方法就是，选最大模数的那个方程，假设这个方程是第k个，然后设x=p[k]+b[k]，将x带入其它方程检验，成功则输出x，否则让x加上p[k]再检验，重复这一过程

这个方法的时间复杂度对数据的依赖性较强，因此无法分析。我们知道，使用复杂度无法分析的算法，无异于耍流氓。我们更知道，这个算法的复杂度一定可以被卡的很高

### 中国剩余定理

首先说一下做法，证明放在后面来说

首先设N是所有p[i]的积，然后枚举每一个方程

对于第i个方程，设M=N/p[i]，然后解一个同余方程kM=b\[i\](mod p[i])，当然我们可以把它化成不定方程的形式来用扩欧解：**k**M+**r**p[i]=b[i]。最后求出t=k\*M

将每个方程求出来的t相加，对N取模，即为答案

先贴出主要代码：

```cpp
LL China(LL *p,LL *b,int k)
{
	LL N=1,ans=0;
	for(int i=0;i<k;i++) N*=p[i];
	for(int i=0;i<k;i++)
	{
		LL x=0,y=0,M=N/p[i];
		ExGcd(M,p[i],x,y);
		if(x<0) x+=p[i];
		ans=(ans+x*b[i]*M%N)%N;
	}
	return ans;
}
```

下面是证明。~~我相信没人会看这一部分~~

其实我们处理每个方程时，都运用了“不影响他人”的思想。因为我们每个方程求出来的t，都是除自己之外所有p[i]的倍数，因此这个t对其它同余方程的贡献为0（也就是说其它任何同余方程某一边加上一个t仍然成立）。而我们求出的t，已经满足了自己这个同余方程，同时又对其它方程没影响，所以将所有t加起来，就满足了所有同余方程

因为N是所有p[i]的积，所以N对所有的同余方程贡献均为0（也就是说任何同余方程某一边加上一个N仍然成立），所以最小的答案一定小于N，因此最后对N取模就是答案
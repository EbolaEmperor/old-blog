[返回首页](https://EbolaEmperor.github.io)
# 【JSOI2012】玄武密码 题解

### 题解

·首先将所有“玄武密码”建出AC自动机

·然后拿文字段在AC自动机上匹配一遍，每匹配到一个位置就打上一个访问标记，同时沿fail指针走，把沿途经过的结点都打上访问标记，因为显然fai指针指向的位置可以匹配

·然后从“玄武密码”的词尾结点往上走，第一个有标记的结点就是最长匹配前缀的结尾位置

```cpp
#include<bits/stdc++.h>
#define N 10001000
using namespace std;
 
int ch[N][4],fa[N],fail[N];
bool tag[N];
int word[100010];
int sz=0;
char s[N],s2[110];
int length[100010];
 
int get(char c)
{
	if(c=='E') return 0;
	if(c=='W') return 1;
	if(c=='N') return 2;
	if(c=='S') return 3;
}
 
void insert(char *s,int x)
{
	int p=0,len=strlen(s);
	for(int i=0;i<len;i++)
	{
		int j=get(s[i]);
		if(!ch[p][j])
		{
			ch[p][j]=++sz;
			fa[sz]=p;
		}
		p=ch[p][j];
	}
	word[x]=p;
}
 
void getfail()
{
	queue<int> q;
	q.push(0);
	while(!q.empty())
	{
		int u=q.front();
		for(int i=0;i<4;i++)
			if(ch[u][i])
			{
				int v=ch[u][i];
				if(u) fail[v]=ch[fail[u]][i];
				q.push(v);
			}
			else ch[u][i]=ch[fail[u]][i];
		q.pop();
	}
}
 
void serch(char *s)
{
	int p=0,len=strlen(s);
	for(int i=0;i<len;i++)
	{
		int j=get(s[i]);
		while(p&&(!ch[p][j])) p=fail[p];
		if(ch[p][j])
		{
			p=ch[p][j];
			for(int u=p;u;u=fail[u]) tag[u]=1;
		}
	}
}
 
int main()
{
	int n,m;
	memset(tag,0,sizeof(tag));
	scanf("%d%d",&n,&m);
	scanf("%s",s);
	for(int i=1;i<=m;i++)
	{
		scanf("%s",s2);
		length[i]=strlen(s2);
		insert(s2,i);
	}
	getfail();
	serch(s);
	for(int i=1;i<=m;i++)
	{
		int p=word[i],ans=length[i];
		while(p)
		{
			if(tag[p]) break;
			p=fa[p];ans--;
		}
		printf("%d\n",ans);
	}
	return 0;
}
```
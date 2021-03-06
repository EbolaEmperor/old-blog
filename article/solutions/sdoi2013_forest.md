[返回首页](https://EbolaEmperor.github.io)
# 【SDOI2013】森林 题解

[我是题目链接](https://www.luogu.org/problemnew/show/P3302)

### 题解

·因为最近在做启发式合并的专题，所以最近博客画风都是各种鬼畜合并，这题是主席树的启发式合并

·这道毒瘤题呢，用到了并查集、倍增、基本图论知识、主席树这些东西

·首先深搜，以当前结点的父节点作为上一个历史版本建立主席树

·深搜同时维护倍增关系

·对于一组询问，我们求出LCA，我们知道主席树就是“前缀可持久化权值线段树”，因此它的信息具有前缀性，于是u到v的路径上的信息，就等于以u为根的线段树的信息+以v为根的线段树的信息-以LCA为根的线段树的信息-以LCA的父亲为根的线段树的信息

·利用前缀的性质进行kth就好了

·而对于连边这个鬼畜操作，我们发现，当一条边连了上去，意味着本来的某个根节点变成了另一棵树的子树结点，这样它的前缀关系就发生了改变。哦不，原来那个根，以及它的所有子节点，前缀关系都发生了改变！然而对于这种不愉快的事情，我们也不得不用一个不愉快的方式解决——暴力重构

·当然，为了使时间复杂度降下来，我们选择把小的树合并到大的树里面，然后重构小的树里面的主席树

·细节参考代码

```cpp
#include<bits/stdc++.h>
#define newnode(x) x=new Node,x->lson=x->rson=null
#define MAXN 80000
#define INF 1000000000
using namespace std;

int w[MAXN+10];
struct Node
{
	Node *lson,*rson;
	int sum;
	Node(){sum=0;lson=rson=NULL;}
};
Node *root[MAXN+10],*null;
void insert(Node* &o,Node* pre,int l,int r,int x)
{
	newnode(o);
	o->sum=pre->sum+1;
	if(l==r) return;
	int mid=(l+r)/2;
	if(x<=mid) o->rson=pre->rson,insert(o->lson,pre->lson,l,mid,x);
	else o->lson=pre->lson,insert(o->rson,pre->rson,mid+1,r,x);
}
int kth(Node* p1,Node* p2,Node* f1,Node* f2,int l,int r,int k)
{
	if(l==r) return l;
	int sz=(p1->lson->sum)+(p2->lson->sum)-(f1->lson->sum)-(f2->lson->sum);
	int mid=(l+r)/2;
	return (k<=sz)?kth(p1->lson,p2->lson,f1->lson,f2->lson,l,mid,k):kth(p1->rson,p2->rson,f1->rson,f2->rson,mid+1,r,k-sz);
}

int fa[MAXN+10],sz[MAXN+10];
int find(int x){return x==fa[x]?x:fa[x]=find(fa[x]);}
void merge(int x,int y){int fx=find(x),fy=find(y);fa[fx]=fy;sz[fy]+=sz[fx];}

int mul[MAXN+10][20],dep[MAXN+10];
int LCA(int p1,int p2)
{
	if(dep[p1]<dep[p2]) swap(p1,p2);
	for(int j=17;j>=0;j--)
		if(dep[mul[p1][j]]>=dep[p2]) p1=mul[p1][j];
	for(int j=17;j>=0;j--)
		if(mul[p1][j]!=mul[p2][j]) p1=mul[p1][j],p2=mul[p2][j];
	if(p1!=p2) p1=mul[p1][0];
	return p1;
}

struct Edge{int to,next;} e[2*MAXN+10];
int h[MAXN+10],tot=0;
void add_edge(int u,int v){tot++;e[tot].to=v;e[tot].next=h[u];h[u]=tot;}
void dfs(int u,int la)
{
	for(int j=1;j<=17;j++) mul[u][j]=mul[mul[u][j-1]][j-1];
	dep[u]=dep[la]+1;
	insert(root[u],root[la],0,INF,w[u]);
	for(int tmp=h[u];tmp;tmp=e[tmp].next)
	{
		if(e[tmp].to==la) continue;
		mul[e[tmp].to][0]=u;
		dfs(e[tmp].to,u);
	}
}

int main()
{
	null=new Node;
	null->lson=null->rson=null;
	root[0]=null;
	int n,m,t,u,v,fuck,lastans=0,k;
	cin>>fuck>>n>>m>>t;
	for(int i=1;i<=n;i++) scanf("%d",w+i);
	for(int i=1;i<=n;i++) fa[i]=i,sz[i]=1;
	for(int i=1;i<=m;i++)
	{
		scanf("%d%d",&u,&v);
		add_edge(u,v);
		add_edge(v,u);
		merge(u,v);
	}
	for(int i=1;i<=n;i++)
		if(!mul[i][0]) dfs(i,0);
	for(int i=1;i<=t;i++)
	{
		char opt=getchar();
		while(opt!='Q'&&opt!='L') opt=getchar();
		if(opt=='Q')
		{
			scanf("%d%d%d",&u,&v,&k);
			u^=lastans;v^=lastans;k^=lastans;
			int lca=LCA(u,v);
			lastans=kth(root[u],root[v],root[lca],root[mul[lca][0]],0,INF,k);
			printf("%d\n",lastans);
		}
		if(opt=='L')
		{
			scanf("%d%d",&u,&v);
			u^=lastans;v^=lastans;
			int fu=find(u),fv=find(v);
			if(sz[fu]>sz[fv]) swap(u,v);
			add_edge(u,v);
			add_edge(v,u);
			merge(u,v);
			mul[u][0]=v;
			dfs(u,v);
		}
	}
	return 0;
}
```
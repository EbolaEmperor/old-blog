# A. 杨柳

### 题意简述

有n个棋子和n个红格子，你需要移动棋子，使得最后所有的棋子都位于红格子中。要求不能走到有障碍的格子中

移动规则：若棋子位于(x,y)，可以移动至(x-a,y-b)、(x-a,y+b)、(x-b,y-a)、(x-b,y+a)、(x+a,y-b)、(x+a,y+b)、(x+b,y-a)、(x+b,y+a)，其中a和b给定

求达成目标的最小移动步数

棋盘大小100\*100，棋子数量500

### 题解

显然的最小费用最大流

源点向棋子连边，容量1费用0；红格子向汇点连边，容量1费用0；能互相到达的一对非障碍点之间连双向边，容量inf费用1

然后直接跑费用流即可

然后你发现你T飞了，于是大骂出题人毒瘤，居然要用zkw费用流才能A

```cpp
#include<bits/stdc++.h>
using namespace std;

const int N=1000010;
const int INF=0x3f3f3f3f;
struct Edge{int to,next,flow,cost;} e[N];
int h[N],sum=-1,s,t;
int r,c,n,a,b,ans=0;
int idx[110][110],dfc=0;
char mp[110][110];
int dis[N],cur[N];
bool inq[N];

void add_edge(int u,int v,int w,int c)
{
    e[++sum]={v,h[u],w,c};h[u]=sum;
    e[++sum]={u,h[v],0,-c};h[v]=sum;
}

bool spfa()
{
    memset(dis,0x3f,sizeof(dis));
    queue<int> q;q.push(s);
    inq[s]=1;dis[s]=0;
    while(!q.empty())
    {
        int u=q.front();
        for(int tmp=h[u];~tmp;tmp=e[tmp].next)
        {
            int v=e[tmp].to;
            if(e[tmp].flow>0&&dis[u]+e[tmp].cost<dis[v])
            {
                dis[v]=dis[u]+e[tmp].cost;
                if(inq[v]) continue;
                inq[v]=1;q.push(v);
            }
        }
        q.pop();inq[u]=0;
    }
    return dis[t]<INF;
}

int dfs(int u,int a)
{
    if(u==t||a==0) return a;
    int flow=0;inq[u]=1;
    for(int &tmp=cur[u];~tmp;tmp=e[tmp].next)
    {
        int v=e[tmp].to;
        if(inq[v]) continue;
        if(dis[u]+e[tmp].cost==dis[v])
        {
            int f=dfs(v,min(a,e[tmp].flow));
            if(!f) continue;
            e[tmp].flow-=f;
            e[tmp^1].flow+=f;
            a-=f;flow+=f;
            ans+=f*e[tmp].cost;
            if(a==0) break;
        }
    }
    inq[u]=0;
    return flow;
}

void adde(int u,int x,int y)
{
    if(x<1||x>r||y<1||y>c) return;
    if(mp[x][y]=='*') return;
    int v=idx[x][y];
    add_edge(u,v,INF,1);
    add_edge(v,u,INF,1);
}

int main()
{
    memset(h,-1,sizeof(h));
    scanf("%d%d%d%d%d",&r,&c,&n,&a,&b);
    for(int i=1;i<=r;i++) scanf("%s",mp[i]+1);
    for(int i=1;i<=r;i++)
        for(int j=1;j<=c;j++)
            idx[i][j]=++dfc;
    for(int i=1;i<=r;i++)
        for(int j=1;j<=c;j++)
        {
            if(mp[i][j]=='*') continue;
            adde(idx[i][j],i-a,j-b);
            adde(idx[i][j],i-a,j+b);
            adde(idx[i][j],i-b,j-a);
            adde(idx[i][j],i-b,j+a);
        }
    s=0;t=r*c+1;
    for(int i=1,x,y;i<=n;i++)
    {
        scanf("%d%d",&x,&y);
        add_edge(s,idx[x][y],1,0);
    }
    for(int i=1,x,y;i<=n;i++)
    {
        scanf("%d%d",&x,&y);
        add_edge(idx[x][y],t,1,0);
    }
    int flow=0;
    while(spfa())
    {
        memcpy(cur,h,sizeof(h));
        flow+=dfs(s,INF);
    }
    printf("%d\n",flow==n?ans:-1);
    return 0;
}
```
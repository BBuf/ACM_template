# prime算法

与**克鲁斯卡尔算法**不同的是,**普里姆算法(prim)**用了另一种是思路解决这个问题，我们要尽量少的选取边使得花费的权值最小，那么最终肯定是要包含所有的点，那么我们的思路就可以变成：把顶点分成两类：**树顶点(已经选入生成树的点)**和**非树顶点(还没有被选入生成树的点)**,从任意一个点开始选择，找出这个点连接的所有的边，然后找出最短的，选中这条边加入到生成树中，枚举每一个树顶点到每一个非树顶点的所有的边，然后找最短的边加入到生成树，一直加边**n-1次**,直到所有的顶点都被加入到生成树中。

在实现的过程中，枚举树顶点和非树顶点的边的时候，利用一个**dis[]数组来记录各个点到生成树的最短距离**,然后找到dis的值最小的点加入到生成树,然后通过这个点更新一下其他点到生成树的dis的值。

[HDU1233 还是畅通工程(最小生成树模板题，Prime,kruskal算法)](http://blog.csdn.net/riba2534/article/details/60318424)

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a,b) memset(a,b,sizeof(a))
#define inf 0x3f3f3f3f
const int N=100+10;
const int M=10000+20;
int n,m;
int vis[N],e[N][N],dis[N];
void init()
{
    for(int i=1; i<=n; i++)
        for(int j=1; j<=n; j++)
            e[i][j]=(i==j)?0:inf;
}
void prim()
{
    for(int i=1; i<=n; i++)
    {
        dis[i]=e[1][i];
        vis[i]=0;
    }
    vis[1]=1;
    int sum=0;
    for(int i=1; i<=n-1; i++)
    {
        int minn=inf,k=1;
        for(int j=1; j<=n; j++)
        {
            if(!vis[j]&&dis[j]<minn)
            {
                minn=dis[j];
                k=j;
            }
        }
        vis[k]=1;
        sum+=dis[k];
        for(int j=1; j<=n; j++)
        {
            if(!vis[j]&&e[k][j]<dis[j])
                dis[j]=e[k][j];
        }
    }
    printf("%d\n",sum);
}
int main()
{
    int u,v,w;
    while(~scanf("%d",&n)&&n)
    {
        m=(n*n-n)/2;
        init();
        for(int i=1; i<=m; i++)
        {
            scanf("%d%d%d",&u,&v,&w);
            e[u][v]=e[v][u]=w;
        }
        prim();
    }
    return 0;
}

```

## prim算法的堆优化

用邻接表+优先队列可以实现对**prim算法**的优化

prim的思想还是没有改变，还是从任意一个点开始选择，找出这个点连接的所有的边，然后找出最短的，选中这条边加入到生成树中，枚举每一个树顶点到每一个费数顶点的所有的边，然后找最短的边加入到生成树，一直加边**n-1次**,直到所有的顶点都被加入到生成树中

在实现的时候，我们先选取随便一个点，然后用一个pair类型保存当前的点的dis值和当前的点的编号,然后让dis值比较小的先出队，然后取队首，当当前点没有被访问过时，记录cnt和sum,然后遍历这个点连接的所有边，进行松弛操作.

[P3366 【模板】最小生成树](https://www.luogu.org/problemnew/show/P3366)

```cpp
#define mem(a,b) memset(a,b,sizeof(a))
typedef long long ll;
typedef pair<int,int> pir;
const int N=5000+10;
const int M=200000+10;
int first[N],tot;
int vis[N],dis[N],n,m;
priority_queue <pir,vector<pir>,greater<pir> >q;
struct edge
{
    int v,w,next;
} e[M*2];
void add_edge(int u,int v,int w)
{
    e[tot].v=v;
    e[tot].w=w;
    e[tot].next=first[u];
    first[u]=tot++;
}
void init()
{
    mem(first,-1);
    tot=0;
    mem(dis,127);
}
void prim()
{
    int cnt=0,sum=0;
    dis[1]=0;
    q.push(make_pair(0,1));
    while(!q.empty()&&cnt<n)
    {
        int d=q.top().first,u=q.top().second;
        q.pop();
        if(!vis[u])
        {
            cnt++;
            sum+=d;
            vis[u]=1;
            for(int i=first[u]; ~i; i=e[i].next)
                if(e[i].w<dis[e[i].v])
                {
                    dis[e[i].v]=e[i].w;
                    q.push(make_pair(dis[e[i].v],e[i].v));
                }
        }
    }
    if(cnt==n) printf("%d\n",sum);
    else puts("orz");
}
int main()
{
    int u,v,w;
    init();
    scanf("%d%d",&n,&m);
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        add_edge(u,v,w);
        add_edge(v,u,w);
    }
    prim();
    return 0;
}

```










# Dijkstra算法

一种单源最短路径算法，已知顶点和边，然后可以求出图中一点和另一点之间的最短距离。思想是:利用一个dis[i]数组来记录，从顶点1到其他各个点的最短距离，刚开始的时候全部初始化为INF，从顶点1开始找他的出边里面最小的，然后把这个顶点标记，然后松弛dis数组，之后找出这个点的所有出边里面最小的，然后标记，更新，一直到最后,时间复杂度是O(n^2)

和prim算法的思想差不多，都是松弛，找到第一个点，然后进行对于dis的松弛，一般有**邻接矩阵**和**邻接表**两种写法

邻接表写法：

```cpp
#define mem(a,b) memset(a,b,sizeof(a))
#define inf 0x3f3f3f3f
typedef long long ll;
typedef pair<int,int> pir;
const int N=1e5+10;
const int M=1e6+10;
int n,m;
int first[N],tot;
int dis[N],vis[N];
struct edge
{
    int v,w,next;
} e[M];
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
}
void dijkstra(int st)
{
    for(int i=1; i<=n; i++)
    {
        dis[i]=inf;
        vis[i]=0;
    }
    dis[st]=0;
    for(int i=1; i<=n; i++)
    {
        int minn=inf,k=0;
        for(int j=1; j<=n; j++)
            if(!vis[j]&&dis[j]<minn)
            {
                minn=dis[j];
                k=j;
            }
        vis[k]=1;
        for(int j=first[k]; ~j; j=e[j].next)
            if(!vis[e[j].v]&&dis[k]+e[j].w<dis[e[j].v])
                dis[e[j].v]=dis[k]+e[j].w;
    }
}
int main()
{
    int st,ed,u,v,w;
    scanf("%d%d%d%d",&n,&m,&st,&ed);
    init();
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        add_edge(u,v,w);
        add_edge(v,u,w);
    }
    dijkstra(st);
    printf("%d\n",dis[ed]);
    return 0;
}
```

堆优化的迪杰斯特拉:

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a,b) memset(a,b,sizeof(a))
#define inf 2147483647
const int N=10000+20;
const int M=500000+20;
int n,m;
int first[N],tot,dis[N],vis[N];
struct edge
{
    int u,v,w,next;
} e[M];
void add_edge(int u,int v,int w)
{
    e[tot].v=v,e[tot].w=w;
    e[tot].next=first[u];
    first[u]=tot++;
}
struct node
{
    int id,now;
    node() {}
    node(int _id,int _now)
    {
        id=_id;
        now=_now;
    }
    bool friend operator < (node a,node b)
    {
        return a.now>b.now;
    }
};
void dijkstra(int st)
{
    for(int i=1; i<=n; i++)
    {
        dis[i]=inf;
        vis[i]=0;
    }
    dis[st]=0;
    priority_queue<node>q;
    q.push(node(st,0));
    while(!q.empty())
    {
        node u=q.top();
        q.pop();
        if(!vis[u.id])
        {
            vis[u.id]=1;
            for(int i=first[u.id]; ~i; i=e[i].next)
            {
                int v=e[i].v,w=e[i].w;
                if(dis[u.id]+w<dis[v])
                {
                    dis[v]=dis[u.id]+w;
                    q.push(node(v,dis[v]));
                }
            }
        }
    }
}
void init()
{
    mem(first,-1);
    tot=0;
}
int main()
{
    int u,v,w,st;
    init();
    scanf("%d%d%d",&n,&m,&st);
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        add_edge(u,v,w);
    }
    dijkstra(st);
    for(int i=1; i<=n; i++)
        printf("%d ",dis[i]);
    puts("");
    return 0;
}
```



邻接矩阵写法:

```cpp
const int inf=0x3f3f3f3f;  
int map[1010][1010];//map[i][j]表示从i-->j的距离    
int dis[1010];//dis[i]从v1到i的距离    
int vis[1010];//标记有没有被访问过    
void dijkstra(int n)  
{  
    int k,min;  
    for(int i=1; i<=n; i++)  
    {  
        dis[i]=map[1][i];  
        vis[i]=0;  
    }  
    for(int i=1; i<=n; i++)//遍历顶点    
    {  
        k=0;  
        min=inf;  
        for(int j=1; j<=n; j++)  
            if(vis[j]==0&&dis[j]<min)  
            {  
                min=dis[j];  
                k=j;  
            }  
        vis[k]=1;  
        for(int j=1; j<=n; j++)  
            if(vis[j]==0&&dis[k]+map[k][j]<dis[j])  
                dis[j]=dis[k]+map[k][j];//如果找到了通路就加上   
    }  
    return;  
}  
int main()  
{  
    int t,n,a,b,w;  
    while(~scanf("%d%d",&t,&n))  
    {  
        mem(map,0);  
        mem(vis,0);  
        mem(dis,0);  
        for(int i=1; i<=n; i++)  
            for(int j=1; j<=n; j++)  
                map[i][j]=inf;//初始化为无穷大    
        for(int i=1; i<=t; i++)  
        {  
            scanf("%d%d%d",&a,&b,&w);  
            if(w<map[a][b])  
            {  
                map[a][b]=w;  
                map[b][a]=map[a][b];//建立无向图  
            }//这里是判断是否有重边，应为两点之间的路，未必只有一条。  
        }  
        dijkstra(n);  
        printf("%d\n",dis[n]);  
    }  
    return 0;  
}  
```

分层最短路:

分层图最短路的模板题，此题洛谷上卡`SPFA`，所以用堆优化的`dijkstra`来做.

题意就是，给出你一张无向图，你有`k`次机会使得某一条边的花费为`0`，求最小花费.

我们需要利用类似动态规划的思想来进行转移。

我们定义:

- dis[i] [j]：从起点`st`到`i`点，使用了`j`次优惠机会所使用的最小花费.
- vis[i] [j]：从起点`st`到`i`点，使用了`j`次优惠机会这个状态有没有被标记

我们需要对普通的迪杰斯特拉转移的时候进行一些改变：

1. 不使用免费机会的时候：

   ```cpp
   如果 到点u的花费dis[u][k]+从u到v的边权w<到v的边权dis[v][k]
   	正常转移更新:dis[v][k]=dis[u][k]+w
   	加入堆q.push(node(v,k,dis[v][k]))
   ```

2. 使用免费机会的时候:

   ```cpp
   如果 到点u的时候使用了k次机会的花费dis[u][k]<到v点的时候使用了k+1次机会的边权dis[v][k+1]
   	这条边免费更新dis[v][k+1]=dis[u][k]
   	加入堆q.push(node(v,k+1,dis[v+1][k]))
   ```

剩下的就是迪杰斯特拉的堆优化模板了。

[BZOJ2763飞行路线(分层最短路，dijkstra的堆优化)](https://blog.csdn.net/riba2534/article/details/82353768)

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a, b) memset(a, b, sizeof(a))
const int N = 1e5 + 10;
const int inf = 0x3f3f3f3f;
int n, m, k;
int first[N], tot;

struct edge
{
    int v, w, next;
} e[N * 2];
void add_edge(int u, int v, int w)
{
    e[tot].v = v, e[tot].w = w;
    e[tot].next = first[u];
    first[u] = tot++;
}
struct node
{
    int id, now, k;
    node() {}
    node(int _id, int _k, int _now)
    {
        id = _id, now = _now, k = _k;
    }
    bool friend operator<(node a, node b)
    {
        return a.now > b.now;
    }
};
/*
dis[i][j]:表示从st到i点用了j次免费机会的最小花费
vis[i][j]:表示从st到i点用了j次免费机会有没有被标记过
*/
int dis[N][12], vis[N][12];
void dijkstra(int st)
{
    for (int i = 1; i <= n; i++)
    {
        for (int j = 0; j <= k; j++)
        {
            dis[i][j] = inf;
            vis[i][j] = 0;
        }
    }
    dis[st][0] = 0;
    priority_queue<node> q;
    q.push(node(st, 0, 0));
    while (!q.empty())
    {
        node u = q.top();
        q.pop();
        if (!vis[u.id][u.k])
        {
            vis[u.id][u.k] = 1;
            for (int i = first[u.id]; ~i; i = e[i].next)
            {
                int v = e[i].v, w = e[i].w;
                if (!vis[v][u.k] && dis[u.id][u.k] + w < dis[v][u.k])
                {
                    dis[v][u.k] = dis[u.id][u.k] + w;
                    q.push(node(v, u.k, dis[v][u.k]));
                }
                if (u.k < k && !vis[v][u.k + 1] && dis[u.id][u.k] < dis[v][u.k + 1])
                {
                    dis[v][u.k + 1] = dis[u.id][u.k];
                    q.push(node(v, u.k + 1, dis[v][u.k + 1]));
                }
            }
        }
    }
}
void init()
{
    mem(first, -1);
    tot = 0;
}
int main()
{
    //freopen("in.txt", "r", stdin);
    int u, v, w, st, ed;
    scanf("%d%d%d%d%d", &n, &m, &k, &st, &ed);
    st++, ed++;
    init();
    for (int i = 1; i <= m; i++)
    {
        scanf("%d%d%d", &u, &v, &w);
        u++, v++;
        add_edge(u, v, w);
        add_edge(v, u, w);
    }
    dijkstra(st);
    int ans = inf;
    for (int i = 0; i <= k; i++)
        ans = min(ans, dis[ed][i]);
    printf("%d\n", ans);
    return 0;
}
```
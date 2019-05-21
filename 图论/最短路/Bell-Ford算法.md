# Bell-Ford算法以及队列优化SPFA

贝尔曼福特算法可以用来解决负权边，主要思想是进行n-1次边的松弛操作，使达到最优状态，时间复杂度是：**O(MN)**

核心代码:

```cpp
for(int k=1; k<=n-1; k++)
    for(int i=1; i<=m; i++)
        if(dis[u[i]]+w[i]<dis[v[i]])
            dis[v[i]]=dis[u[i]]+w[i];
```

-   还可以用来检测一个图是否含有负权回路，如果一个图在经过了n-1轮松弛之后，还存在：


```cpp
if(dis[v[i]]>dis[u[i]]+w[i])
    dis[v[i]]=dis[u[i]]+w[i]
```

**那么这个图必然存在负权回路**

-   如果在算法循环中发现松弛了一轮以后，图没有发生变化，那么我们可以提前跳出循环
-   松弛操作的本质是不断寻找当前状态与目标状态间的矛盾并调整，直到 找不到矛盾时即达到优状态

队列优化SPFA

利用一个队列，先让起点进入队列，然后用一个vis数组来标记一个点是否在队列中，当队列不为空时，每次取队首，然后遍历这个顶点所连接的所有的边，当可以松弛的时候进行松弛，然后判断当前点是否在队列中，如果不在那就标记一下入队，通常用邻接表来实现.判断负环的方法是:**如果某个点进入队列的次数超过n次，那么这个图一定有负环**

[最短路径·三：SPFA算法](https://hihocoder.com/problemset/problem/1093)

用了我自己封装的SPFA类

```cpp
const int N=1e5+10;
const int M=1e6+10;
int n,m;
struct SPFA
{
    int first[N],tot,vis[N],dis[N];
    void init()
    {
        mem(first,-1);
        tot=0;
    }
    struct edge
    {
        int v,w,next;
    } e[2*M];
    void add_edge(int u,int v,int w)
    {
        e[tot].v=v,e[tot].w=w;
        e[tot].next=first[u];
        first[u]=tot++;
    }
    void spfa(int st)
    {
        for(int i=1; i<=n; i++)
        {
            dis[i]=inf;
            vis[i]=0;
        }
        dis[st]=0;
        vis[st]=1;//判断一个点是否在队列中
        queue<int>q;
        q.push(st);
        while(!q.empty())
        {
            int u=q.front();
            q.pop();
            vis[u]=0;
            for(int i=first[u]; ~i; i=e[i].next)
            {
                int v=e[i].v,w=e[i].w;
                if(dis[u]+w<dis[v])
                {
                    dis[v]=dis[u]+w;
                    if(!vis[v])
                    {
                        vis[v]=1;
                        q.push(v);
                    }
                }
            }
        }
    }
} ac;
int main()
{
    int st,ed,u,v,w;
    scanf("%d%d%d%d",&n,&m,&st,&ed);
    ac.init();
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        ac.add_edge(u,v,w);
        ac.add_edge(v,u,w);
    }
    ac.spfa(st);
    printf("%d\n",ac.dis[ed]);
    return 0;
}
```


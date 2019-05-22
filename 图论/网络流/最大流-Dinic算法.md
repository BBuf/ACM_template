# 最大流-Dinic算法

Dinic算法

比较快的算法，适用于稀疏图，算案发复杂度$$O(V^2*E)$$

可以AC：[P3376 【模板】网络最大流](https://www.luogu.org/problemnew/show/3376)
```cpp
const int N=10000+50;
const int M=200000+50;
struct node
{
    node() {};
    node(int tv,int tw,int tnext)
    {
        v=tv,w=tw,next=tnext;
    };
    int v,w,next;
} e[M];
int first[N],vis[N],dis[N],tot;
void add_edge(int u,int v,int w)
{
    e[tot]=node(v,w,first[u]);
    first[u]=tot++;
    e[tot]=node(u,0,first[v]);
    first[v]=tot++;
}
int bfs(int s,int t)
{
    mem(vis,0);
    mem(dis,0);
    queue<int>q;
    q.push(s);
    vis[s]=1;
    while(!q.empty())
    {
        int u=q.front();
        q.pop();
        for(int i=first[u]; ~i; i=e[i].next)
        {
            if(!vis[e[i].v]&&e[i].w>0)
            {
                vis[e[i].v]=1;
                dis[e[i].v]=dis[u]+1;
                q.push(e[i].v);
            }
        }
    }
    return dis[t];
}
int dfs(int u,int t,int flow)
{
    if(u==t)return flow;
    for(int i=first[u]; ~i; i=e[i].next)
    {
        if(dis[e[i].v]==dis[u]+1&&e[i].w>0)
        {
            int dd=dfs(e[i].v,t,min(e[i].w,flow));
            if(dd)
            {
                e[i].w-=dd;
                e[i^1].w+=dd;
                return dd;
            }
        }
    }
    dis[u]=0;
    return 0;
}
int Dinic(int s,int t)
{
    int ans=0,flow;
    while(bfs(s,t))
    {
        while(flow=dfs(s,t,INF))
            ans+=flow;
    }
    return ans;
}
void init()
{
    mem(first,-1);
    tot=0;
}
int main()
{
    int n,m,s,t;
    int u,v,w;
    init();
    scanf("%d%d%d%d",&n,&m,&s,&t);
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        add_edge(u,v,w);
    }
    printf("%d\n",Dinic(s,t));
    return 0;
}
```


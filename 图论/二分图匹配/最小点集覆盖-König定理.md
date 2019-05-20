# 最小点集覆盖-König定理

[HDU1054 Strategic Game(二分图最小点集覆盖，König定理)](http://blog.csdn.net/riba2534/article/details/78554273)

```cpp
const int N=1600+20;
int e[N][N],vis[N],match[N],n,m;
vector<int>G[N];
int dfs(int u)
{
    for(int i=0; i<G[u].size(); i++)
    {
        int v=G[u][i];
        if(!vis[v])
        {
            vis[v]=1;
            if(!match[v]||dfs(match[v]))
            {
                match[v]=u;
                return 1;
            }
        }
    }
    return 0;
}
int query()
{
    mem(match,0);
    int sum=0;
    for(int i=0; i<n; i++)
    {
        mem(vis,0);
        if(dfs(i))sum++;
    }
    return sum;
}
int main()
{
    int u,v,k;
    while(~scanf("%d",&n))
    {
        mem(G,0);
        for(int cs=1; cs<=n; cs++)
        {
            scanf("%d:(%d)",&u,&k);
            for(int i=1; i<=k; i++)
            {
                scanf("%d",&v);
                G[u].push_back(v);
                G[v].push_back(u);
            }
        }
        printf("%d\n",query()/2);
    }
    return 0;
}
```


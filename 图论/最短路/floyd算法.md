# floyd算法

求多源最短路径的一种算法，缺点是时间复杂度太大，对于小数据，可以求出任意一点到任意一点的最短路径.

用了三重for循环.

[最短路径·二：Floyd算法](https://hihocoder.com/problemset/problem/1089)

```cpp
const int N=1e2+10;
const int M=1e6+10;
int n,m;
int e[N][N];
int main()
{
    int u,v,w;
    scanf("%d%d",&n,&m);
    for(int i=1; i<=n; i++)for(int j=1; j<=n; j++)
            e[i][j]=i==j?0:inf;
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        if(w<e[u][v])
            e[u][v]=e[v][u]=w;
    }
    for(int k=1; k<=n; k++)
        for(int i=1; i<=n; i++)
            for(int j=1; j<=n; j++)
                if(e[i][k]+e[k][j]<e[i][j])
                    e[i][j]=e[i][k]+e[k][j];
    for(int i=1; i<=n; i++)
    {
        for(int j=1; j<=n; j++)
            printf("%d ",e[i][j]);
        puts("");
    }
    return 0;
}
```


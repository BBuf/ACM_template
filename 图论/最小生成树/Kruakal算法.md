# Kruakal算法

首先是**最小生成树**的一般定义，一般的最小生成树模型是:有一个国家，里面有很多城市，然后给出一些这些城市相互之间的距离(保证图可以连通)和权值，然后从这些边里面挑选出一些边使得**图连通**(不一定直接可达，间接可达也可以)，其实就是将多余的边去掉，使得**花费最小而且要让图连通**.

**树的定义是:如果一个连通无向图不包含回路，那么这就是一棵树**

这个问题就是求图的最小生成树。

那么解决这个问题的关键就是：尽量少的挑选边使得花费的权值最小，且让图连通。

那么**克鲁斯卡尔**的思想是:先把给出的边按照权值排序，然后依次从权值小的向权值大的进行加边，在加边的时候利用**并查集**进行判断当前两个顶点是否连通，如果没有连通就加入进去，当加入的次数等于n-1(顶点数-1)的时候停止，当前的权值之和就是最小生成树的最小花费

例题:

[HDU1233 还是畅通工程(最小生成树模板题，Prime,kruskal算法)](http://blog.csdn.net/riba2534/article/details/60318424)

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a,b) memset(a,b,sizeof(a))
const int N=100+10;
const int M=10000+20;
int n,m;
int pre[N];
struct node
{
    int u,v,w;
} e[M];
bool cmp(node a,node b)
{
    return a.w<b.w;
}
void init()
{
    for(int i=1; i<=n; i++)
        pre[i]=i;
}
int find(int x)
{
   return x == pre[x] ? x : pre[x] = find(pre[x]);
}
int mix(int x,int y)
{
    int fx=find(x);
    int fy=find(y);
    if(fx!=fy)
    {
        pre[fy]=fx;
        return 1;
    }
    return 0;
}
int kruskal()
{
    int cnt=0,sum=0;
    for(int i=1; i<=m; i++)
    {
        if(mix(e[i].u,e[i].v))
        {
            cnt++;
            sum+=e[i].w;
        }
        if(cnt==n-1)
            break;
    }
    return sum;
}
int main()
{
    while(scanf("%d",&n)&&n)
    {
        init();
        m=(n*n-n)/2;
        for(int i=1; i<=m; i++)
            scanf("%d%d%d",&e[i].u,&e[i].v,&e[i].w);
        sort(e+1,e+m+1,cmp);
        printf("%d\n",kruskal());
    }
    return 0;
}

```

[POJ1679 The Unique MST(判断最小生成树是否唯一)](https://blog.csdn.net/riba2534/article/details/80158776)

有t组数据，有n个点和m条边，问最小生成树的个数是否唯一，如果唯一输出最小生成树的权值，不唯一输出`Not Unique!`. 先用`kruskal`求一下最小生成树，然后尝试删除最小生成树的每一条边，每次再求一下最小生成树，如果出现删去边后求出来的最小生成树的权值和已经知道的一样，那么最小生成树就不唯一

```cpp
typedef long long ll;
const int N=100+10;
const int M=N*N;
int pre[N],n,m,vis[M];
struct edge
{
    int u,v,w;
} e[M];
vector<int>v;
vector<int>p;
void init()
{
    for(int i=1; i<=n; i++)
        pre[i]=i;
}
int find(int x)
{
    return x==pre[x]?x:pre[x]=find(pre[x]);
}
int mix(int x,int y)
{
    int fx=find(x),fy=find(y);
    if(fx!=fy)
    {
        pre[fy]=fx;
        return 1;
    }
    return 0;
}
bool cmp(edge a,edge b)
{
    return a.w<b.w;
}
int kruskal(int flag)
{
    init();
    int sum=0,cnt=0;
    for(int i=1; i<=m; i++)
    {
        if(vis[i])continue;
        if(mix(e[i].u,e[i].v))
        {
            if(flag)
                v.push_back(i);
            cnt++;
            sum+=e[i].w;
        }
        if(cnt==n-1)
            break;
    }
    if(cnt==n-1)
        return sum;
    return inf;
}
int main()
{
    int t;
    scanf("%d",&t);
    while(t--)
    {
        v.clear();
        p.clear();
        mem(vis,0);
        scanf("%d%d",&n,&m);
        for(int i=1; i<=m; i++)
            scanf("%d%d%d",&e[i].u,&e[i].v,&e[i].w);
        sort(e+1,e+m+1,cmp);
        int ans=kruskal(1);
        for(int i=0; i<v.size(); i++)
        {
            vis[v[i]]=1;
            p.push_back(kruskal(0));
            vis[v[i]]=0;
        }
        sort(p.begin(),p.end());
        if(ans==p[0])
            puts("Not Unique!");
        else
            printf("%d\n",ans);
    }
    return 0;
}
```


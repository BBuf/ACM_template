# 最大流-EK算法

EK算法

用**邻接矩阵**存储**残余网络**，利用一个队列q来存放已访问未检查的点,vis[]数组来标记当前点是否被访问过，pre[]数组来记录前驱节点。

利用BFS来寻找可增广路，当存在一条可增广路时。从汇点一直往前面找，一直找到起点，在路径上找到一条**可增广量**最小的边，然后从起点到汇点，残余网络中正向沿着可增广路**增流**，反向沿着可增广路**减流**，然后不断地进行BFS，把每一次的**可增光量**累加起来，就是**最大流**

复杂度：$$O(V*E^2)$$

代码：
可以AC:[POJ1273 Drainage Ditches(网络流--最大流，EK增广路算法)](http://blog.csdn.net/riba2534/article/details/78933513)
```cpp
const int N=200+20;
int g[N][N],f[N][N],pre[N];//分别代表残余网络，实流网络，前驱数组
bool vis[N];//标记数组
int n,m;//点数和边数
bool bfs(int s,int t)
{
    mem(pre,-1);
    mem(vis,false);
    queue<int>q;
    vis[s]=true;
    q.push(s);
    while(!q.empty())
    {
        int now=q.front();
        q.pop();
        for(int i=1; i<=n; i++)
        {
            if(!vis[i]&&g[now][i]>0)
            {
                vis[i]=true;
                pre[i]=now;
                if(i==t)
                    return true;
                q.push(i);
            }
        }
    }
    return false;
}
int EK(int s,int t)
{
    int v,w,d,maxflow=0;
    while(bfs(s,t))
    {
        v=t;
        d=inf;
        while(v!=s)
        {
            w=pre[v];
            d=min(d,g[w][v]);
            v=w;
        }
        maxflow+=d;
        v=t;
        while(v!=s)
        {
            w=pre[v];
            g[w][v]-=d;
            g[v][w]+=d;
            if(f[v][w]>0)
                f[v][w]-=d;
            else
                f[w][v]+=d;
            v=w;
        }
    }
    return maxflow;
}
int main()
{
    int a,b,c;
    while(~scanf("%d%d",&m,&n))
    {
        mem(g,0);
        mem(f,0);
        for(int i=1; i<=m; i++)
        {
            scanf("%d%d%d",&a,&b,&c);
            g[a][b]+=c;
        }
        printf("%d\n",EK(1,n));
    }
    return 0;
}
```

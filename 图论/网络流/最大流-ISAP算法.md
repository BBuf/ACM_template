# 最大流-ISAP算法

SAP重贴标签算法

在这个算法中，我们用**邻接表**存储**混合网络**（正向边显示,cap,flow,反向边也显示这个）.

这个算法的思想是，我们给广搜的网络标一个高度，每一层一个高度，最后一层的高度是0，然后按照广搜的层次，到起点依次递增。然后搜索的时候直接按照标签的告诉从高到低找，这样可以加快效率，当走的点的标签高度走不动的时候，我们采取**重贴标签**的策略，令**当前节点的高度=所有邻接点高度的最小值+1**，如果没有邻接边，则令**当前节点的高度=节点数**（注意：一定要保证容量>流量）,然后不断的进行贴标签，重贴标签的过程，累积**可增广量**，最后的值就是**最大流**

我们需要进行gap优化:利用一个数组g[]来记录，当前的高度的节点有多少个，当重贴标签后发现当前高度的点只有一个那么就可以提前结束程序

算法复杂度：$$O(V^2*E)$$

下面是代码：
可以AC:[ P3376 【模板】网络最大流](https://www.luogu.org/problemnew/show/P3376)

```cpp
const int N=10000+20;
const int M=100000+20;
int top;
int h[N],pre[N],g[N];//h[i]记录每个节点的高度，pre[i]记录前驱,g[i]表示距离为i个节点数有多少个
int first[N];
struct node
{
    int v,next;
    int cap,flow;
} E[M*2];
void init()
{
    mem(first,-1);
    top=0;
}
void add_edge(int u,int v,int c)
{
    E[top].v=v;
    E[top].cap=c;
    E[top].flow=0;
    E[top].next=first[u];
    first[u]=top++;
}
void add(int u,int v,int c)
{
    add_edge(u,v,c);
    add_edge(v,u,0);
}

void set_h(int t)//标高函数,从终点向起点标高
{
    queue<int>q;
    mem(h,-1);
    mem(g,0);
    h[t]=0;
    q.push(t);
    while(!q.empty())
    {
        int v=q.front();
        q.pop();
        g[h[v]]++;//当前高度的个数+1
        for(int i=first[v]; ~i; i=E[i].next)
        {
            int u=E[i].v;
            if(h[u]==-1)//当前节点未标高
            {
                h[u]=h[v]+1;
                q.push(u);
            }
        }
    }
}

int Isap(int s,int t,int n)//isap算法
{
    set_h(t);
    int ans=0,u=s;
    int d;
    while(h[s]<n)//节点的高度小于顶点数
    {
        int i=first[u];
        if(u==s) d=inf;
        for(; ~i; i=E[i].next)
        {
            int v=E[i].v;
            if(E[i].cap>E[i].flow&&h[u]==h[v]+1)//容量大于流量且当前的高度等于要去的高度+1
            {
                u=v;
                pre[v]=i;
                d=min(d,E[i].cap-E[i].flow);//找最小增量
                if(u==t)//到达汇点
                {
                    while(u!=s)
                    {
                        int j=pre[u];//找到u的前驱
                        E[j].flow+=d;//正向流量+d
                        E[j^1].flow-=d;//反向流量-d
                        u=E[j^1].v;//向前搜索
                    }
                    ans+=d;
                    d=inf;
                }
                break;
            }
        }
        if(i==-1)//邻接边搜索完毕，无法行进
        {
            if(--g[h[u]]==0)//重要的优化，这个高度的节点只有一个,结束
                break;
            int hmin=n-1;//重贴标签的高度初始为最大
            for(int j=first[u]; ~j; j=E[j].next)
            {
                if(E[j].cap>E[j].flow)
                    hmin=min(hmin,h[E[j].v]);//取所有邻接点高度的最小值
            }
            h[u]=hmin+1;//重新标高
            g[h[u]]++;//标高后该高度的点数+1
            if(u!=s)//不是源点时，向前退一步，重新搜
                u=E[pre[u]^1].v;
        }
    }
    return ans;
}
int main()
{
    int n,m,u,v,w,s,t;
    scanf("%d%d%d%d",&n,&m,&s,&t);
    init();
    for(int i=1; i<=m; i++)
    {
        scanf("%d%d%d",&u,&v,&w);
        add(u,v,w);
    }
    printf("%d\n",Isap(s,t,n));
    return 0;
}
```
###kuangbin的SAP模板

```cpp
const int N=1000+20;
const int M=2*100000+20;
int top;
int h[N],pre[N],g[N],first[N],cur[N];//h[i]记录每个节点的高度，pre[i]记录前驱,g[i]表示距离为i个节点数有多少个
struct node
{
    int v,next,cap;
} E[M];
void init()
{
    mem(first,-1);
    top=0;
}
void add_edge(int u,int v,int c)
{
    E[top].v=v;
    E[top].cap=c;
    E[top].next=first[u];
    first[u]=top++;
    E[top].v=u;
    E[top].cap=0;
    E[top].next=first[v];
    first[v]=top++;
}
int sap(int start,int end,int nodenum)
{
    memset(h,0,sizeof(h));
    memset(g,0,sizeof(g));
    memcpy(cur,first,sizeof(first));
    int u=pre[start]=start,maxflow=0,aug=-1;
    g[0]=nodenum;
    while(h[start]<nodenum)
    {
loop:
        for(int &i=cur[u]; i!=-1; i=E[i].next)
        {
            int v=E[i].v;
            if(E[i].cap&&h[u]==h[v]+1)
            {
                if(aug==-1||aug>E[i].cap)
                    aug=E[i].cap;
                pre[v]=u;
                u=v;
                if(v==end)
                {
                    maxflow+=aug;
                    for(u=pre[u]; v!=start; v=u,u=pre[u])
                    {
                        E[cur[u]].cap-=aug;
                        E[cur[u]^1].cap+=aug;
                    }
                    aug=-1;
                }
                goto loop;
            }
        }
        int mindis=nodenum;
        for(int i=first[u]; i!=-1; i=E[i].next)
        {
            int v=E[i].v;
            if(E[i].cap&&mindis>h[v])
            {
                cur[u]=i;
                mindis=h[v];
            }
        }
        if((--g[h[u]])==0)break;
        g[h[u]=mindis+1]++;
        u=pre[u];
    }
    return maxflow;
}
```
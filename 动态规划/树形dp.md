# 树形dp

[POJ2342 Anniversary party(树形dp)](http://blog.csdn.net/riba2534/article/details/79079444)

这一题是树形dp入门题，题意是某公司要举办一次晚会，但是为了使得晚会的气氛更加活跃，每个参加晚会的人都不希望在晚会中见到他的直接上司，现在已知每个人的活跃指数和上司关系（当然不可能存在环），求邀请哪些人（多少人）来能使得晚会的总活跃指数最大。

关于树形dp的一些介绍:  [树形动态规划（树状DP）小结](http://blog.csdn.net/txl199106/article/details/45373507)

我们定义一个dp数组，把这个关系转换乘一棵树，对于树中的每个节点定义：

- dp[i][0]：不选第i个节点所能达到的最大活跃值
- dp[i][1]：选择第i个节点多能达到的最大活跃值

我们从一棵树的根节点开始进行`DFS`，会首先遍历到叶子节点，我们设当前节点的编号为`i`，它的儿子节点为`j`，满足:
```cpp
dp[i][0]=dp[i][0]+max(dp[j][0],dp[j][1])\\
dp[i][1]=dp[i][1]+dp[j][0]
```
也就是说：

1. 不选当前的节点获得的最大活跃值为，它的儿子节点选择和不选择中较大的一个。
2. 选择当前节点获得的最大活跃值为，这个节点本身的活跃值加上不选他儿子节点的活跃值(因为都不想见到上司)

```cpp
typedef long long ll;
#define inf 0x3f3f3f3f
#define mem(a,b) memset(a,b,sizeof(a))
const int N=6000+20;
int dp[N][2];
int pre[N],root;
int n;
int find(int x)
{
    return x==pre[x]?x:find(pre[x]);
}
void dfs(int i)
{
    for(int j=1; j<=n; j++)
    {
        if(pre[j]==i)
        {
            dfs(j);
            dp[i][0]+=max(dp[j][0],dp[j][1]);
            dp[i][1]+=dp[j][0];
        }
    }
}
int main()
{
    while(~scanf("%d",&n))
    {
        mem(dp,0);
        for(int i=1; i<=n; i++)
        {
            pre[i]=i;
            scanf("%d",&dp[i][1]);
        }
        int u,v;
        while(scanf("%d%d",&u,&v)&&(u||v))
            pre[u]=v;
        root=find(1);
        pre[root]=-1;
        dfs(root);
        printf("%d\n",max(dp[root][0],dp[root][1]));
    }
    return 0;
}
```
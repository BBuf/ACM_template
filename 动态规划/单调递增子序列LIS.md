# 单调递增子序列LIS

给定一整型数列{a1,a2...,an}（0<n<=100000），找出单调递增最长子序列，并求出其长度。

如：1 9 10 5 11 2 13的最长单调递增子序列是1 9 10 11 13，长度为5。

如果要求非递减则是:`upper_bound`

[NYOJ214 单调递增子序列(二)(LIS)](http://blog.csdn.net/riba2534/article/details/70239348)

`O(nlogn)`版:

```cpp
int a[N];
int b[N];
int main()
{
    int n;
    while(cin>>n)
    {
        int len=0;
        mem(a,0);
        mem(b,0);
        for(int i=0; i<n; i++)
            cin>>a[i];
        for(int i=0; i<n; i++)
        {
            int p=lower_bound(b,b+len,a[i])-b;//找到插入的位置
            b[p]=a[i];//插入
            if(p==len)len++;//记录位置
        }
        cout<<len<<endl;
    }
    return 0;
}

```

`O(n^2)`版：

```cpp
#include<stdio.h>
#include<string.h>
int dp[10010];
char a[10010];
int main()
{
    int t;
    scanf("%d",&t);
    while(t--)
    {
        memset(dp,0,sizeof(dp));
        int i,la,j,maxn=-1;
        scanf("%s",a+1);
        la=strlen(a+1);
        for(i=1; i<=la; i++)
        {
            for(j=i-1; j>=0; j--)
            {
                if(a[j]<a[i]&&dp[i]<dp[j]+1)//判断是否递增，并且判断当前元素所处递增序列的长度
                    dp[i]=dp[j]+1;//更新递增序列的最大长度
            }
            if(dp[i]>maxn)
                maxn=dp[i];
        }
        printf("%d\n",maxn);
    }
    return 0;
}
```



`nlog(n)`记录路径:

```cpp
const int N=5000;
int a[N],dp[N],pos[N],pre[N],n;
vector<int>v;
//dp[i]表示长度为i的子序列的可取最小末尾
//pos[i]表示长度为i的子序列的最小末尾的下标
int get_lis()
{
    mem(dp,inf);
    pos[0]=-1;
    for(int i=0; i<n; i++)
    {
        int p=lower_bound(dp,dp+n,a[i])-dp;
        dp[p]=a[i];
        pos[p]=i;
        pre[i]=(p?pos[p-1]:-1);
    }
    int ans=lower_bound(dp,dp+n,inf)-dp;
    return ans;
}
void print1(int pos)
{
    if (pre[pos] != -1)
    {
        print1(pre[pos]);
    }
    printf("%d ", a[pos]);
}
void print2(int ans)
{
    int i;
    for(i=pos[ans-1]; ~pre[i]; i=pre[i])
        v.push_back(a[i]);
    v.push_back(a[i]);
    for(int i=v.size()-1; i>=0; i--)
    {
        cout<<v[i]<<" ";
    }
    cout<<endl;
}
int main()
{
    scanf("%d",&n);
    for(int i=0; i<n; i++)
        scanf("%d",&a[i]);
    int ans=get_lis();
    cout<<"ans="<<ans<<endl;
    print1(pos[ans-1]);
    puts("");
    print2(ans);
    return 0;
}
/*
8
1 3 5 2 4 6 5 3
*/
```

`nlog(n)`的写法中还有用树状数组实现的LIS求法

树状数组维护已经插入的元素中的最大值。
有两个函数:

1. `update(int x, int val)`:代表给把x这个位置的数变成val
2. `qmax(int x)`:查询已经插入的数中小于`x`的最大值

具体的做法是:

1. `f[i]`:表示以a[i]结尾的LIS长度

对原序列进行离散化之后，对于每一个数，先查出**小于这个数结尾的**的LIS的最大值，那么`f[i]= f[i] = qmax(a[i]) + 1;`
然后再把以`a[i]`结尾的LIS的最大值`f[i]`插入到树状数组中。利用树状数组维护这一过程
以下代码ac nyoj214

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a, b) memset(a, b, sizeof(a))
const int N = 1e5 + 10;
int a[N], b[N], c[N], n, len, f[N];
int lowbit(int x)
{
    return x & -x;
}
void update(int x, int val)
{
    for (int i = x; i <= n; i += lowbit(i))
        c[i] = max(c[i], val);
}
int qmax(int x)
{
    int ans = 0;
    for (int i = x; i; i -= lowbit(i))
        ans = max(ans, c[i]);
    return ans;
}
int main()
{
    //freopen("in.txt", "r", stdin);
    while (~scanf("%d", &n))
    {
        for (int i = 1; i <= n; i++)
            scanf("%d", &a[i]), b[i] = a[i];
        sort(b + 1, b + n + 1);
        len = unique(b + 1, b + n + 1) - b - 1;
        mem(c, 0);
        int ans = 0;
        for (int i = 1; i <= n; i++)
        {
            a[i] = lower_bound(b + 1, b + len + 1, a[i]) - b;
            f[i] = qmax(a[i]) + 1;
            update(a[i], f[i]);
            ans = max(ans, f[i]);
        }
        printf("%d\n", ans);
        //for (int i = 1; i <= n; i++)
        //    printf("%d\n", f[i]);
    }
    return 0;
}
```
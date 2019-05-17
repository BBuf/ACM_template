### RMQ算法/ST表

关于RMQ介绍：[RMQ (Range Minimum/Maximum Query)算法](http://blog.csdn.net/riba2534/article/details/70825916)

预处理：

```cpp
void RMQ(int num) //预处理->O(nlogn)  
{  
    for(int j = 1; j < 20; ++j)  
        for(int i = 1; i <= num; ++i)  
            if(i + (1 << j) - 1 <= num)  
            {  
                maxsum[i][j] = max(maxsum[i][j - 1], maxsum[i + (1 << (j - 1))][j - 1]);  
                minsum[i][j] = min(minsum[i][j - 1], minsum[i + (1 << (j - 1))][j - 1]);  
            }  
}
```

查询的时候,设要查询的区间是[a,b]：

```cpp
int k=(int)(log(b-a+1.0)/log(2.0));
int maxnum=max(maxx[a][k],maxx[b-(1<<k)+1][k]);
int minnum=min(minn[a][k],minn[b-(1<<k)+1][k]);
```

[NYOJ119 士兵杀敌（三）(RMQ算法)](http://blog.csdn.net/riba2534/article/details/70832713)

对于每次询问，输出第m号士兵到第n号士兵之间所有士兵杀敌数的最大值与最小值的差。

```cpp
int maxx[N][20];int minn[N][20];
void RMQ(int n)
{
    for(int j=1; j<20; j++)
        for(int i=1; i<=n; i++)
            if(i+(1<<j)-1<=n)
            {
                maxx[i][j]=max(maxx[i][j-1],maxx[i+(1<<(j-1))][j-1]);
                minn[i][j]=min(minn[i][j-1],minn[i+(1<<(j-1))][j-1]);
            }
}
int main()
{
    int n,m,a,b,x;
    scanf("%d%d",&n,&m);
    for(int i=1; i<=n; i++)
    {
        scanf("%d",&x);minn[i][0]=maxx[i][0]=x;
    }
    RMQ(n);
    while(m--)
    {
        scanf("%d%d",&a,&b);
        int k=(int)(log(b-a+1.0)/log(2.0));
        int maxnum=max(maxx[a][k],maxx[b-(1<<k)+1][k]);
        int minnum=min(minn[a][k],minn[b-(1<<k)+1][k]);
        printf("%d\n",maxnum-minnum);
    }
    return 0;
}
```
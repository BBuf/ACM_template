# 最大完备匹配-KM算法

[HDU2255 奔小康赚大钱(二分图的最大完备匹配，KM算法)](http://blog.csdn.net/riba2534/article/details/78570571)

原理：https://www.cnblogs.com/Lanly/p/6291214.html

注意：

### KM算法的时候一定要记得把边权先全部变成`-inf`,然后如果求最小匹配，只需要将权值取相反数，结果取相反数

```cpp
const int N=300+20;
int nx,ny;//两边的点数
int e[N][N];
int match[N],cx[N],cy[N];
int slack[N];
bool visx[N],visy[N];
bool dfs(int x)
{
    visx[x]=true;
    for(int y=0; y<ny; y++)
    {
        if(visy[y])continue;
        int tmp=cx[x]+cy[y]-e[x][y];
        if(tmp==0)
        {
            visy[y]=true;
            if(match[y]==-1||dfs(match[y]))
            {
                match[y]=x;
                return true;
            }
        }
        else if(slack[y]>tmp)
            slack[y]=tmp;
    }
    return false;
}
int KM()
{
    mem(match,-1);
    mem(cy,0);
    for(int i=0; i<nx; i++)
    {
        cx[i]=-inf;
        for(int j=0; j<ny; j++)
            if(e[i][j]>cx[i])
                cx[i]=e[i][j];
    }
    for(int x=0; x<nx; x++)
    {
        mem(slack,inf);
        while(true)
        {
            mem(visx,false);
            mem(visy,false);
            if(dfs(x))break;
            //不存在的时候就要开始加上边
            int d=inf;
            for(int i=0; i<ny; i++)
                if(!visy[i]&&d>slack[i])
                    d=slack[i];//找到最小的d
            for(int i=0; i<nx; i++)
                if(visx[i])
                    cx[i]-=d;
            for(int i=0; i<ny; i++)
            {
                if(visy[i]) cy[i]+=d;
                else slack[i]-=d;
            }
        }
    }
    int res=0;
    for(int i=0; i<ny; i++)
        if(match[i]!=-1)
            res+=e[match[i]][i];
    return res;
}
int main()
{
    int n;
    while(~scanf("%d",&n))
    {
        for(int i=0; i<n; i++)
            for(int j=0; j<n; j++)
                scanf("%d",&e[i][j]);
        nx=ny=n;
        printf("%d\n",KM());
    }
    return 0;
}
```


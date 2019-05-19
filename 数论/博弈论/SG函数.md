# SG函数

首先定义**mex(minimal excludant)**运算，这是施加于一个集合的运算，表示最小的不属于这个集合的非负整数。例如

```cpp
mex{0,1,2,4}=3、mex{2,3,5}=0、mex{}=0。
```

对于一个给定的有向无环图，定义关于图的每个顶点的**Sprague-Grundy**函数g如下：g(x)=mex{ g(y) | y是x的后继},这里的g（x）即sg[x]。

例如：取石子问题，有1堆n个的石子，每次只能取{1，3,4}个石子，先取完石子者胜利，那么各个数的SG值为多少？

sg[0]=0，f[]={1,3,4},

> x=1时，可以取走1-f{1}个石子，剩余{0}个，mex{sg[0]}={0}，故sg[1]=1;
>
> x=2时，可以取走2-f{1}个石子，剩余{1}个，mex{sg[1]}={1}，故sg[2]=0；
>
> x=3时，可以取走3-f{1,3}个石子，剩余{2,0}个，mex{sg[2],sg[0]}={0,0}，故sg[3]=1;
>
> x=4时，可以取走4-f{1,3,4}个石子，剩余{3,1,0}个，mex{sg[3],sg[1],sg[0]}={1,1,0},故sg[4]=2;
>
> x=5时，可以取走5-f{1,3,4}个石子，剩余{4,2,1}个，mex{sg[4],sg[2],sg[1]}={2,0,1},故sg[5]=3；
> 以此类推.....

```cpp
x         0  1  2  3  4  5  6  7  8....
sg[x]      0  1  0  1  2  3  2  0  1....
```

参考链接:[SG函数和SG定理【详解】](http://www.cnblogs.com/ECJTUACM-873284962/p/6921829.html)

打表:

```cpp
/*
f[]:可以取走的石子个数
sg[]:0~n的sg的值
vis[]:用来求解mex{}
ps:f数组的下标，从1开始
也可以不使用ps,但是一定要保证循环有终止条件，见下面例题代码
*/
int f[N], sg[N], vis[N], ps;
void get_sg(int n)
{
    mem(sg, 0);
    for (int i = 1; i <= n; i++)
    {
        mem(vis, 0);
        for (int j = 1; f[j] <= i && j <= ps; j++)
            vis[sg[i - f[j]]] = 1;
        for (int j = 0;; j++)
            if (!vis[j])
            {
                sg[i] = j;
                break;
            }
    }
}
```

DFS:

```cpp
/*
f[]:可以取走的石子个数,必须要升序
vis[]:用来求解mex{}
sg[]:存储sg的值
n:f[]下标
*/
const int N = 1e3 + 10;
int f[N], sg[N], n;
int sg_dfs(int x)
{
    if (~sg[x])
        return sg[x];
    int vis[N]; //必须定义在里面
    mem(vis, 0);
    for (int i = 1; i <= n; i++)
        if (x >= f[i])
        {
            sg_dfs(x - f[i]);
            vis[sg[x - f[i]]] = 1;
        }
    for (int i = 0;; i++)
        if (!vis[i])
            return sg[x] = i;
}
```

例题:[HDU1847-Good Luck in CET-4 Everybody!](http://acm.hdu.edu.cn/showproblem.php?pid=1847)

题意是只能取2的次幂，所以把2的次幂打个表，然后存进f数组，计算出每个数的sg值，然后判断是否为0

```cpp
//打表
/*
f[]:可以取走的石子个数
sg[]:0~n的sg的值
vis[]:用来求解mex{}
ps:f数组的下标，从1开始
*/
const int N = 1e3 + 10;
int f[N], sg[N], vis[N];
void get_sg(int n)
{
    mem(sg, 0);
    for (int i = 1; i <= n; i++)
    {
        mem(vis, 0);
        for (int j = 1; f[j] <= i; j++)
            vis[sg[i - f[j]]] = 1;
        for (int j = 0;; j++)
            if (!vis[j])
            {
                sg[i] = j;
                break;
            }
    }
}
void init()
{
    int ans = 1, len = 0;
    for (int i = 1; i <= 11; i++)
    {
        f[++len] = ans;
        ans *= 2;
    }
    get_sg(1000);
}
int main()
{
    init();
    int x;
    while (~scanf("%d", &x))
    {
        if (sg[x])
            printf("Kiki\n");
        else
            printf("Cici\n");
    }
    return 0;
}
const int N = 1e3 + 10;
int f[N], sg[N], n;
int sg_dfs(int x)
{
    if (~sg[x])
        return sg[x];
    int vis[N]; //必须定义在里面
    mem(vis, 0);
    for (int i = 1; i <= n; i++)
        if (x >= f[i])
        {
            sg_dfs(x - f[i]);
            vis[sg[x - f[i]]] = 1;
        }
    for (int i = 0;; i++)
        if (!vis[i])
            return sg[x] = i;
}
void init()
{
    mem(sg, -1);
    sg[0] = 0;
    n = 0;
    for (int i = 1; i <= 1000; i *= 2)
        f[++n] = i;
    sg_dfs(1000);
}
int main()
{
    init();
    int x;
    while (~scanf("%d", &x))
    {
        if (sg[x])
            printf("Kiki\n");
        else
            printf("Cici\n");
    }
    return 0;
}
```
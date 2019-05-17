# 数位dp

[HDU2089 不要62(数位dp,3种写法)](https://blog.csdn.net/riba2534/article/details/80150736)

题目求区间`[L,R]`内不包含`4`和`62`的数的个数。

- 不包含`4`用状态`num1`来表示,1表示当前位包含4,0表示不包含
- 不包含`62`用状态`num2`来表示，`num2`有三个取值:
  - 0代表：`x x`(都没有出现过)
  - 1代表:`6 x`(前一位是6)
  - 2代表:`6 2`(同时出现6和2)

那么我们的dfs模板是:

`limit`表示有没有限制，比如要枚举比`123`小的数,现在要枚举`12x`,那么x的取值范围只能为`0 1 2`,如果当前是`11x`，那么x的取值可以从0到9,limit代表前一位有没有对当前产生限制.

```cpp
int dp[10][10][10], a[10];
int pd(int num2, int i)//前一个状态是num2,当前位的值是i
{
    if (num2 == 0)//x x前面没有出现过6 2
    {
        if (i == 6)return 1; //变成状态1
        else return 0;
    }
    else if (num2 == 1)//前面出现过6
    {
        if (i == 2) return 2;//变成 6 2也就是状态2
        else if (i == 6) return 1;//还是当前状态
        else return 0;
    }
    else return 2;//已经是6 2了，状态不变
}
//分别代表：当前位，num1的状态,num2的状态，当前位的前一位是否有限制
int dfs(int pos, int num1, int num2, int limit)
{
    if (pos == -1)//当前位枚举完毕 
        return num2 != 2 && num1 != 1;//是否满足条件num2的状态不为2，num1不为1
    if (!limit && ~dp[pos][num1][num2]) return dp[pos][num1][num2];
    int up = limit ? a[pos] : 9, ans = 0;//限制是否存在
    for (int i = 0; i <= up; i++)//枚举当前为可能的数
        ans += dfs(pos - 1, num1 || i == 4, pd(num2, i), limit && (i == up));
    return limit ? ans : dp[pos][num1][num2] = ans;
}
int solve(int x)
{
    int pos = 0;
    while (x)
    {
        a[pos++] = x % 10;
        x /= 10;
    }
    return dfs(pos - 1, 0, 0, 1);
}
int main()
{
    int n, m;
    mem(dp, -1);
    while (scanf("%d%d", &n, &m) && (n || m))
        printf("%d\n", solve(m) - solve(n - 1));
    return 0;
}
```

[ HDU3555 Bomb(数位dp)](https://blog.csdn.net/riba2534/article/details/80161815)

题目是让求`1~n`中包含数字`49`的数的个数,我们可以先求出不包含`49`的数的个数，然后再用总的数量减去它。

[CodeForces - 1036C Classy Numbers(数位dp)](https://blog.csdn.net/riba2534/article/details/82805974)

先说题意，给你`t`组数据,询问区间`[L,R]`内非`0`数字出现次数`<=3`次的数的个数.

定义状态：`dp[i][j]`表示前`i`位数字，非`0`数字出现`j`次的方案数。

模板修改:

num代表当前这一位，为0数字出现的次数.

所以当pos枚举完之后，直接判断`num`是否`<=3`即可.

```cpp
#define mem(a, b) memset(a, b, sizeof(a))
typedef long long ll;
const ll N = 3e5 + 10;
ll dp[20][20], a[20];
ll dfs(ll pos, ll num, ll limit)
{
    if (pos == -1)
        return num <= 3;
    if (!limit && ~dp[pos][num])
        return dp[pos][num];
    ll up = limit ? a[pos] : 9, ans = 0;
    for (ll i = 0; i <= up; i++)
        ans += dfs(pos - 1, num + (i != 0), limit && (i == up));
    return limit ? ans : dp[pos][num] = ans;
}
ll solve(ll x)
{
    ll pos = 0;
    while (x)
    {
        a[pos++] = x % 10;
        x /= 10;
    }
    return dfs(pos - 1, 0, 1);
}
int main()
{
    ll n, m, t;
    mem(dp, -1);
    scanf("%lld", &t);
    while (t--)
    {
        scanf("%lld%lld", &n, &m);
        printf("%lld\n", solve(m) - solve(n - 1));
    }
    return 0;
}
```
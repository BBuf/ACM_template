# 状压dp

[E. AC Challenge(ACM-ICPC 2018 南京赛区网络预赛，状态压缩dp)](https://blog.csdn.net/riba2534/article/details/82317233)

要进行一场比赛，这场比赛中有`n`道题目，每一道题目有两个属性`a`和`b`，完成这一道题目所获得的分数为`t * a + b`(t为时间戳)。但是有一些要求，对于某一道题目，要完成它，必须完成一些前置题目，只有做了前置题目，才可以完成当前的这道题。样例是如下给出的。首先给出一个数字代表`n`，接下来有`n`行，对于每一行首先给出两个数`a`和`b`代表这一道题目的两个属性，接下来给出一个数字`s`，代表完成这道题目之前要完成多少道题目，接下来有`s`个数字，代表这`s`道题目的具体编号是多少.

题目问你，该如何规划，可以使得获得的分数最大，最后输出这个分数。
```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll N = 1 << 21;
/*
pre[i]:表示要解决第i个问题，需要解决的问题集合(二进制状态)
dp[state]:表示解决当前状态的问题最多能获得的分数(二进制状态)
cnt[state]:表示state这个状态已经解决的问题数量
*/
ll a[N], b[N], pre[N];
ll dp[N], cnt[N], n;
void solve()
{
    dp[0] = 0;
    ll ans = 0;
    for (ll t = 1; t <= n; t++) //枚举时间
    {
        for (ll j = 0; j < n; j++) //枚举每道题目
        {
            for (ll k = 0; k < (1 << n); k++) //从0～2^n-1个状态
            {
                //如果状态k已经解决t-1个问题 且 状态k没有解决第j个问题 且 状态k已经解决了状态j的前置问题
                if (cnt[k] == t - 1 && !((k >> j) & 1) && (k & pre[j]) == pre[j])
                {
                    if ((k | (1 << j)) >= (1 << n)) //当k状态解决j问题后所得的值 >= 总状态数就跳过，因为所有的状态一共只有(1<<n)
                        continue;
                    if (dp[k] + a[j] * t + b[j] >= dp[(k | (1 << j))])
                    {
                        dp[k | (1 << j)] = dp[k] + a[j] * t + b[j];
                        cnt[k + (1 << j)] = cnt[k] + 1;
                        ans = max(ans, dp[k + (1 << j)]);
                    }
                }
            }
        }
    }
    printf("%lld\n", ans);
}
int main()
{
    //freopen("in.txt", "r", stdin);
    ll t, k;
    scanf("%lld", &n);
    for (ll i = 0; i < n; i++)
    {
        scanf("%lld%lld%lld", &a[i], &b[i], &t);
        while (t--)
        {
            scanf("%lld", &k);
            pre[i] |= (1 << (k - 1));
        }
    }
    solve();
    return 0;
}
```
# BM线性递推

输入一个序列的前几项，自动推导序列的后面的项，一般来说放8项以上。

[HDU6172 Array Challenge(BM线性递推)](https://blog.csdn.net/riba2534/article/details/82730967)

只有模质数时有效。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef long long ll;
typedef vector<int> VI;
const int maxn = 10005;
const ll mod = 1e9 + 7;
const int INF = 0x3f3f3f3f;
const double eps = 1e-9;
ll fast_mod(ll a, ll n, ll Mod)
{
    ll ans = 1;
    a %= Mod;
    while (n)
    {
        if (n & 1)
            ans = (ans * a) % Mod;
        a = (a * a) % Mod;
        n >>= 1;
    }
    return ans;
}
namespace linear_seq
{
ll res[maxn], base[maxn], num[maxn], md[maxn]; //数组大小约10000
vector<int> vec;
void mul(ll *a, ll *b, int k)
{
    for (int i = 0; i < 2 * k; i++)
        num[i] = 0;
    for (int i = 0; i < k; i++)
    {
        if (a[i])
            for (int j = 0; j < k; j++)
                num[i + j] = (num[i + j] + a[i] * b[j]) % mod;
    }
    for (int i = 2 * k - 1; i >= k; i--)
    {
        if (num[i])
            for (int j = 0; j < vec.size(); j++)
                num[i - k + vec[j]] = (num[i - k + vec[j]] - num[i] * md[vec[j]]) % mod;
    }
    for (int i = 0; i < k; i++)
        a[i] = num[i];
}
ll solve(ll n, VI a, VI b)
{
    ll ans = 0, cnt = 0;
    int k = a.size();
    assert(a.size() == b.size());
    for (int i = 0; i < k; i++)
        md[k - 1 - i] = -a[i];
    md[k] = 1;
    vec.clear();
    for (int i = 0; i < k; i++)
        if (md[i])
            vec.push_back(i);
    for (int i = 0; i < k; i++)
        res[i] = base[i] = 0;
    res[0] = 1;
    while ((1LL << cnt) <= n)
        cnt++;
    for (int p = cnt; p >= 0; p--)
    {
        mul(res, res, k);
        if ((n >> p) & 1)
        {
            for (int i = k - 1; i >= 0; i--)
                res[i + 1] = res[i];
            res[0] = 0;
            for (int j = 0; j < vec.size(); j++)
                res[vec[j]] = (res[vec[j]] - res[k] * md[vec[j]]) % mod;
        }
    }
    for (int i = 0; i < k; i++)
        ans = (ans + res[i] * b[i]) % mod;
    if (ans < 0)
        ans += mod;
    return ans;
}
VI BM(VI s)
{
    VI B(1, 1), C(1, 1);
    int L = 0, m = 1, b = 1;
    for (int i = 0; i < s.size(); i++)
    {
        ll d = 0;
        for (int j = 0; j < L + 1; j++)
            d = (d + (ll)C[j] * s[i - j]) % mod;
        if (d == 0)
            m++;
        else if (2 * L <= i)
        {
            VI T = C;
            ll c = mod - d * fast_mod(b, mod - 2, mod) % mod;
            while (C.size() < B.size() + m)
                C.push_back(0);
            for (int j = 0; j < B.size(); j++)
                C[j + m] = (C[j + m] + c * B[j]) % mod;
            L = i + 1 - L, B = T, b = d, m = 1;
        }
        else
        {
            ll c = mod - d * fast_mod(b, mod - 2, mod) % mod;
            while (C.size() < B.size() + m)
                C.push_back(0);
            for (int j = 0; j < B.size(); j++)
                C[j + m] = (C[j + m] + c * B[j]) % mod;
            m++;
        }
    }
    return C;
}
int gao(VI a, ll n)
{
    VI c = BM(a);
    c.erase(c.begin());
    for (int i = 0; i < c.size(); i++)
        c[i] = (mod - c[i]) % mod;
    return solve(n, c, VI(a.begin(), a.begin() + c.size()));
}
} // namespace linear_seq
int main()
{
    //填数字的时候带上模数之后的
    ll t, n;
    scanf("%d", &t);
    while (t--)
    {
        scanf("%lld", &n);
        printf("%lld\n", linear_seq::gao(VI{31, 197, 1255, 7997, 50959, 324725, 2069239, 13185773, 84023455, 535421093, 411853810}, n - 2));
    }
    return 0;
}
```


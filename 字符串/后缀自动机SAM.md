# 后缀自动机SAM

**后缀自动机**是一个能解决许多字符串相关问题的有力的数据结构。
举个例子，字符串问题：

- 在另一个字符串中搜索一个字符串的所有出现位置。
- 计算给定的字符串中有多少个不同的子串。
  以上问题都可以在线性的时间复杂度内通过后缀自动机来实现。
  直观上，字符串的后缀自动机可以理解为给定字符串的**所有子串**的压缩形式。值得注意的事实是，后缀自动机将所有的这些信息以高度压缩的形式储存。对于一个长度为 $$n$$ 的字符串，它的空间复杂度仅为 $$O(n)$$。此外，构造后缀自动机的时间复杂度仅为 $$O(n)$$（这里我们将字符集的大小 $$k$$ 看作常数，否则时间复杂度和空间复杂度均为 $$O(n\log k)$$)

几个性质:

1. `endpos(s)=`s在S中所有出现位置的集合,S的子串s1,s2,如果`length(s1)<=length(s2)`，则s1是s2的后缀.(`endpos(s1)>=endpos(s2)`),s1不是s2后缀当且仅当`endpos(s1)∩endpos(s2)=∅`
2. 同一个状态的子串关系(`substring(st)`包含的是`longest(st)的一系列后缀`):
   - `substring(st)`:表示状态st中所有子串集合
   - `longest(st)`:表示`st`的最长子串
   - `shortest(st)`:表示st的最短子串
3. `SAM`的`trans`表示从st开始遇到下一个字符可能是哪些,记做`next(st)`

[HihoCoder - 1445 后缀自动机二·重复旋律5(后缀自动机,不同子串个数)](https://blog.csdn.net/riba2534/article/details/82813193)

答案就是`SAM`中的每个状态中包含的子串数量的和,我们只需要计算出每一个`maxlen[st]-minlen[st]+1`的和就行了.`tot`为状态的数量.
```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N = 1e6 + 7;
/*
maxlen[st]:st包含的最长子串长度
minlen[st]:st包含的最短子串长度
trans[st]:st的转移函数
slink[st]:st的suffixLink
*/
int tot, slink[2 * N], trans[2 * N][26], minlen[2 * N], maxlen[2 * N];
char str[N];
int n;
int newstate(int _maxlen, int _minlen, int *_trans, int _slink)
{
    maxlen[++tot] = _maxlen;
    minlen[tot] = _minlen;
    slink[tot] = _slink;
    if (_trans)
        for (int i = 0; i < 26; i++)
            trans[tot][i] = _trans[i];
    return tot;
}
int add_char(char ch, int u)
{
    int c = ch - 'a', v = u;
    int z = newstate(maxlen[u] + 1, -1, NULL, 0);
    while (v && !trans[v][c])
    {
        trans[v][c] = z;
        v = slink[v];
    }
    if (!v)
    {
        minlen[z] = 1;
        slink[z] = 1;
        return z;
    }
    int x = trans[v][c];
    if (maxlen[v] + 1 == maxlen[x])
    {
        slink[z] = x;
        minlen[z] = maxlen[x] + 1;
        return z;
    }
    int y = newstate(maxlen[v] + 1, -1, trans[x], slink[x]);
    slink[z] = slink[x] = y;
    minlen[x] = minlen[z] = maxlen[y] + 1;
    while (v && trans[v][c] == x)
    {
        trans[v][c] = y;
        v = slink[v];
    }
    minlen[y] = maxlen[slink[y]] + 1;
    return z;
}
int main()
{
    scanf("%s", str);
    int len = strlen(str), pre = 1;
    tot = 1;
    for (int i = 0; i < len; i++)
        pre = add_char(str[i], pre);
    ll ans = 0;
    for (int i = 2; i <= tot; i++)
        ans += maxlen[i] - minlen[i] + 1;
    cout << ans << endl;
    return 0;
}
```
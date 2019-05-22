# AC自动机算法

转载自：<http://blog.csdn.net/niushuai666/article/details/7002823>

AC自动机简介： 

首先简要介绍一下AC自动机：Aho-Corasick automation，该算法在1975年产生于贝尔实验室，是著名的多模匹配算法之一。一个常见的例子就是给出n个单词，再给出一段包含m个字符的文章，让你找出有多少个单词在文章里出现过。要搞懂AC自动机，先得有字典树Trie和KMP模式匹配算法的基础知识。KMP算法是单模式串的字符匹配算法，AC自动机是多模式串的字符匹配算法。

AC自动机的构造：

1.构造一棵Trie，作为AC自动机的搜索数据结构。

2.构造fail指针，使当前字符失配时跳转到具有最长公共前后缀的字符继续匹配。如同 KMP算法一样， AC自动机在匹配时如果当前字符匹配失败，那么利用fail指针进行跳转。由此可知如果跳转，跳转后的串的前缀，必为跳转前的模式串的后缀并且跳转的新位置的深度（匹配字符个数）一定小于跳之前的节点。所以我们可以利用 bfs在 Trie上面进行 fail指针的求解。

3.扫描主串进行匹配。

AC自动机详讲：

我们给出5个单词，say，she，shr，he，her。给定字符串为yasherhs。问多少个单词在字符串中出现过。

[HDU2222 Keywords Search(AC自动机)](http://blog.csdn.net/riba2534/article/details/78411276)

给你给了n个字符串，然后接下来有一个串，问在这个串中，那n个串出现过几次

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=1000000+10;
struct dicTree
{
    int next[N][26],fail[N],sum[N];
    int root,sz;
    int newnode()
    {
        for(int i=0; i<26; i++)
            next[sz][i]=-1;
        sum[sz++]=0;
        return sz-1;
    }
    void init()
    {
        sz=0;
        root=newnode();
    }
    void insert(char *s)
    {
        int len=strlen(s);
        int now=root;
        for(int i=0; i<len; i++)
        {
            int to=s[i]-'a';
            if(next[now][to]==-1)
                next[now][to]=newnode();
            now=next[now][to];
        }
        sum[now]++;
    }
    void build()
    {
        queue<int>q;
        fail[root]=root;
        for(int i=0; i<26; i++)
            if(next[root][i]==-1)
                next[root][i]=root;
            else
            {
                fail[next[root][i]]=root;
                q.push(next[root][i]);
            }
        while(!q.empty())
        {
            int now=q.front();
            q.pop();
            for(int i=0; i<26; i++)
                if(next[now][i]==-1)
                    next[now][i]=next[fail[now]][i];
                else
                {
                    fail[next[now][i]]=next[fail[now]][i];
                    q.push(next[now][i]);
                }
        }
    }
    int query(char *s)
    {
        int len=strlen(s);
        int now=root;
        int ans=0;
        for(int i=0; i<len; i++)
        {
            int to=s[i]-'a';
            now=next[now][to];
            int temp=now;
            while(temp!=root)
            {
                ans+=sum[temp];
                sum[temp]=0;
                temp=fail[temp];
            }
        }
        return ans;
    }
} ac;
char s[N];
int main()
{
    int t,n;
    scanf("%d",&t);
    while(t--)
    {
        ac.init();
        scanf("%d",&n);
        while(n--)
        {
            scanf("%s",s);
            ac.insert(s);
        }
        ac.build();
        scanf("%s",s);
        printf("%d\n",ac.query(s));
    }
    return 0;
}
```

[HDU2896 病毒侵袭(AC自动机)](http://blog.csdn.net/riba2534/article/details/78421269)

与AC自动机的模板不同的是，这个题每一个串都有一个编号，那么我们在插入的时候，把编号也插进去，在查询时开一个数组标记一下子串存不存在，并输出，简单的AC自动机变形.

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a,b) memset(a,b,sizeof(a))
const int N=500*210;
struct dicTree
{
    int next[N][128],fail[N],sum[N];
    int root,sz;
    int newnode()
    {
        for(int i=0; i<128; i++)
            next[sz][i]=-1;
        sum[sz++]=-1;
        return sz-1;
    }
    void init()
    {
        sz=0;
        root=newnode();
    }
    void insert(char *s,int id)
    {
        int len=strlen(s);
        int now=root;
        for(int i=0; i<len; i++)
        {
            int to=s[i];
            if(next[now][to]==-1)
                next[now][to]=newnode();
            now=next[now][to];
        }
        sum[now]=id;
    }
    void build()
    {
        queue<int>q;
        fail[root]=root;
        for(int i=0; i<128; i++)
            if(next[root][i]==-1)
                next[root][i]=root;
            else
            {
                fail[next[root][i]]=root;
                q.push(next[root][i]);
            }
        while(!q.empty())
        {
            int now=q.front();
            q.pop();
            for(int i=0; i<128; i++)
                if(next[now][i]==-1)
                    next[now][i]=next[fail[now]][i];
                else
                {
                    fail[next[now][i]]=next[fail[now]][i];
                    q.push(next[now][i]);
                }
        }
    }
    bool vis[550];
    int query(char *s,int n,int id)
    {
        mem(vis,0);
        int len=strlen(s);
        int now=root;
        bool flag=false;
        for(int i=0; i<len; i++)
        {
            int to=s[i];
            now=next[now][to];
            int temp=now;
            while(temp!=root)
            {
                if(sum[temp]!=-1)
                {
                    vis[sum[temp]]=true;
                    flag=true;
                }
                temp=fail[temp];
            }
        }
        if(!flag) return false;
        printf("web %d:",id);
        for(int i=1; i<=n; i++)
            if(vis[i])
                printf(" %d",i);
        printf("\n");
        return true;
    }
} ac;
char s[N];
int main()
{
    int n,m;
    while(~scanf("%d",&n))
    {
        ac.init();
        for(int i=1; i<=n; i++)
        {
            scanf("%s",s);
            ac.insert(s,i);
        }
        ac.build();
        int ans=0;
        scanf("%d",&m);
        for(int i=1; i<=m; i++)
        {
            scanf("%s",s);
            if(ac.query(s,n,i))
                ans++;
        }
        printf("total: %d\n",ans);
    }
    return 0;
}
```

[HDU3065 病毒侵袭持续中(AC自动机)](http://blog.csdn.net/riba2534/article/details/78422051)

Sample Input

```
3
AA
BB
CC
ooxxCC%dAAAoen....END
```

Sample Output

```
AA: 2
CC: 1
```

在查询的时候用一个num数组记录一下出现的次数，在插入的时候也要插入id号码,通过这样来记录，AC自动机的简单使用

```cpp
#include <bits/stdc++.h>
using namespace std;
#define mem(a,b) memset(a,b,sizeof(a))
const int N=510*210;
char str[1010][100];
struct dicTree
{
    int next[N][128],sum[N],fail[N];
    int root,sz;
    int newnode()
    {
        for(int i=0; i<128; i++)
            next[sz][i]=-1;
        sum[sz++]=-1;
        return sz-1;
    }
    void init()
    {
        sz=0;
        root=newnode();
    }
    void insert(char *s,int id)
    {
        int len=strlen(s);
        int now=root;
        for(int i=0; i<len; i++)
        {
            int to=s[i];
            if(next[now][to]==-1)
                next[now][to]=newnode();
            now=next[now][to];
        }
        sum[now]=id;
    }
    void build()
    {
        queue<int>q;
        fail[root]=root;
        for(int i=0; i<128; i++)
        {
            if(next[root][i]==-1)
                next[root][i]=root;
            else
            {
                fail[next[root][i]]=root;
                q.push(next[root][i]);
            }
        }
        while(!q.empty())
        {
            int now=q.front();
            q.pop();
            for(int i=0; i<128; i++)
            {
                if(next[now][i]==-1)
                    next[now][i]=next[fail[now]][i];
                else
                {
                    fail[next[now][i]]=next[fail[now]][i];
                    q.push(next[now][i]);
                }
            }
        }
    }
    int num[1010];
    int query(char *s,int n)
    {
        mem(num,0);
        int len=strlen(s);
        int now=root;
        for(int i=0; i<len; i++)
        {
            int to=s[i];
            now=next[now][to];
            int temp=now;
            while(temp!=root)
            {
                if(sum[temp]!=-1)
                    num[sum[temp]]++;
                temp=fail[temp];
            }
        }
        for(int i=1; i<=n; i++)
            if(num[i])
                printf("%s: %d\n",str[i],num[i]);
    }
};
dicTree ac;
char s[2000000+20];
int main()
{
    int n,m;
    while(~scanf("%d",&n))
    {
        ac.init();
        for(int i=1; i<=n; i++)
        {
            scanf("%s",str[i]);
            ac.insert(str[i],i);
        }
        ac.build();
        scanf("%s",s);
        ac.query(s,n);
    }
    return 0;
}

```
[L. Poor God Water(ACM-ICPC 2018 焦作赛区网络预赛,ac自动机+矩阵快速幂)](https://blog.csdn.net/riba2534/article/details/82764769)

求不包含给出的串的方案数

我们可以想到[POJ2778 DNA Sequence(AC自动机+矩阵快速幂)](https://blog.csdn.net/riba2534/article/details/78452202)这个题,要求长度为n的序列中，不包含给的序列的方案数。我们用ac自动机+矩阵快速幂可以解决，那么思路就一样了，先把病毒串插入字典树，然后矩阵快速幂，记得加稀疏矩阵优化

```cpp
const ll N = 19;
const ll mod = 1e9 + 7;
struct Matrix
{
    ll mat[N][N], n;
    Matrix() {}
    Matrix(ll _n)
    {
        n = _n;
        mem(mat, 0);
    }
    Matrix operator*(const Matrix &b) const
    {
        Matrix ret = Matrix(n);
        for (ll i = 0; i < n; i++)
            for (ll j = 0; j < n; j++)
                if (mat[i][j])
                    for (ll k = 0; k < n; k++)
                        ret.mat[i][k] = ret.mat[i][k] + mat[i][j] * b.mat[j][k] % mod;
        for (ll i = 0; i < n; i++)
            for (ll j = 0; j < n; j++)
                ret.mat[i][j] %= mod;
        return ret;
    }
};
Matrix mat_pow(Matrix a, ll n)
{
    Matrix ret = Matrix(a.n);
    for (ll i = 0; i < ret.n; i++)
        ret.mat[i][i] = 1;
    Matrix tmp = a;
    while (n)
    {
        if (n & 1)
            ret = ret * tmp;
        tmp = tmp * tmp;
        n >>= 1;
    }
    return ret;
}
struct dicTree
{
    ll next[N][4], fail[N];
    bool end[N];
    ll root, sz;
    ll newnode()
    {
        for (ll i = 0; i < 3; i++)
            next[sz][i] = -1;
        end[sz++] = 0;
        return sz - 1;
    }
    void init()
    {
        sz = 0;
        root = newnode();
    }
    ll getch(char ch)
    {
        if (ch == '1')
            return 0;
        if (ch == '2')
            return 1;
        if (ch == '3')
            return 2;
    }
    void insert(string s)
    {
        ll len = s.size();
        ll now = root;
        for (ll i = 0; i < len; i++)
        {
            ll to = getch(s[i]);
            if (next[now][to] == -1)
                next[now][to] = newnode();
            now = next[now][to];
        }
        end[now] = true;
    }
    void build()
    {
        queue<ll> q;
        fail[root] = root;
        for (ll i = 0; i < 3; i++)
        {
            if (next[root][i] == -1)
                next[root][i] = root;
            else
            {
                fail[next[root][i]] = root;
                q.push(next[root][i]);
            }
        }
        while (!q.empty())
        {
            ll now = q.front();
            q.pop();
            if (end[fail[now]] == true)
                end[now] = true;
            for (ll i = 0; i < 3; i++)
            {
                if (next[now][i] == -1)
                    next[now][i] = next[fail[now]][i];
                else
                {
                    fail[next[now][i]] = next[fail[now]][i];
                    q.push(next[now][i]);
                }
            }
        }
    }
    Matrix get_mat()
    {
        Matrix res = Matrix(sz);
        for (ll i = 0; i < sz; i++)
            for (ll j = 0; j < 3; j++)
                if (end[next[i][j]] == false && end[i] == false)
                    res.mat[i][next[i][j]]++;
        return res;
    }
};
dicTree ac;
int main()
{
    ll t, m;
    ac.init();
    string s[10];
    s[1] = "111";
    s[2] = "222";
    s[3] = "333";
    s[6] = "123";
    s[7] = "321";
    s[4] = "212";
    s[5] = "232";
    for (ll i = 1; i <= 7; i++)
        ac.insert(s[i]);
    ac.build();
    Matrix a = ac.get_mat();
    scanf("%lld", &t);
    while (t--)
    {
        scanf("%lld", &m);
        Matrix b = a;
        b = mat_pow(b, m);
        ll ans = 0;
        for (ll i = 0; i < ac.sz; i++)
            ans = ans + b.mat[0][i];
        printf("%lld\n", ans % mod);
    }
    return 0;
}
```


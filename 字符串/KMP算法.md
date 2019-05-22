# KMP算法

在[计算机科学](https://zh.wikipedia.org/wiki/%E8%AE%A1%E7%AE%97%E6%9C%BA%E7%A7%91%E5%AD%A6)中，**Knuth-Morris-Pratt字符串查找算法**（简称为**KMP算法**）可在一个**主文本字符串**`S`内查找一个**词**`W`的出现位置。此算法通过运用对这个词在不匹配时本身就包含足够的信息来确定下一个匹配将在哪里开始的发现，从而避免重新检查先前匹配的[字符](https://zh.wikipedia.org/wiki/%E5%AD%97%E7%AC%A6)。

假设有一个字符串a,要判断b是不是a的子串，我们先求出b的所有相同的前缀和后缀，生成next数组，作用就是如果在匹配的过程中，发现字符不匹配了，我们可以通过查next数组来达到减少判断次数的目的，使时间复杂度从O(m*n)变成O(m+n),返回匹配串在原串的位置

KMP的精髓在于，对于每次失配之后，我都不会从头重新开始枚举，而是根据我已经得知的数据，从某个特定的位置开始匹配；而对于模式串的每一位，都有**唯一**的“特定变化位置”，这个在失配之后的特定变化位置可以帮助我们利用已有的数据不用从头匹配，从而节约时间。

关于kmp算法，可以参考这里:

[KMP 算法（1）：如何理解 KMP](https://61mon.com/index.php/archives/183/)	

[字符串匹配的KMP算法](http://www.ruanyifeng.com/blog/2013/05/Knuth%E2%80%93Morris%E2%80%93Pratt_algorithm.html)

其中最主要的，是对**next**数组的使用。

## next数组

定义:`next[i]`等于`P[0]...P[i - 1]`最长的相同前后缀的长度.

普通求法(可以解决循环节):

```cpp
const int N=1e6+10;
int nxt[N];
void get_next(string p)
{
    int len=p.length();
    int j=0,k=-1;
    nxt[0]=-1;
    while(j<len)
        if(k==-1||p[j]==p[k])
            nxt[++j]=++k;
        else
            k=nxt[k];
}
```

优化后的求法:

```cpp
int nxtval[N];
void get_next(string p)
{
    int len=p.length();
    int j=0,k=-1;
    nxtval[0]=-1;
    while(j<len)
    {
        if(k==-1||p[j]==p[k])
        {
            j++;
            k++;
            if(p[j]!=p[k])
                nxtval[j]=k;
            else
                nxtval[j]=nxtval[k];
        }
        else
            k=nxtval[k];
    }
}
```

区别:

**KMP 算法（未优化版）：** next 数组表示最长的相同真前后缀的长度，我们不仅可以利用 next 来解决模式串的匹配问题，也可以用来解决类似字符串重复问题等等，这类问题大家可以在各大 OJ 找到，这里不作过多表述。

**KMP 算法（优化版）：** 根据代码很容易知道（名称也改为了 nextval），优化后的 next 仅仅表示相同真前后缀的长度，但**不一定是最长**（我个人称之为 “最优相同真前后缀”）。此时我们利用优化后的 next 可以在模式串匹配问题中以更快的速度得到我们的答案（相较于未优化版），但是上述所说的字符串重复问题，优化版本则束手无策。

## 求模式串P在原串S中出现的次数

分两种情况，第一种:

当原串S为`aaaa`,模式串为`aa`时算作出现了`3`次.

终点是:`当j==plen`时，`j=nxt[j]`

[HihoCoder - 1015 -KMP算法](https://hihocoder.com/problemset/problem/1015)

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=1e6+10;
int nxt[N];
void get_next(string p)
{
    int len=p.length();
    int j=0,k=-1;
    nxt[0]=-1;
    while(j<len)
        if(k==-1||p[j]==p[k])
            nxt[++j]=++k;
        else
            k=nxt[k];
}
int get_num(string s,string p)
{
    int ans=0;
    int slen=s.length();
    int plen=p.length();
    get_next(p);
    int i=0,j=0;
    while(i<slen)
    {
        if(j==-1||s[i]==p[j])
        {
            i++;
            j++;
        }
        else
            j=nxt[j];
        if(j==plen)
        {
            ans++;
            j=nxt[j];
        }
    }
    return ans;
}
int main()
{
    int t;
    string s,p;
    cin>>t;
    while(t--)
    {
        cin>>p>>s;
        cout<<get_num(s,p)<<endl;
    }
    return 0;
}
```

分两种情况，第二种:

当原串S为`aaaa`,模式串为`aa`时算作出现了`2`次.

终点是:`当j==plen`时，`j=nxt[j]`

[HDU2087 剪花布条(暴力+KMP)](http://blog.csdn.net/riba2534/article/details/72630243)

```cpp
typedef long long ll;
const int N=1e6+10;
int nxt[N];
void get_next(string p)
{
    int len=p.length();
    int j=0,k=-1;
    nxt[0]=-1;
    while(j<len)
        if(k==-1||p[j]==p[k])
            nxt[++j]=++k;
        else
            k=nxt[k];
}
int get_num(string s,string p)
{
    int ans=0;
    int slen=s.length();
    int plen=p.length();
    get_next(p);
    int i=0,j=0;
    while(i<slen)
    {
        if(j==-1||s[i]==p[j])
        {
            i++;
            j++;
        }
        else
            j=nxt[j];
        if(j==plen)
        {
            ans++;
            j=0;
        }
    }
    return ans;
}
int main()
{
    string s,p;
    while(cin>>s&&s!="#")
    {
        cin>>p;
        cout<<get_num(s,p)<<endl;
    }
    return 0;
}
```

## 求模式串P在原串S中出现的位置

返回第一次出现的位置:

```cpp
int get_index(string s,string p)
{
    int slen=s.length();
    int plen=p.length();
    get_next(p);
    int i=0,j=0;
    while(i<slen&&j<plen)
    {
        if(j==-1||s[i]==p[j])
        {
            i++;
            j++;
        }
        else
            j=nxt[j];
    }
    if(j==plen)
        return i-j;
    return -1;
}
```

返回所有出现的位置:

[P3375 【模板】KMP字符串匹配](https://www.luogu.org/problemnew/show/P3375)

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=1000000+10;
int nxt[N];
void get_next(string p)
{
    int len=p.length();
    int j=0,k=-1;
    nxt[0]=-1;
    while(j<len)
        if(k==-1||p[j]==p[k])
            nxt[++j]=++k;
        else
            k=nxt[k];
}
void kmp(string s,string p)
{
    int slen=s.length();
    int plen=p.length();
    int i=0,j=0;
    get_next(p);
    while(i<slen)
    {
        if(j==-1||s[i]==p[j])
        {
            i++;
            j++;
        }
        else
            j=nxt[j];
        if(j==plen)
        {
            printf("%d\n",i-plen+1);
            j=nxt[j];
        }
    }
}
int main()
{
    string s,p;
    cin>>s>>p;
    kmp(s,p);
    for(int i=1; i<=p.length(); i++)
        printf("%d ",nxt[i]);
    return 0;
}

```

##  循环节问题

[HDU1358 Period(KMP，找循环节)](http://blog.csdn.net/riba2534/article/details/77985061)

给了一个长度为n的字符串，然后让你找每一个前缀（从第二个字母开始）是否是循环的，如果是就把当前的位置和循环节的长度输出

实际上就是 next 数组的使用,令`j=i−next[i]`,如果`i%j==0`就证明存在循环节，循环节的长度为`i/j`

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=1e6+10;
int nxt[N];
void get_next(string p)
{
    int len=p.length();
    int j=0,k=-1;
    nxt[0]=-1;
    while(j<len)
        if(k==-1||p[j]==p[k])
            nxt[++j]=++k;
        else
            k=nxt[k];
}
int main()
{
    int n,q=1;
    string p;
    while(cin>>n&&n)
    {
        cin>>p;
        get_next(p);
        printf("Test case #%d\n",q++);
        for(int i=1; i<=n; i++)
        {
            int j=i-nxt[i];
            if(i%j==0&&i/j>1)
                printf("%d %d\n",i,i/j);
        }
        puts("");
    }
    return 0;
}

```


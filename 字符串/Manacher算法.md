# Manacher算法

## 0. 问题定义

**最长回文子串问题：给定一个字符串，求它的最长回文子串长度。**

如果一个字符串正着读和反着读是一样的，那它就是回文串。下面是一些回文串的实例：

```
12321 a aba abba aaaa tattarrattat（牛津英语词典中最长的回文单词）
```

讲解链接:[最长回文子串——Manacher 算法](https://segmentfault.com/a/1190000003914228)

模板题:

[ P3805 【模板】manacher算法](https://www.luogu.org/problemnew/show/P3805)

给出一个只由小写英文字符a,b,c...y,z组成的字符串S,求S中最长回文串的长度.

```cpp
#include <bits/stdc++.h>
using namespace std;
const int N=51000100+10;
int n,hw[N],ans;
char a[N],s[N*2];//因为要插入字符所以*2，空间限制也很安全
void manacher()
{
    int maxright=0,mid;
    for(int i=1; i<n; i++)
    {
        if(i<maxright)
            hw[i]=min(hw[2*mid-i],hw[mid]+mid-i);//这句话重点，好好理解
        else
            hw[i]=1;
        for(; s[i+hw[i]]==s[i-hw[i]]; hw[i]++); //继续扩展
        if(hw[i]+i>maxright)
        {
            maxright=hw[i]+i;//更新
            mid=i;
        }
    }
}
void change()
{
    s[0]=s[1]='#';
    for(int i=0; i<n; i++)
    {
        s[i*2+2]=a[i];
        s[i*2+3]='#';
    }
    n=n*2+2;
    s[n]=0;
}
int main()
{
    scanf("%s",a);
    n=strlen(a);
    change();
    manacher();
    ans=1;
    for(int i=0; i<n; i++)
        ans=max(ans,hw[i]);
    printf("%d",ans-1);//想想为什么要-1
    return 0;
}

```


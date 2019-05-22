# 扩展KMP算法

**问题定义：**给定两个字符串 S 和 T（长度分别为 n 和 m），下标从 0 开始，定义`extend[i]`等于`S[i]...S[n-1]`与 T 的最长相同前缀的长度，求出所有的`extend[i]`。举个例子，看下表：

| i         | 0    | 1    | 2    | 3    | 4    | 5    | 6    | 7    |
| --------- | ---- | ---- | ---- | ---- | ---- | ---- | ---- | ---- |
| S         | a    | a    | a    | a    | a    | b    | b    | b    |
| T         | a    | a    | a    | a    | a    | c    |      |      |
| extend[i] | 5    | 4    | 3    | 2    | 1    | 0    | 0    | 0    |

为什么说这是 KMP 算法的扩展呢？显然，如果在 S 的某个位置 i 有`extend[i]`等于 m，则可知在 S 中找到了匹配串 T，并且匹配的首位置是 i。而且，扩展 KMP 算法可以找到 S 中所有 T 的匹配。接下来具体介绍下这个算法。

> 定义母串S，和子串T，设S的长度为n，T的长度为m，求T与S的每一个后缀的最长公共前缀，也就是说，设extend数组,extend[i]表示T与S[i,n-1]的最长公共前缀，要求出所有extend[i](0<=i<n)。
>
> 注意到，如果有一个位置extend[i]=m,则表示T在S中出现，而且是在位置i出现，这就是标准的KMP问题，所以说拓展kmp是对KMP算法的扩展，所以一般将它称为扩展KMP算法。

再定义一个辅助数组`int next[]`，其中`next[i]`含义为：`T[i]...T[m - 1]`与 T 的最长相同前缀长度，m 为串 T 的长度。举个例子：

| i       | 0    | 1    | 2    | 3    | 4    | 5    |
| ------- | ---- | ---- | ---- | ---- | ---- | ---- |
| T       | a    | a    | a    | a    | a    | c    |
| next[i] | 6    | 4    | 3    | 2    | 1    | 0    |

代码：

```cpp
#include <iostream>
#include <string>
using namespace std;
/* 求解 T 中 next[]，注释参考 GetExtend() */
void GetNext(string & T, int & m, int next[])
{
    int a = 0, p = 0;
    next[0] = m;
    for (int i = 1; i < m; i++)
    {
        if (i >= p || i + next[i - a] >= p)
        {
            if (i >= p)
                p = i;
            while (p < m && T[p] == T[p - i])
                p++;
            next[i] = p - i;
            a = i;
        }
        else
            next[i] = next[i - a];
    }
}
/* 求解 extend[] */
void GetExtend(string & S, int & n, string & T, int & m, int extend[], int next[])
{
    int a = 0, p = 0;
    GetNext(T, m, next);
    for (int i = 0; i < n; i++)
    {
        if (i >= p || i + next[i - a] >= p) // i >= p 的作用：举个典型例子，S 和 T 无一字符相同
        {
            if (i >= p)
                p = i;
            while (p < n && p - i < m && S[p] == T[p - i])
                p++;
            extend[i] = p - i;
            a = i;
        }
        else
            extend[i] = next[i - a];
    }
}
int main()
{
    int next[100];
    int extend[100];
    string S, T;
    int n, m;
    while (cin >> S >> T)
    {
        n = S.size();
        m = T.size();
        GetExtend(S, n, T, m, extend, next);
        // 打印 next
        cout << "next:   ";
        for (int i = 0; i < m; i++)
            cout << next[i] << " ";
        // 打印 extend
        cout << "\nextend: ";
        for (int i = 0; i < n; i++)
            cout << extend[i] << " ";
        cout << endl << endl;
    }
    return 0;
}

```


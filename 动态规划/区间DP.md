# 区间dp-状压dp

[NYOJ737 石子合并（一）(区间dp,详细)](http://blog.csdn.net/riba2534/article/details/76045531)


> 有N堆石子排成一排，每堆石子有一定的数量。现要将N堆石子并成为一堆。合并的过程只能每次将相邻的两堆石子堆成一堆，每次合并花费的代价为这两堆石子的和，经过N-1次合并后成为一堆。求出总的代价最小值。

代码1(逆着推)

```cpp
int n;
int a[N],sum[N],dp[N][N];
int main()
{
	while(~scanf("%d",&n))
	{
		mem(a,0);
		mem(sum,0);
		mem(dp,0);
		for(int i=1; i<=n; i++)
		{
			scanf("%d",&a[i]);
			sum[i]=sum[i-1]+a[i];//求出前i项的和
		}
		for(int i=n; i>=1; i--)
			for(int j=i+1; j<=n; j++)
			{
				dp[i][j]=inf;
				for(int k=i; k<j; k++)
					dp[i][j]=min(dp[i][j],dp[i][k]+dp[k+1][j]+sum[j]-sum[i-1]);
			}
		printf("%d\n",dp[1][n]);
	}
	return 0;
}

```

代码2（正着推）

```cpp
int dp[N][N],a[N],sum[N];
int main()
{
    int n;
    while(~scanf("%d",&n))
    {
        mem(sum,0);
        mem(dp,0);
        mem(a,0);
        for(int i=1; i<=n; i++)
        {
            scanf("%d",&a[i]);
            sum[i]=sum[i-1]+a[i];
        }
        for(int l=1; l<=n; l++)
        {
            for(int i=1; i+l<=n; i++)
            {
                int j=i+l;
                dp[i][j]=inf;
                for(int k=i; k<=j; k++)
                    dp[i][j]=min(dp[i][j],dp[i][k-1]+dp[k][j]+sum[j]-sum[i-1]);
            }
        }
        printf("%d\n",dp[1][n]);
    }
    return 0;
}
```

 [NYOJ15 括号匹配（二）(区间DP)](http://blog.csdn.net/riba2534/article/details/76157771)

给你一个字符串，里面只包含"(",")","[","]"四种符号，请问你需要至少添加多少个括号才能使这些括号匹配起来。
如：
[]是匹配的
([])[]是匹配的
((]是不匹配的
([)]是不匹配的

```cpp
int dp[110][110];//dp[i][j]表示从第i个符号到第j个符号，最少要添加的匹配数  
char s[110];  
int is(char a,char b)  
{  
    if(a=='('&&b==')')  
        return 1;  
    if(a=='['&&b==']')  
        return 1;  
    return 0;  
}  
int main()  
{  
    int t;  
    scanf("%d",&t);  
    while(t--)  
    {  
        scanf("%s",s);  
        int len=strlen(s);  
        mem(dp,0);  
        for(int i=0; i<len; i++)  
            dp[i][i]=1;  
        for(int i=len-2; i>=0; i--)  
            for(int j=i+1; j<len; j++)  
            {  
                dp[i][j]=dp[i+1][j]+1;//从第i个字符到第j-1个字符，没有匹配的括号时，匹配数+1  
                for(int k=i+1; k<=j; k++)  
                    if(is(s[i],s[k]))//遍历i~j这个区间，如果找到匹配的  
                        dp[i][j]=min(dp[i][j],dp[i+1][k-1]+dp[k][j]-1);//从i~k到k~j的匹配数的和减去已经匹配的一个与直接的作比较，取最小的  
            }  
        printf("%d\n",dp[0][len-1]);  
    }  
    return 0;  
}  
```

[NYOJ1023 还是回文(区间DP，详细)](http://blog.csdn.net/riba2534/article/details/76165918)

判断回文串很简单，把字符串变成回文串也不难。现在我们增加点难度，给出一串字符(全部是小写字母)，添加或删除一个字符，都会产生一定的花费。那么，将字符串变成回文串的最小花费是多少呢

```cpp
char s[2020];
int dp[2020][2020];
char c[5];
int cost[30];//增删字母所需要的花费
int main()
{
    int n,m,x,y;
    while(~scanf("%d%d",&n,&m))
    {
        mem(dp,0);
        mem(cost,0);
        scanf("%s",s);
        for(int i=0; i<n; i++)
        {
            scanf("%s%d%d",c,&x,&y);
            cost[c[0]-'a']=min(x,y);
        }
        for(int k=1; k<m; k++)
            for(int i=0; i+k<m; i++)
            {
                int j=i+k;
                dp[i][j]=min(dp[i+1][j]+cost[s[i]-'a'],dp[i][j-1]+cost[s[j]-'a']);
                if(s[i]==s[j])
                    dp[i][j]=min(dp[i][j],dp[i+1][j-1]);
            }
        printf("%d\n",dp[0][m-1]);
    }
    return 0;
}

```

[第13届景驰-埃森哲杯广东工业大学ACM程序设计大赛 D psd面试(最长回文子序列，区间dp)](https://blog.csdn.net/riba2534/article/details/79680499)

最长回文子序列:

`dp[i][j]`表示区间`i~j`的最长回文子序列长度，那么对于一个区间`[j,j+i]`，如果第`j`个元素和第`j+i`个元素相等，那么它的答案就等于`dp[j+1][j+i-1]`的值+2，否则，就在`dp[j+1][j+i]`和`dp[j][j+i-1]`中取一个最大值

这个题只需要都转换成小写就行了

```cpp
const int N=1234+7;
int f[N][N];
int main()
{
    string s;
    while (cin>>s)
    {
        mem(f,0);
        int len=s.length();
        for(int i=0; i<len; i++)f[i][i]=1;
        for(int k=1; k<len; k++)
        {
            for(int i=0; i+k<len; i++)
            {
                int j=i+k;
                if(tolower(s[i])==tolower(s[j]))
                    f[i][j]=max(f[i][j],f[i+1][j-1]+2);
                else
                    f[i][j]=max(f[i+1][j],f[i][j-1]);
            }
        }
        printf("%d\n",len-f[0][len-1]);
    }
    return 0;
}
```
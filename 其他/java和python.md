# java和python

## python:

### 进制转换

使用Python内置函数：`bin()`、`oct()`、`int()`、`hex()`可实现进制转换。

其中`int([number | string[, base]])`可以转换其他进制.比如8进制转换到二进制,我们这样`bin(int(x,8))`.

着实有用.

### datetime日期库的使用

```python

# 1000天以后
import datetime
a = datetime.datetime(2015,2,19)
a += datetime.timedelta(1000)
print(a)
```

## JAVA

在Java中有两个类BigInteger和BigDecimal分别表示大整数类和大浮点数类，至于两个类的对象能表示最大范围不清楚，理论上能够表示无线大的数，只要计算机内存足够大。 
这两个类都在`java.math.*`包中，因此每次必须在开头处引用该包。

java大数开根号:

```java
public static BigInteger getSqrt(String s) {
    BigInteger remain = BigInteger.ZERO;
    BigInteger odd = BigInteger.ZERO;
    BigInteger ans = BigInteger.ZERO;
    int group = 0, k = 0;
    if (s.length() % 2 == 1) {
        group = s.charAt(0) - '0';
        k = -1;
    } else {
        group = (s.charAt(0) - '0') * 10 + s.charAt(1) - '0';
        k = 0;
    }
    for (int j = 0; j < (s.length() + 1) / 2; j++) {
        if (j != 0)
            group = (s.charAt(j * 2 + k) - '0') * 10 + s.charAt(j * 2 + k + 1) - '0';
        odd = BigInteger.valueOf(20).multiply(ans).add(BigInteger.ONE);
        remain = BigInteger.valueOf(100).multiply(remain).add(BigInteger.valueOf(group));
        int count = 0;
        while (remain.compareTo(odd) >= 0) {
            count++;
            remain = remain.subtract(odd);
            odd = odd.add(BigInteger.valueOf(2));
        }
        ans = ans.multiply(BigInteger.TEN).add(BigInteger.valueOf(count));
    }
    return ans;
}
```

Ⅰ基本函数： 
1. `valueOf(parament);` 将参数转换为制定的类型 
2. `add();` 大整数相加 
    BigInteger a=new BigInteger(“23”); 
    BigInteger b=new BigInteger(“34”); 
    a. add(b);
3. subtract(); 相减 
4. multiply(); 相乘 
5. divide(); 相除取整 
6. remainder(); 取余 
7. pow(); a.pow(b)=a^b 
8. gcd(); 最大公约数 
9. abs(); 绝对值 
10. negate(); 取反数 
11. mod(); a.mod(b)=a%b=a.remainder(b); 
12. max(); min(); 
13. punlic int comareTo(); 
14. boolean equals(); 是否相等 
15. BigInteger构造函数： 
一般用到以下两种： 
BigInteger(String val); 
将指定字符串转换为十进制表示形式； 
BigInteger(String val,int radix); 
将指定基数的 BigInteger 的字符串表示形式转换为 BigInteger 
Ⅱ.基本常量： 
A=BigInteger.ONE 1 
B=BigInteger.TEN 10 
C=BigInteger.ZERO 0 


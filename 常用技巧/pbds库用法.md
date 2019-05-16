## pb_ds库的讲解和应用举例 

**pb_ds** 是**GNU-C++**自带的一个**C++**的扩展库，其中实现了很多数据结构，比**STL**里面的功能更强大

### 哈希表

需要的头文件:

```cpp
#include<ext/pb_ds/assoc_container.hpp>
#include<ext/pb_ds/hash_policy.hpp>
using namespace __gnu_pbds;
```

两种定义哈希表的方式：

```cpp
cc_hash_table<string,int>mp1;//拉链法
gp_hash_table<string,int>mp2;//查探法(快一些)
```

说明：

在不允许使用**C++11**的时候，pb_ds库中的两种hash函数比map的效率大大提高 ，可以代替map作为一个哈希工具,使用方法和**map**完全一样,一般来说查探法的效率更高

### 堆

需要的头文件:

用法和普通的优先队列一样

```cpp
#include<ext/pb_ds/priority_queue.hpp>
using namespace __gnu_pbds;
__gnu_pbds::priority_queue<int>q;//因为名字和std重复，故需要带上命名空间
__gnu_pbds::priority_queue<int,greater<int>,pairing_heap_tag> pq;//最快
__gnu_pbds::priority_queue<int,greater<int>,binary_heap_tag> pq;
__gnu_pbds::priority_queue<int,greater<int>,binomial_heap_tag> pq;
__gnu_pbds::priority_queue<int,greater<int>,rc_binomial_heap_tag> pq;
__gnu_pbds::priority_queue<int,greater<int>,thin_heap_tag> pq;
__gnu_pbds::priority_queue<int,greater<int> > pq;
```

`pb_ds`库的堆提供了五种tag，分别是`binary_heap_tag`，`binomal_heap_tag`，`pairing_heap_tag`，`thin_heap_tag`，`rc_binomal_heap_tag` 。 因为重名的原因一定要加上 `__gnu_pbds:: `

常用操作:

```cpp
push()  //会返回一个迭代器
top()  //同 stl 
size()  //同 stl 
empty() //同 stl 
clear()  //同 stl 
pop()  //同 stl 
join(priority_queue &other)  //合并两个堆,other会被清空
split(Pred prd,priority_queue &other)  //分离出两个堆
modify(point_iterator it,const key)  //修改一个节点的值
```

优先队列的迭代器:

```cpp
__gnu_pbds::priority_queue<int>::point_iterator it;  
```



### 红黑树(set,map)

所需头文件：

```cpp
#include <ext/pb_ds/tree_policy.hpp>
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
```

定义一棵红黑树:

```cpp
tree<int, null_type, less<int>, rb_tree_tag, tree_order_statistics_node_update> t;
/*
定义一颗红黑树
int 关键字类型
null_type无映射(低版本g++为null_mapped_type)
less<int>从小到大排序
rb_tree_tag 红黑树（splay_tree_tag）
tree_order_statistics_node_update结点更新
插入t.insert();
删除t.erase();
求k在树中是第几大:t.order_of_key();
求树中的第k大:t.find_by_order();
前驱:t.lower_bound();
后继t.upper_bound();
a.join(b)b并入a 前提是两棵树的key的取值范围不相交
a.split(v,b)key小于等于v的元素属于a，其余的属于b
T.lower_bound(x)   >=x的min的迭代器
T.upper_bound((x)  >x的min的迭代器
T.find_by_order(k) 有k个数比它小的数
*/
```
> 第一个参数代表key的类型 
> 第二个参数表示value的类型。这里不需要映射值，也就填null_type。在老版本G++中这个需要替换为null_mapped_type（如BZOJ）。 
> 第三个参数表示key的排序方式，从小到大。 
> 第四个参数表示使用哪种数据结构，rb_tree_tag表示红黑树。 
> 第五个参数表示如何更新保存节点信息，填写tree_order_statistics_node_update会额外获得order_of_key()和find_by_order()两个功能。

tree也使用和set类似的迭代器，定义为

```cpp
tree<int, null_type, less<int>, rb_tree_tag, tree_order_statistics_node_update>::iterator it;
```

要保证迭代器类型要和定义的树的类型相同。但是为了方便，在条件允许的情况下，可以用auto来自动推断类型。如：

```cpp
auto it = t.begin();
```

当然这个迭代器支持++和--操作。 
功能简介

> 1、tree包含set的全部功能，如lower_bound, upper_bound, insert, erase, find, begin, end, rbegin等。 
> 2、查询第k+1小的数，使用find_by_order()函数，返回的为迭代器。

```cpp
cout << *t.find_by_order(2) << endl;
```

> 此时输出的为排名为3的数，注意，查询的是为第k+1小的数。如果传入的k值非法，则会返回end()。 
> 3、查询比x小的数的个数，注意，是比x小的个数，不是x的排名！查询的出的值是排名-1。

```cpp
cout << t1.order_of_key(2) << endl;
```

> 4、合并两个类型相同的tree，t1.join(t2)。t2的内容会全部加入到t1，t2被清空。要保证t1的值全部小于t2或者全部大于t2，否则会抛出异常。 
> 5、不支持多重值，如果需要多重值，可以再开一个unordered_map来记录值出现的次数。将x<<32后加上出现的次数后插入tree。注意此时应该为long long类型。

```cpp
auto it=t.begin();it--;//此时的it==t.end();t.end()不是最后一位，是最后一位的下一位
```

例子:[P3369 【模板】普通平衡树](https://www.luogu.org/problemnew/show/P3369)

您需要写一种数据结构（可参考题目标题），来维护一些数，其中需要提供以下操作：

1. 插入xx数
2. 删除xx数(若有多个相同的数，因只删除一个)
3. 查询xx数的排名(排名定义为比当前数小的数的个数+1+1。若有多个相同的数，因输出最小的排名)
4. 查询排名为xx的数
5. 求xx的前驱(前驱定义为小于xx，且最大的数)
6. 求xx的后继(后继定义为大于xx，且最小的数)

```cpp
#include<ext/pb_ds/assoc_container.hpp>
#include<ext/pb_ds/tree_policy.hpp>
#include<bits/stdc++.h>
using namespace std;
using namespace __gnu_pbds;
typedef long long ll;
typedef __gnu_pbds::tree<ll, null_type, less<ll>, rb_tree_tag, tree_order_statistics_node_update> RBT;
RBT rbt;
ll T, op, x;
int main() {
    scanf("%lld", &T);
    while(T--) {
        scanf("%lld%lld", &op, &x);
        if(op == 1) rbt.insert((x << 20) + T + 1);
        else if(op == 2) rbt.erase(rbt.lower_bound(x << 20));
        else if(op == 3) printf("%lld\n", rbt.order_of_key(x << 20) + 1);
        else if(op == 4) printf("%lld\n", *rbt.find_by_order(x - 1) >> 20);
        else if(op == 5) printf("%lld\n", *(--rbt.lower_bound(x << 20)) >> 20);
        else if(op == 6) printf("%lld\n", *rbt.upper_bound((x + 1) << 20) >> 20);
    }
    return 0;
}
另一种用vector的写法写法:
vector <int>a;
switch (op){
case(1):a.insert(upper_bound(a.begin(),a.end(),x),x);break;
case(2):a.erase(lower_bound(a.begin(),a.end(),x));break;
case(3): cout<<lower_bound(a.begin(),a.end(),x)-a.begin()+1<<endl;break;
case(4): cout<<a[x-1]<<endl;break;
case(5): cout<<*--lower_bound(a.begin(),a.end(),x)<<endl;break;
case(6): cout<<*upper_bound(a.begin(),a.end(),x)<<endl;break;}
```
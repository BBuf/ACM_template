# 树剖求lca

理论复杂度为:$$O(2n+mlogn)$$,常数比倍增小

```cpp
typedef long long ll;
const int N = 5e5 + 10;
const int inf = 0x3f3f3f3f;
int n, m, s;
int fa[N], dep[N], siz[N], son[N], top[N];
int first[N], tot, cnt;
struct edge
{
    int v, next;
} e[N * 2];
void init()
{
    mem(first, -1);
    tot = 0;
    cnt = 0;
}
void add_edge(int u, int v)
{
    e[tot].v = v;
    e[tot].next = first[u];
    first[u] = tot++;
}
void dfs1(int u, int f, int deep)
{
    fa[u] = f;
    dep[u] = deep;
    siz[u] = 1;
    son[u] = 0;
    int maxson = -1;
    for (int i = first[u]; ~i; i = e[i].next)
    {
        int v = e[i].v;
        if (v == f)
            continue;
        dfs1(v, u, deep + 1);
        siz[u] += siz[v];
        if (siz[v] > maxson)
        {
            son[u] = v;
            maxson = siz[v];
        }
    }
}
void dfs2(int u, int topf)
{
    top[u] = topf;
    if (!son[u])
        return;
    dfs2(son[u], topf);
    for (int i = first[u]; ~i; i = e[i].next)
    {
        int v = e[i].v;
        if (v == fa[u] || v == son[u])
            continue;
        dfs2(v, v);
    }
}
int qlca(int x, int y)
{
    while (top[x] != top[y])
    {
        if (dep[top[x]] < dep[top[y]])
            swap(x, y);
        x = fa[top[x]];
    }
    return dep[x] < dep[y] ? x : y;
}
int main()
{
    //freopen("in.txt", "r", stdin);
    int u, v;
    scanf("%d%d%d", &n, &m, &s);
    init();
    for (int i = 1; i <= n - 1; i++)
    {
        scanf("%d%d", &u, &v);
        add_edge(u, v);
        add_edge(v, u);
    }
    dfs1(s, 0, 1);
    dfs2(s, s);
    while (m--)
    {
        scanf("%d%d", &u, &v);
        printf("%d\n", qlca(u, v));
    }

    return 0;
}
```


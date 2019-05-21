### 次短路与第K短路
次短路是除了最短路之外第二短的路，这条路的长度有可能和最短路一样长.
第K短路就是第K短的路，鉴于这两个算法都是特别模板的题，直接上例子
[HRBUST 1050 Hot Pursuit II](http://acm.hrbust.edu.cn/index.php?m=ProblemSet&a=showProblem&problem_id=1050) 
求次短路：Dijkstra的dist数组和vis数组再加一维，松弛的时候讨论当前的路小于最短路，或者大于最短路但小于次短路这两种情况，就能维护一个次短路了

```cpp
const int maxn = 1000 + 5;
const int INF = 0x3f3f3f3f;

struct Node {
    int v, c, flag;
    Node (int _v = 0, int _c = 0, int _flag = 0) : v(_v), c(_c), flag(_flag) {}
    bool operator < (const Node &rhs) const {
        return c > rhs.c;
    }
};
struct Edge {
    int v, cost;
    Edge (int _v = 0, int _cost = 0) : v(_v), cost(_cost) {}
};
vector<Edge>E[maxn];
bool vis[maxn][2];
int dist[maxn][2];
void Dijkstra(int n, int s) {
    memset(vis, false, sizeof(vis));
    for (int i = 1; i <= n; i++) {
        dist[i][0] = INF;
        dist[i][1] = INF;
    }
    priority_queue<Node>que;
    dist[s][0] = 0;
    que.push(Node(s, 0, 0));
    while (!que.empty()) {
        Node tep = que.top(); que.pop();
        int u = tep.v;
        int flag = tep.flag;
        if (vis[u][flag]) continue;
        vis[u][flag] = true;
        for (int i = 0; i < (int)E[u].size(); i++) {
            int v = E[u][i].v;
            int cost = E[u][i].cost;
            if (!vis[v][0] && dist[v][0] > dist[u][flag] + cost) {
                dist[v][1] = dist[v][0];
                dist[v][0] = dist[u][flag] + cost;
                que.push(Node(v, dist[v][0], 0));
                que.push(Node(v, dist[v][1], 1));
            } else if (!vis[v][1] && dist[v][1] > dist[u][flag] + cost) {
                dist[v][1] = dist[u][flag] + cost;
                que.push(Node(v, dist[v][1], 1));
            }
        }
    }
}
void addedge(int u, int v, int w) {
    E[u].push_back(Edge(v, w));
}
int main() {
    //freopen("in.txt", "r", stdin);
    int n, m, v, w;
    while (scanf("%d", &n) != EOF) {
        for (int i = 0; i <= n; i++) E[i].clear();
        for (int u = 1; u <= n; u++) {
            scanf("%d", &m);
            for (int j = 0; j < m; j++) {
                scanf("%d%d", &v, &w);
                addedge(u, v, w);
            }
        }
        Dijkstra(n, 1);
        printf("%d\n", dist[n][1]);
    }
    return 0;
}
```
[POJ 2449 Remmarguts' Date](http://poj.org/problem?id=2449)
求S点到T点的第K短路的长度

```cpp
#include <cstdio>
#include <cstring>
#include <queue>
#include <vector>
#include <algorithm>
using namespace std;
const int maxn = 1000 + 5;
const int INF = 0x3f3f3f3f;
int s, t, k;
bool vis[maxn];
int dist[maxn];
struct Node {
    int v, c;
    Node (int _v = 0, int _c = 0) : v(_v), c(_c) {}
    bool operator < (const Node &rhs) const {
        return c + dist[v] > rhs.c + dist[rhs.v];
    }
};
struct Edge {
    int v, cost;
    Edge (int _v = 0, int _cost = 0) : v(_v), cost(_cost) {}
};
vector<Edge>E[maxn], revE[maxn];
void Dijkstra(int n, int s) {
    memset(vis, false, sizeof(vis));
    for (int i = 1; i <= n; i++) dist[i] = INF;
    priority_queue<Node>que;
    dist[s] = 0;
    que.push(Node(s, 0));
    while (!que.empty()) {
        Node tep = que.top(); que.pop();
        int u = tep.v;
        if (vis[u]) continue;
        vis[u] = true;
        for (int i = 0; i < (int)E[u].size(); i++) {
            int v = E[u][i].v;
            int cost = E[u][i].cost;
            if (!vis[v] && dist[v] > dist[u] + cost) {
                dist[v] = dist[u] + cost;
                que.push(Node(v, dist[v]));
            }
        }
    }
}
int astar(int s) {
    priority_queue<Node> que;
    que.push(Node(s, 0)); k--;
    while (!que.empty()) {
        Node pre = que.top(); que.pop();
        int u = pre.v;
        if (u == t) {
            if (k) k--;
            else return pre.c;
        }
        for (int i = 0; i < (int)revE[u].size(); i++) {
            int v = revE[u][i].v;
            int c = revE[u][i].cost;
            que.push(Node(v, pre.c + c));
        }
    }
    return -1;
}
void addedge(int u, int v, int w) {
    revE[u].push_back(Edge(v, w));
    E[v].push_back(Edge(u, w));
}
int main() {
    //freopen("in.txt", "r", stdin);
    int n, m, u, v, w;
    while (scanf("%d%d", &n, &m) != EOF) {
        for (int i = 0; i <= n; i++) {
            E[i].clear();
            revE[i].clear();
        }
        for (int i = 0; i < m; i++) {
            scanf("%d%d%d", &u, &v, &w);
            addedge(u, v, w);
        }
        scanf("%d%d%d", &s, &t, &k);
        Dijkstra(n, t);
        if (dist[s] == INF) {
            puts("-1");
            continue;
        }
        if (s == t) k++;
        printf("%d\n", astar(s));
    }
    return 0;
}
```

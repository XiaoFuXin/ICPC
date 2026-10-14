
# 目录

## 一.准备
### 1.火车头
### 2.高精度
### 3.交互

## 二.图论
### 1.最大流
### 2.费用流
### 3.二分图
### 4.一般图最大匹配(带花树)
### 5.一般图最大权匹配
### 6. 2-SAT
### 7.虚树
### 8.上下界网络流
### 9.点分治
### 10.Kruskal 重构树

## 三.字符串
### 1.KMP
### 2.马拉车
### 3.trie树
### 4.AC自动机
### 5.回文自动机
### 6.后缀排序
### 7.后缀自动机

## 四.数据结构
### 1.带权并查集
### 2.动态开点线段树
### 3.线段树
### 4.树链
### 5.st表
### 6.左偏树
### 7.替罪羊树
### 8.树上启发式合并
### 9.主席树
### 10.主席树2（静态区间第 k 小）
### 11.可持久化并查集
### 12.可持久化tire树

## 五.数学
### 1.矩阵快速幂
### 2.线性基
### 3.Pollard-Rho(高效的质数判断方法)

## 六.计算几何
### 1.凸包
### 2.前置知识,封装及函数
### 3.半平面交
### 4.角度

## 七.杂项
### 1.染色
### 2.区间完美匹配方案数奇偶性
### 3.结论

## 一.准备
### 1.火车头
 ```cpp
#include<bits/stdc++.h>
using namespace std;

typedef long long ll;
typedef unsigned long long ull;
typedef long double ld;
typedef pair<int,int> pii;
typedef pair<ll,ll> pll;

using vi = vector<int>;
using vll = vector<ll>;
using vpii = vector<pii>;

#define pb push_back
#define mp make_pair
#define fi first
#define se second
#define all(x) (x).begin(), (x).end()
#define sz(x) ((int)(x).size())

const int N=2e5+5;
const int M=1e9+7;
const int K=20;
const int dx4[4] = {1, -1, 0, 0};
const int dy4[4] = {0, 0, 1, -1};
const int dx8[8] = {1, -1, 0, 0, 1, -1, 1, -1};
const int dy8[8] = {0, 0, 1, -1, 1, 1, -1, -1};

void solve(){
    
}
int main(){
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    int t=1;
    //cin>>t;
    while(t--)solve();
}
```
### 2.高精度
```cpp
#include<bits/stdc++.h>
using namespace std;
typedef vector<int> vi;

//去前导0
void trim(vi &a){
    while(a.size()>1&&a.back()==0)a.pop_back();
}

// 从字符串转换
vi from_str(const string &s){
    vi a;
    for(int i=s.size()-1;i>=0;i--)a.push_back(s[i]-'0');
    trim(a);
    return a;
}

//转化成字符串
string to_str(const vi &a){
    string s;
    for(int i=a.size()-1;i>=0;i--){
        s.push_back(char(a[i]+'0'));
    }
    return s.empty()?"0":s;
}

// 比较大小：a>b 返回 1，a<b 返回 -1，相等返回 0
int cmp(const vi &a,const vi &b){
    if(a.size()!=b.size())return a.size()>b.size()?1:-1;
    for(int i=(int)a.size()-1;i>=0;i--){
        if(a[i]!=b[i]){
            return a[i]>b[i]?1:-1;
        }
    }
    return 0;
}

//加法
vi add(const vi &a,const vi &b){
    vi c;
    int carry=0,n=max(a.size(),b.size());
    for(int i=0;i<n||carry;i++){
        int sum=carry;
        if(i<a.size())sum+=a[i];
        if(i<b.size())sum+=b[i];
        c.push_back(sum%10);
        carry=sum/10;
    }
    trim(c);
    return c;
}

// 减法，要求 a >= b
vi sub(const vi &a,const vi &b){
    vi c;
    int borrow=0;
    for(int i=0;i<(int)a.size();i++){
        int cur=a[i]-borrow;
        if(i<(int)b.size())cur-=b[i];
        if(cur<0){
            cur+=10;
            borrow=1;
        }
        else borrow=0;
        c.push_back(cur);
    }
    trim(c);
    return c;
}

// 乘法
vi mul(const vi &a,vi &b){
    vi c(a.size()+b.size()+1,0);
    for(int i=0;i<(int)a.size();i++){
        for(int j=0;j<(int)b.size();j++){
            c[i+j]+=a[i]*b[j];
            c[i+j+1]+=c[i+j]/10;
            c[i+j]%=10;
        }
    }
    trim(c);
    return c;
}

// 乘以一位整数 (0~9)
vi mulSmall(const vi &a, int d) {
    if (d == 0) return vi(1, 0);
    vi c;
    int carry = 0;
    for (int i = 0; i < (int)a.size() || carry; ++i) {
        int cur = carry;
        if (i < (int)a.size()) cur += a[i] * d;
        c.push_back(cur % 10);
        carry = cur / 10;
    }
    trim(c);
    return c;
}

// 除法，返回 {商, 余数}，保证 b != 0
pair<vi, vi> divmod(const vi &a, const vi &b) {
    vi rem, quo;
    for (int i = (int)a.size() - 1; i >= 0; --i) {
        // rem = rem * 10 + a[i]
        rem.insert(rem.begin(), 0);   // 左移一位（低位补0）
        rem[0] += a[i];

        // 处理进位
        int carry = 0;
        for (int j = 0; j < (int)rem.size() || carry; ++j) {
            if (j == (int)rem.size()) rem.push_back(0);
            int val = rem[j] + carry;
            carry = val / 10;
            rem[j] = val % 10;
        }

        // 关键修正：试商前去除高位前导零
        trim(rem);

        // 试商 d (9~0)
        int d = 0;
        for (int t = 9; t >= 0; --t) {
            vi tmp = mulSmall(b, t);
            if (cmp(rem, tmp) >= 0) { d = t; break; }
        }

        // 更新余数
        vi tmp = mulSmall(b, d);
        rem = sub(rem, tmp);   // sub 内部已 trim
        quo.push_back(d);
    }
    reverse(quo.begin(), quo.end());
    trim(quo);
    trim(rem);
    return {quo, rem};
}

```

### 3.交互
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    cin >> n; // 读初始参数（根据题目调整）

    int l = 1, r = n;
    while (l < r) {
        int mid = (l + r) / 2;

        cout << "? " << mid << endl; // 输出询问，endl自带刷新

        string s;
        cin >> s; // 读回复

        if (s == "<") r = mid - 1;
        else if (s == ">") l = mid + 1;
        else {
            cout << "! " << mid << endl;
            return 0;
        }
    }

    cout << "! " << l << endl;
    return 0;
}
```
## 二.图论
### 1.最大流

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const ll INF=1e18;
const int N=205;
struct edge{
    int to,rev;
    ll cap;
};
vector<edge>adj[N];
int iter[N];
int dep[N];
void add(int u,int v,ll w){
    adj[u].push_back({v,(int)adj[v].size(),w});
    adj[v].push_back({u,(int)adj[u].size()-1,0});
}
bool bfs(int s,int t){
    memset(dep,-1,sizeof(dep));
    queue<int>que;
    que.push(s);
    dep[s]=0;
    while(!que.empty()){
        int x=que.front();
        que.pop();
        for(edge &a:adj[x]){
            if(a.cap>0&&dep[a.to]==-1){
                dep[a.to]=dep[x]+1;
                que.push(a.to);
            }
        }
    }
    return dep[t]!=-1;
}
ll dfs(int cur,int t,ll f){
    if(cur==t)return f;
    while(iter[cur]<adj[cur].size()){
        edge &a=adj[cur][iter[cur]];
        if(a.cap>0&&dep[a.to]>dep[cur]){
            ll c=dfs(a.to,t,min(f,a.cap));
            if(c>0){
                a.cap-=c;
                adj[a.to][a.rev].cap+=c;
                return c;
            }
        }
        iter[cur]++;
    }
    return 0;
}
ll max_flow(int s,int t){
    ll c=0;
    while(bfs(s,t)){
        memset(iter,0,sizeof(iter));
        ll f;
        while(f=dfs(s,t,INF))c+=f;
    }
    return c;
}
int main(){
    int n,m,s,t;
    cin>>n>>m>>s>>t;
    for(int i=0;i<m;i++){
        int u,v;
        ll w;
        cin>>u>>v>>w;
        add(u,v,w);
    }
    cout<<max_flow(s,t);
}

```
### 2.费用流
```cpp
const int N=5e3+5;
const ll INF=1e10;
struct edge{
    int to,rev;
    ll v,c;
};
vector<edge>adj[N];
ll dis[N];
ll flow[N];
int pv[N];
int pe[N];
bool inq[N];

void add(int u,int v,ll f,ll c){
    adj[u].push_back({v,(int)adj[v].size(),f,c});
    adj[v].push_back({u,(int)adj[u].size()-1,0,-c});
}
bool spfa(int s,int t){
    for(int i=0;i<N;i++){
        dis[i]=INF;
        flow[i]=INF;
        inq[i]=false;
    }
    dis[s]=0;
    queue<int>que;
    que.push(s);
    while(!que.empty()){
        int x=que.front();que.pop();
        inq[x]=false;
        for(int i=0;i<adj[x].size();i++){
            edge &e=adj[x][i];
            if(e.v>0&&dis[e.to]>dis[x]+e.c){
                dis[e.to]=dis[x]+e.c;
                pv[e.to]=x;
                pe[e.to]=i;
                flow[e.to]=min(flow[x],e.v);
                if(!inq[e.to]){
                    inq[e.to]=true;
                    que.push(e.to);
                }
            }
        }
    }
    return dis[t]!=INF;
}
void minc(int s,int t){
    ll ma=0;
    ll ans=0;
    while(spfa(s,t)){
        ll f=flow[t];
        ma+=f;
        ans+=dis[t]*f;
        int v=t;
        while(v!=s){
            int u=pv[v];
            edge &e=adj[u][pe[v]];
            e.v-=f;
            adj[v][e.rev].v+=f;
            v=u;
        }
    }
    cout<<ma<<" "<<ans;
}
```
### 3.二分图
- 最大匹配
 ```cpp

#include <bits/stdc++.h>
using namespace std;
const int MAXN = 505;  // 根据题目调整顶点上限
vector<int> adj[MAXN]; // 邻接表：左部节点 -> 右部节点
int match[MAXN];       // match[v] = u 表示右部节点v匹配的左部节点u
bool used[MAXN];       // 标记右部节点是否被访问（避免重复匹配）
// 为左部节点u寻找增广路
bool hungary(int u) {
    // 遍历u能连接的所有右部节点
    for (int v : adj[u]) {
        if (used[v]) continue; // 已访问过，跳过
        used[v] = true;        // 标记为已访问
        // 情况1：右部节点v未匹配；情况2：v的匹配节点能找到其他匹配
        if (match[v] == -1 || hungary(match[v])) {
            match[v] = u;      // 更新匹配：v匹配u
            return true;       // 找到增广路，返回成功
        }
    }
    return false; // 未找到增广路
}
// 计算二分图最大匹配数（左部节点数为n）
int max_matching(int n) {
    int res = 0;
    memset(match, -1, sizeof(match)); // 初始化所有右部节点为未匹配
    for (int u = 1; u <= n; ++u) {    // 遍历所有左部节点
        memset(used, false, sizeof(used)); // 每次找增广路重置访问标记
        if (hungary(u)) res++;        // 找到增广路，匹配数+1
    }
    return res;
}

 ```
- 染色法
```cpp
const int MAXN = 505;
vector<int> adj[MAXN];
int color[MAXN]; // 0:未染色，1/2:两种颜色
// 染色DFS，返回是否为二分图
bool dfs(int u, int c) {
    color[u] = c;
    for (int v : adj[u]) {
        if (color[v] == c) return false; // 相邻节点同色，非二分图
        if (color[v] == 0 && !dfs(v, 3 - c)) return false; // 未染色则染另一种颜色
    }
    return true;
}
// 判定整个图是否为二分图
bool is_bipartite(int n) {
    memset(color, 0, sizeof(color));
    for (int i = 1; i <= n; ++i) {
        if (color[i] == 0 && !dfs(i, 1)) return false;
    }
    return true;
}

```
### 4.一般图最大匹配(带花树)
# P6113 【模板】一般图最大匹配

## 题目背景

模板题，无背景。

## 题目描述

给出一张 $n$ 个点 $m$ 条边的无向图，求该图的最大匹配。

## 输入格式

第一行两个正整数 $n$ 和 $m$，分别表示图的点数和边数。

接下来 $m$ 行，每行两个正整数 $u$ 和 $v$，表示图中存在一条连接 $u$ 和 $v$ 的无向边。

## 输出格式

第一行一个整数，表示最大匹配数。

第二行 $n$ 个整数，第 $i$ 个数表示与结点 $i$ 匹配的结点编号，若该结点无匹配则输出 $0$。

如有多解输出任意解即可。

```cpp
#include <bits/stdc++.h>
using namespace std;

const int N = 1005;

int n, m;
vector<int> g[N];

int match[N];   // 匹配对象
int pre[N];     // BFS 树上的父边
int base[N];    // 缩花后的代表点
int label[N];   // 0 未访问, 1 外点(S), 2 内点(T)
int q[N], qh, qt;

int vis[N], tim; // 求 LCA 用

// 求两个外点在当前交替树中的最近公共祖先（花根）
int lca(int u, int v) {
    ++tim;
    while (true) {
        if (u) {
            u = base[u];
            if (vis[u] == tim) return u;
            vis[u] = tim;
            u = pre[match[u]];
        }
        swap(u, v);
    }
}

// 将 u 到 p 的路径缩花
void blossom(int u, int v, int p) {
    while (base[u] != p) {
        pre[u] = v;
        v = match[u];
        if (label[v] == 2) {
            label[v] = 1;
            q[++qt] = v;
        }
        base[u] = base[v] = p;
        u = pre[v];
    }
}

// 从 s 出发找增广路，找到返回 1，否则返回 0
int find_path(int s) {
    for (int i = 1; i <= n; ++i) {
        label[i] = 0;
        pre[i] = 0;
        base[i] = i;
    }
    label[0] = pre[0] = 0;

    qh = 1; qt = 0;
    q[++qt] = s;
    label[s] = 1; // 根是外点

    while (qh <= qt) {
        int u = q[qh++];
        for (int v : g[u]) {
            if (base[u] == base[v] || match[u] == v) continue;

            if (label[v] == 0) {
                pre[v] = u;
                label[v] = 2; // 内点
                if (!match[v]) {
                    // 找到增广路，沿 pre 数组翻转匹配
                    int cur = v;
                    while (cur) {
                        int nxt = pre[cur];
                        int tmp = match[nxt];
                        match[cur] = nxt;
                        match[nxt] = cur;
                        cur = tmp;
                    }
                    return 1;
                } else {
                    label[match[v]] = 1;
                    q[++qt] = match[v];
                }
            } else if (label[v] == 1) {
                // 遇到另一个外点，形成奇环，缩花
                int p = lca(u, v);
                blossom(u, v, p);
                blossom(v, u, p);
            }
        }
    }
    return 0;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    cin >> n >> m;
    for (int i = 0; i < m; ++i) {
        int u, v;
        cin >> u >> v;
        if (u == v) continue;
        g[u].push_back(v);
        g[v].push_back(u);
    }

    int ans = 0;
    for (int i = 1; i <= n; ++i) {
        if (!match[i]) {
            ans += find_path(i);
        }
    }

    cout << ans << '\n';
    for (int i = 1; i <= n; ++i) {
        cout << match[i] << (i == n ? '\n' : ' ');
    }
    return 0;
}
```
### 6. 2-SAT
# P4782 【模板】2-SAT

## 题目描述

有 $n$ 个布尔变量 $x_1\sim x_n$，另有 $m$ 个需要满足的条件，每个条件的形式都是 「$x_i$ 为 `true` / `false` 或 $x_j$ 为 `true` / `false`」。比如 「$x_1$ 为真或 $x_3$ 为假」、「$x_7$ 为假或 $x_2$ 为假」。

2-SAT 问题的目标是给每个变量赋值使得所有条件得到满足。

## 输入格式

第一行两个整数 $n$ 和 $m$，意义如题面所述。

接下来 $m$ 行每行 $4$ 个整数 $i$, $a$, $j$, $b$，表示 「$x_i$ 为 $a$ 或 $x_j$ 为 $b$」($a, b\in \{0,1\}$)

## 输出格式

如无解，输出 `IMPOSSIBLE`；

否则输出 `POSSIBLE`，下一行 $n$ 个整数 $x_1\sim x_n$（$x_i\in\{0,1\}$），表示构造出的解。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
const int N=2e6+5;
vector<int>adj[N];
stack<int>stk;
bool instk[N];
int low[N],dfn[N],ssc[N];
int tot,cur;
int n,m;
void add(int u,int v){
    adj[u].push_back(v);
}
int node(int u,int v){
    return u+(v?0:n);
}
void dfs(int u){
    low[u]=dfn[u]=++tot;
    stk.push(u);
    instk[u]=true;
    for(int v:adj[u]){
        if(!dfn[v]){
            dfs(v);
            low[u]=min(low[u],low[v]);
        }
        else if(instk[v]){
            low[u]=min(low[u],dfn[v]);
        }
    }
    if(low[u]==dfn[u]){
        int v;
        cur++;
        do{
            v=stk.top();stk.pop();
            instk[v]=false;
            ssc[v]=cur;
        }while(v!=u);
    }
}
int main(){
    cin>>n>>m;
    while(m--){
        int i,a,j,b;
        cin>>i>>a>>j>>b;
        add(node(i,a^1),node(j,b));
        add(node(j,b^1),node(i,a));
    }
    for(int i=1;i<=2*n;i++)if(!dfn[i])dfs(i);
    for(int i=1;i<=n;i++){
        if(ssc[i]==ssc[i+n]){
            cout<<"IMPOSSIBLE"<<endl;
            return 0;
        }
    }
    cout<<"POSSIBLE"<<endl;
    for(int i=1;i<=n;i++){
        cout<<(ssc[i]<ssc[i+n]?1:0)<<" ";
    }
}
```
### 7.虚树
# P2495 【模板】虚树 / [SDOI2011] 消耗战

## 题目描述

在一场战争中，战场由 $n$ 个岛屿和 $n-1$ 个桥梁组成，保证每两个岛屿间有且仅有一条路径可达。现在，我军已经侦查到敌军的总部在编号为 $1$ 的岛屿，而且他们已经没有足够多的能源维系战斗，我军胜利在望。已知在其他 $k$ 个岛屿上有丰富能源，为了防止敌军获取能源，我军的任务是炸毁一些桥梁，使得敌军不能到达任何能源丰富的岛屿。由于不同桥梁的材质和结构不同，所以炸毁不同的桥梁有不同的代价，我军希望在满足目标的同时使得总代价最小。  

侦查部门还发现，敌军有一台神秘机器。即使我军切断所有能源之后，他们也可以用那台机器。机器产生的效果不仅仅会修复所有我军炸毁的桥梁，而且会重新随机资源分布（但可以保证的是，资源不会分布到 $1$ 号岛屿上）。不过侦查部门还发现了这台机器只能够使用 $m$ 次，所以我们只需要把每次任务完成即可。

## 输入格式

第一行一个整数 $n$，表示岛屿数量。  

接下来 $n-1$ 行，每行三个整数 $u,v,w$ ，表示 $u$ 号岛屿和 $v$ 号岛屿由一条代价为 $w$ 的桥梁直接相连。  

第 $n+1$ 行，一个整数 $m$ ，代表敌方机器能使用的次数。  

接下来 $m$ 行，第 $i$ 行一个整数 $k_i$ ，代表第 $i$ 次后，有 $k_i$ 个岛屿资源丰富。接下来 $k_i$ 个整数 $h_1,h_2,..., h_{k_i}$ ，表示资源丰富岛屿的编号。

## 输出格式

输出共 $m$ 行，表示每次任务的最小代价。

```cpp
#include<bits/stdc++.h>
using namespace std;
typedef long long ll;
typedef pair<ll,ll> pll;
const int N=3e5+5;
const int K=20;
int dep[N],dfn[N];
int fa[N][K];
ll mi[N];
vector<pll>adj[N];
vector<int>vt[N];
int tot=0;
void dfs(int u,int f){
    fa[u][0]=f;
    dep[u]=dep[f]+1;
    dfn[u]=++tot;
    for(int i=1;i<K;i++){
        fa[u][i]=fa[fa[u][i-1]][i-1];
    }
    for(auto[v,w]:adj[u]){
        if(v==f)continue;
        mi[v]=min(mi[u],w);
        dfs(v,u);
    }
}
int lca(int u,int v){
    if(dep[u]<dep[v])swap(u,v);
    int c=dep[u]-dep[v];
    for(int i=0;i<K;i++){
        if(c&(1<<i))u=fa[u][i];
    }
    if(u==v)return u;
    for(int i=K-1;i>=0;i--){
        if(fa[u][i]!=fa[v][i]){
            u=fa[u][i];
            v=fa[v][i];
        }
    }
    return fa[u][0];
}

int stk[N];
int top;
bool iskey[N];
vector<int>key;
vector<int>vs;
void build(){
    sort(key.begin(),key.end(),[&](int x,int y){
        return dfn[x]<dfn[y];
    });
    vs.clear();
    top=0;
    stk[++top]=1;
    vs.push_back(1);
    for(int i:key){
        int l=lca(i,stk[top]);
        if(l!=stk[top]){
            while(top>1&&dfn[stk[top-1]]>=dfn[l]){
                vt[stk[top-1]].push_back(stk[top]);
                top--;
            }
            if(stk[top]!=l){
                vt[l].push_back(stk[top]);
                stk[top]=l;
                vs.push_back(l);
            }
        }
        stk[++top]=i;
        vs.push_back(i);
    }
    while(top>1){
        vt[stk[top-1]].push_back(stk[top]);
        top--;
    }
}
ll dp[N];
void dfs2(int u){
    if(iskey[u])dp[u]=mi[u];
    else{
        ll sum=0;
        for(int v:vt[u]){
            dfs2(v);
            sum+=dp[v];
        }
        dp[u]=min(mi[u],sum);
    }
}
void solve(){
    int k;
    cin>>k;
    key.clear();
    for(int i=1;i<=k;i++){
        int u;
        cin>>u;
        key.push_back(u);
        iskey[u]=true;
    }
    build();
    dfs2(1);
    cout<<dp[1]<<endl;
    for(int u:vs){
        vt[u].clear();
        iskey[u]=false;
    }
}

int main(){
    int n;
    cin>>n;
    for(int i=1;i<n;i++){
        ll u,v,w;
        cin>>u>>v>>w;
        adj[u].push_back({v,w});
        adj[v].push_back({u,w});
    }
    mi[1]=1e18;
    dfs(1,0);
    int t;
    cin>>t;
    while(t--)solve();
}
```
#### 8.上下界网络流
# 上下界网络流知识点

## 一、核心思想

对于一条边 u -> v，容量为 [low, high]：

1. 强制流下界 low
2. 残量边：新建 u -> v，容量为 high - low
3. 记录不平衡量：
   - d[u] -= low
   - d[v] += low
4. 建立超级源汇：
   - 若 d[i] > 0，连 S -> i，容量 d[i]
   - 若 d[i] < 0，连 i -> T，容量 -d[i]
5. 跑 S -> T 最大流，若所有 S 出边满流，则存在可行流。

费用流版本：额外累加强制下界产生的费用 low * cost。

---

## 二、无源汇上下界可行流

问题：没有源汇，所有点流量守恒，每条边有 [low, high]，判断是否存在可行流。

步骤：

1. 对每条边 u -> v，加残量边 u -> v，容量 high - low。
2. 更新不平衡量：d[u] -= low，d[v] += low。
3. 设超级源 S，超级汇 T。
4. 对每个点 i：
   - d[i] > 0：加 S -> i，容量 d[i]，并累加 need += d[i]
   - d[i] < 0：加 i -> T，容量 -d[i]
5. 跑 S -> T 最大流。
6. 若最大流 = need，则存在可行流；否则不存在。

---

## 三、有源汇上下界最大流

问题：有源 s、汇 t，每条边 [low, high]，求 s -> t 的最大流。

步骤：

1. 加一条边 t -> s，容量 [0, INF]，费用 0。
2. 按无源汇上下界可行流的方法建图，并跑 S -> T 最大流。
3. 若不可行，返回无解。
4. 记录 t -> s 这条边上的实际流量 F。
5. 删掉 t -> s 边（正反向容量都置 0）。
6. 在残量网络上，从 s 到 t 再跑一次最大流，得到增量 delta。
7. 答案 = F + delta。

---

## 四、有源汇上下界最小流

问题：有源 s、汇 t，每条边 [low, high]，求 s -> t 的最小流。

步骤：

1. 加一条边 t -> s，容量 [0, INF]，费用 0。
2. 按无源汇上下界可行流的方法建图，并跑 S -> T 最大流。
3. 若不可行，返回无解。
4. 记录 t -> s 这条边上的实际流量 F。
5. 删掉 t -> s 边（正反向容量都置 0）。
6. 在残量网络上，从 t 到 s 跑一次最大流，得到可退流量 R。
7. 答案 = F - R。

---

## 五、费用流版本

把普通最大流替换为最小费用最大流。

无源汇最小费用可行流：

1. 对每条边 u -> v，容量 [low, high]，费用 cost：
   - 加残量边 u -> v，容量 high - low，费用 cost
   - d[u] -= low，d[v] += low
   - 累加强制下界费用：baseCost += low * cost
2. 超级源汇边费用均为 0。
3. 跑 S -> T 最小费用最大流。
4. 若满流，答案 = baseCost + 辅助图最小费用。

有源汇最大/最小费用流：

- 同样先加 t -> s，容量 [0, INF]，费用 0。
- 按有源汇最大流或最小流的逻辑处理。
- 最后累加所有费用。

---

## 六、三种写法对比

| 版本 | 是否加 t->s | 第一次跑 | 第二次跑 | 答案 |
|------|------------|----------|----------|------|
| 无源汇可行流 | 不加 | S->T 最大流 | 无 | 是否满流 |
| 有源汇最大流 | 加 | S->T 最大流 | s->t 最大流 | F + delta |
| 有源汇最小流 | 加 | S->T 最大流 | t->s 最大流 | F - R |

---

## 七、易错点

1. d[u] -= low，d[v] += low，方向不能反。
2. d[i] > 0 连 S -> i；d[i] < 0 连 i -> T。
3. 有源汇问题必须加 t -> s，容量 [0, INF]，费用 0。
4. 第二次跑最大流前，必须删掉 t -> s 边，正反向容量都置 0。
5. 有源汇最大流第二次是 s -> t；最小流第二次是 t -> s。
6. 判断可行：S -> T 最大流 = need，其中 need = sum(d[i] > 0)。
7. 多测要清空：head、tot、d、level、cur。
8. INF 要足够大，建议 1e18。

---

## 八、一句话总结

无源汇：强制下界 + 超级源汇补不平衡，跑 S -> T 看是否满流。
有源汇最大流：加 t -> s 转无源汇 -> 可行流 -> 删 t -> s -> s -> t 再增广。
有源汇最小流：加 t -> s 转无源汇 -> 可行流 -> 删 t -> s -> t -> s 退流。
费用流：把最大流换成最小费用最大流，并累加强制下界费用。

### 9.点分治
```cpp
// 1. 为什么 calc 中要先查询再插入？
//    因为如果先插入当前子树，再查询当前子树，
//    可能统计到“同一个子树内部”的路径。
//    这种路径不经过重心 u，应该交给下一层分治处理。

// 2. 为什么 divide 中先 vis[u] = true？
//    这样 calc 和 getRoot 时都会跳过已经删除的重心，
//    保证只在当前连通块内操作。

// 3. 多测一定要清空：
//    adj、vis、ans、G、sz、mx 等。
//    否则上一组数据会污染下一组。

// 4. 复杂度：
//    每个点会被 getDist 收集 O(log n) 次。
//    如果 G 的插入/查询是 O(1)，总复杂度 O(n log n)。
//    如果用 set/map，通常多一个 log。
//    如果用排序+双指针，通常 O(n log^2 n)。

// 5. 题目相关接口：
//    merge(d, w)     合并边权
//    insert(G, x)    插入状态
//    query(G, x)     查询状态
//    count(G, x)     计数状态
//    updateAns(G, x) 更新答案
//    clear(G)        清空结构
//    这些按题目实现，其他骨架基本不用动。
```

### 10.Kruskal 重构树
# P4768 [NOI2018] 归程

## 题目描述

本题的故事发生在魔力之都，在这里我们将为你介绍一些必要的设定。

魔力之都可以抽象成一个 $n$ 个节点、$m$ 条边的无向连通图（节点的编号从 $1$ 至 $n$）。我们依次用 $l,a$ 描述一条边的**长度、海拔**。

作为季风气候的代表城市，魔力之都时常有雨水相伴，因此道路积水总是不可避免的。由于整个城市的排水系统连通，因此**有积水的边一定是海拔相对最低的一些边**。我们用**水位线**来描述降雨的程度，它的意义是：所有海拔**不超过**水位线的边都是**有积水**的。

Yazid 是一名来自魔力之都的 OIer，刚参加完 ION2018 的他将踏上归程，回到他温暖的家。Yazid 的家恰好在魔力之都的 $1$ 号节点。对于接下来 $Q$ 天，每一天 Yazid 都会告诉你他的出发点 $v$，以及当天的水位线 $p$。

每一天，Yazid 在出发点都拥有一辆车。这辆车由于一些故障不能经过有积水的边。Yazid 可以在任意节点下车，这样接下来他就可以步行经过有积水的边。但车会被留在他下车的节点并不会再被使用。
需要特殊说明的是，第二天车会被重置，这意味着：

- 车会在新的出发点被准备好。
- Yazid 不能利用之前在某处停放的车。

Yazid 非常讨厌在雨天步行，因此他希望在完成回家这一目标的同时，最小化他**步行经过的边**的总长度。请你帮助 Yazid 进行计算。

本题的部分测试点将强制在线，具体细节请见【输入格式】和【子任务】。

## 输入格式

单个测试点中包含多组数据。输入的第一行为一个非负整数 $T$，表示数据的组数。

接下来依次描述每组数据，对于每组数据：

第一行 $2$ 个非负整数 $n,m$，分别表示节点数、边数。

接下来 $m$ 行，每行 $4$ 个正整数 $u, v, l, a$，描述一条连接节点 $u, v$ 的、长度为 $l$、海拔为 $a$ 的边。
在这里，我们保证 $1 \leq u,v \leq n$。

接下来一行 $3$ 个非负数 $Q, K, S$，其中 $Q$ 表示总天数，$K \in {0,1}$ 是一个会在下面被用到的系数，$S$ 表示的是可能的最高水位线。

接下来 $Q$ 行依次描述每天的状况。每行 $2$ 个整数 $v_0, p_0$ 描述一天：

- 这一天的出发节点为 $v = (v_0 + K \times \mathrm{lastans} - 1) \bmod n + 1$。
- 这一天的水位线为 $p = (p_0 + K \times \mathrm{lastans}) \bmod (S + 1)$。

其中 $\mathrm{lastans}$ 表示上一天的答案（最小步行总路程）。特别地，我们规定第 $1$ 天时 $\mathrm{lastans} = 0$。
在这里，我们保证 $1 \leq v_0 \leq n$，$0 \leq p_0 \leq S$。

对于输入中的每一行，如果该行包含多个数，则用单个空格将它们隔开。

## 输出格式

依次输出各组数据的答案。对于每组数据：

- 输出 $Q$ 行每行一个整数，依次表示每天的最小步行总路程。

### 数据范围与约定

所有测试点均保证 $T\leq 3$，所有测试点中的所有数据均满足如下限制：

- $n\leq 2\times 10^5$，$m\leq 4\times 10^5$，$Q\leq 4\times 10^5$，$K\in\left\{0,1\right\}$，$1\leq S\leq 10^9$。
- 对于所有边：$l\leq 10^4$，$a\leq 10^9$。
- 任意两点之间都直接或间接通过边相连。

**为了方便你快速理解，我们在表格中使用了一些简单易懂的表述。在此，我们对这些内容作形式化的说明：**

- 图形态：对于表格中该项为“一棵树”或“一条链”的测试点，保证 $m = n-1$。除此之外，这两类测试点分别满足如下限制：
  - 一棵树：保证输入的图是一棵树，即保证边不会构成回路。
  - 一条链：保证所有边满足 $u + 1 = v$。
- 海拔：对于表格中该项为“一种”的测试点，保证对于所有边有 $a = 1$。
- 强制在线：对于表格中该项为“是”的测试点，保证 $K = 1$；如果该项为“否”，则有 $K = 0$。
- 对于所有测试点，如果上述对应项为“不保证”，则对该项内容不作任何保证。
::cute-table{tuack}

```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
using lll = __int128;
using ull = unsigned long long;

using PII = pair<int, int>;
using PLL = pair<long long ,long long>;


struct node {
    int u, v, w;
    bool operator<(const node& a) const {
        return w > a.w;
    }
};

const int N = 2e5 + 10;
const int M = 4e5 + 10;
const ll inf = 1e18;
int n, m, Q, idx, k, s;
int pe[N * 2];
int val[N * 2];
int dep[N * 2];
int fa[N * 2][31];
node edg[M];
vector<ll> adj[N * 2];
vector<PLL> tree[N];


void clear() {
    for (int i = 0; i <= 2 * n; i++) {
        pe[i] = val[i] = dep[i] = 0;
        for (int j = 0; j < 31; j++) {
            fa[i][j] = 0;
        }

        adj[i].clear();
    }

    for (int i = 1; i <= n; i++) {
        tree[i].clear();
    }
}
int find(int x) {
    if (pe[x] != x) {
        pe[x] = find(pe[x]);
    }

    return pe[x];
}

void dfs(int u, int f) {
    fa[u][0] = f;
    dep[u] = dep[f] + 1;
    for (int i = 1; i <= 30; i++) {
        fa[u][i] = fa[fa[u][i - 1]][i - 1];
    }
    for (auto &i : adj[u]) {
        if (i == f) continue;
        dfs(i, u);
    }
}


int lca(int x, int y) {
    if (dep[x] > dep[y]) {
        swap(x, y);
    }

    int tem = dep[y] - dep[x];
    for (int i = 0; tem; i++, tem >>= 1) {
        if (tem & 1) {
            y = fa[y][i];
        }
    }

    if (x == y) return x;
    for (int i = 30; i >= 0; i--) {
        if (fa[x][i] != fa[y][i]) {
            x = fa[x][i];
            y = fa[y][i];
        }
    }

    return fa[x][0];
}

ll Ans(int p, int x) {
    for (int i = 30; i >= 0; i--) {
        if (fa[p][i] && val[fa[p][i]] > x) {
            p = fa[p][i];
        }
    }

    return p;
}

void solve() {
    cin >> n >> m;
    for (int i = 1; i <= m; i++) {
        int u, v, l, a;
        cin >> u >> v >> l >> a;

        edg[i] = {u, v, a};
        tree[u].push_back({v, l});
        tree[v].push_back({u, l});
    }

    vector<ll> dis(2 * n + 1, inf);
    vector<ll> vis(n + 1);
    priority_queue<PLL, vector<PLL>, greater<>> q;
    q.push({0, 1});
    dis[1] = 0;

    while (q.size()) {
        auto t = q.top().second;
        q.pop();

        if (vis[t]) continue;
        vis[t] = 1;

        for (auto &[i, d] : tree[t]) {
            if (dis[i] > dis[t] + d) {
                dis[i] = dis[t] + d;
                q.push({dis[i], i});
            }
        }
    }

    sort(edg + 1, edg + m + 1);

    for (int i = 1; i <= 2 * n; i++) pe[i] = i;
    idx = n;
    for (int i = 1; i <= m; i++) {
        auto [u, v, w] = edg[i];
        u = find(u);
        v = find(v);

        if (u != v) {
            ++idx;
            pe[u] = idx;
            pe[v] = idx;
            val[idx] = w;
            adj[idx].push_back(v);
            adj[idx].push_back(u);
            dis[idx] = min(dis[u], dis[v]);
        }
    }

    dfs(idx, 0);
    // for (int i = 1; i <= n; i++) cout << dis[i] << "\n";
    cin >> Q >> k >> s;

    ll ans = 0;
    while (Q--) {
        ll u, p;
        cin >> u >> p;

        u = (u + k * ans - 1) % n + 1;
        p = (p + k * ans) % (s + 1);

        ans = dis[Ans(u, p)];

        cout << ans << "\n";
    }

    clear();
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t = 1;
    cin >> t;

    while (t--) {
        solve();
    }

    return 0;
}
```

## 三.字符串
### 1.KMP
```cpp
//前缀数组
vector<int> pre(string s){
    int l=s.size();
    vector<int>pi(l,0);
    for(int i=1;i<l;i++){
        int j=pi[i-1];
        while(j>0&&s[i]!=s[j])j=pi[j-1];
        if(s[i]==s[j])j++;
        pi[i]=j;
    }
    return pi;
}
//KMP 匹配：在主串 S 中查找模式串 P，返回所有匹配的起始索引（0-based）
vector<int>kmp(string s,string p){
    vector<int>res;
    int n=s.size(),m=p.size();
    if(m==0||n<m)return res;
    vector<int> pi=pre(p);
    int j=0;
    for(int i=0;i<n;i++){
        while(j>0&&s[i]!=p[j])j=pi[j-1];
        if(s[i]==p[j])j++;
        if(j==m){
            res.push_back(i-m+1);// 起始索引=主串当前位置-模式串长度+1
            j=pi[j-1];
        }
    }
    return res;
}
```
### 2.马拉车
```cpp
// 马拉车算法核心：返回原字符串的最长回文子串
string manacher(string s){
    if(s.empty())return "";
    string t="^#";
    for(char c:s){
        t+=c;
        t+="#";
    }
    t+="$";
    int n=t.size();
    vector<int>p(n,0);
    int c=0;// 当前最右回文的中心
    int r=0;// 当前最右回文的右边界（R = C + P[C]，回文不包含 R 位置）
    for(int i=1;i<n-1;i++){
        int mirror=2*c-i;
        if(i<r)p[i]=min(r-i,p[mirror]);
        while(t[i+p[i]+1]==t[i-(p[i]+1)])p[i]++;
        if(i+p[i]>r){
            c=i;
            r=i+p[i];
        }
    }
    int max_len=0;
    int center_idx = 0; 
    for (int i = 1; i < n - 1; ++i) {
        if (p[i] > max_len) {
            max_len = p[i];
            center_idx = i;
        }
    }
    // 原字符串起始索引 = (预处理中心索引 - 最长回文半径) / 2
    int start = (center_idx - max_len) / 2;
    return s.substr(start, max_len);
}
```
### 3.trie树
```cpp
int trie[N][26];
int cnt[N];
int tot=0;

void insert(string s){
    int u=0;
    for(char c:s){
        int v=c-'a';
        if(!trie[u][v])trie[u][v]=++tot;
        u=trie[u][v];
        // 如果需要统计前缀出现次数，在这里 cnt[u]++ 
    }
    cnt[u]++;// 单词数+1（若统计重复单词）
}
// 查询某个单词出现次数
int query(string s){
    int u=0;
    for(char c:s){
        int v=c-'a';
        if(!trie[u][v])return 0;
        u=trie[u][v];
    }
    return cnt[u];
}
// 查询以 prefix 为前缀的单词个数（需要额外维护前缀计数）
// 若只需判断是否存在前缀，用 bool 即可
bool starts_with(string s){
    int u=0;
    for(char c:s){
        int v=c-'a';
        if(!trie[u][v])return false;
        u=trie[u][v];
    }
    return true;
}
//高级扩展：前缀计数 + 单词计数
int pass[MAXN];  // 经过该节点的次数（前缀统计）
int endd[MAXN];  // 以该节点结尾的单词数

void insert(const string &s) {
    int u = 0;
    for (char c : s) {
        int v = c - 'a';
        if (!trie[u][v]) trie[u][v] = ++tot;
        u = trie[u][v];
        pass[u]++;
    }
    endd[u]++;
}
```
### 4.AC自动机
```cpp
#include <bits/stdc++.h>
using namespace std;

const int MOD = 10007;
const int MAX_NODE = 6010;   // 模式串总长度 + 10
const int ALPHA = 26;        // 大写字母 A-Z

int tr[MAX_NODE][ALPHA];     // 转移表（补边后即为自动机图）
int fail[MAX_NODE];          // 失配指针
bool danger[MAX_NODE];       // 危险标记（包含模式串或失配链含危险）
int tot = 0;                 // 节点总数（0号根节点）

// ---------- 1. 初始化（多组数据必调！）----------
void init() {
    // 只需要清空用到的部分，但如果 MAX_NODE 不大（如6000），全清 memset 极快
    memset(tr, 0, sizeof(tr));
    memset(fail, 0, sizeof(fail));
    memset(danger, 0, sizeof(danger));
    tot = 0;
}

// ---------- 2. 插入模式串 ----------
void insert(const string &s) {
    int u = 0;
    for (char c : s) {
        int idx = c - 'A';   // 如果是小写字母，改为 c - 'a'
        if (!tr[u][idx]) tr[u][idx] = ++tot;
        u = tr[u][idx];
    }
    danger[u] = true;        // 标记单词结尾为危险
}

// ---------- 3. 构建 Fail 指针 + 补边（BFS） ----------
void build_fail() {
    queue<int> q;
    // 初始化根节点的直接子节点
    for (int i = 0; i < ALPHA; i++) {
        if (tr[0][i]) {
            fail[tr[0][i]] = 0;
            q.push(tr[0][i]);
        }
    }

    while (!q.empty()) {
        int u = q.front(); q.pop();

        for (int i = 0; i < ALPHA; i++) {
            if (tr[u][i]) { 
                // 有真实子节点 v
                int v = tr[u][i];
                // 核心1：设置 fail 指针
                fail[v] = tr[fail[u]][i];
                // 核心2：【危险继承】如果 fail[v] 是危险节点，v 也是危险的
                // 这就是防止漏判 "ABCD" 中包含 "BC" 的关键！
                danger[v] = danger[v] || danger[fail[v]];
                q.push(v);
            } else {
                // 核心3：补边（路径压缩）
                // 不存在的边直接指向 fail 的对应转移，省去 while 回退
                tr[u][i] = tr[fail[u]][i];
            }
        }
    }
}

// ---------- 4. DP 计算长度为 L 且不包含任何模式串的字符串个数 ----------
int dp[105][MAX_NODE]; // dp[长度][节点编号]

int solve(int L) {
    memset(dp, 0, sizeof(dp));
    dp[0][0] = 1; // 起点：长度为0，站在根节点

    for (int i = 0; i < L; i++) {
        for (int u = 0; u <= tot; u++) {
            if (dp[i][u] == 0 || danger[u]) continue; // 危险节点不走
            for (int c = 0; c < ALPHA; c++) {
                int v = tr[u][c]; // 因为补过边了，这里直接跳转，O(1)
                if (!danger[v]) { // 目标节点必须安全
                    dp[i + 1][v] = (dp[i + 1][v] + dp[i][u]) % MOD;
                }
            }
        }
    }

    int ans = 0;
    for (int u = 0; u <= tot; u++) {
        if (!danger[u]) ans = (ans + dp[L][u]) % MOD;
    }
    return ans;
}

// ---------- 5. 主函数示例（POJ 2778 / BZOJ 1030 风格）----------
int main() {
    ios::sync_with_stdio(false);
    cin.tie(0);

    init(); // 非常重要！多组数据时务必调用

    int n, L;
    cin >> n >> L; // 输入模式串个数，和要求的字符串长度

    for (int i = 0; i < n; i++) {
        string s;
        cin >> s;
        insert(s);
    }

    build_fail(); // 建自动机

    cout << solve(L) << endl; // 输出不包含模式串的方案数

    return 0;
}
```

### 5.回文自动机
```cpp
const int N=5e5+5;
int ch[N][26];
int fail[N];
int cnt[N];
int tot,last;
int len[N];
void init(){
    tot=1;
    last=0;
    len[1]=-1;
    len[0]=0;
    fail[0]=1;
    fail[1]=0;
    memset(ch,0,sizeof(ch));
}
string s;
int ans;
int getfail(int x,int i){
    while(s[i-len[x]-1]!=s[i])x=fail[x];
    return x;
}
void insert(int i){
    int c=s[i]-'a';
    int p=getfail(last,i);
    if(!ch[p][c]){
        int np=++tot;
        len[np]=len[p]+2;
        fail[np]=ch[getfail(fail[p],i)][c];
        cnt[np]=cnt[fail[np]]+1;
        ch[p][c]=np;
    }
    last=ch[p][c];
    ans=cnt[last];
}
```
### 6.后缀排序
# P3809 【模板】后缀排序
## 题目描述
读入一个长度为 $ n $ 的由大小写英文字母或数字组成的字符串，请把这个字符串的所有非空后缀按字典序（用 ASCII 数值比较）从小到大排序，然后按顺序输出后缀的第一个字符在原串中的位置。位置编号为 $ 1 $ 到 $ n $。
## 输入格式
一行一个长度为 $ n $ 的仅包含大小写英文字母或数字的字符串。
## 输出格式
一行，共 $n$ 个整数，表示答案。
```cpp
struct node{
    int fi,se,id;
}ns[N];
int n;
string s;
int rk[N];
int sa[N];
int getfail(int x,int l){
    if(x+l>n)return 0;
    return rk[x+l];
}
void solve(){
    cin>>s;
    n=s.size();
    s=" "+s;
    
    for(int i=1;i<=n;i++){
        ns[i].fi=s[i];
        ns[i].se=0;
        ns[i].id=i;
    }
    sort(ns+1,ns+1+n,[&](node &a,node &b){
        if(a.fi!=b.fi)return a.fi<b.fi;
        return a.se<b.se;
    });
    int cur=0;
     for(int i=1;i<=n;i++){
            if(i==1||ns[i].fi!=ns[i-1].fi||ns[i].se!=ns[i-1].se)cur++;
            rk[ns[i].id]=cur;
            sa[i]=ns[i].id;
        }
    for(int len=1;len<=n;len<<=1){
        for(int i=1;i<=n;i++){
            ns[i].fi=rk[i];
            ns[i].se=getfail(i,len);
            ns[i].id=i;
        }
        sort(ns+1,ns+1+n,[&](node &a,node &b){
        if(a.fi!=b.fi)return a.fi<b.fi;
        return a.se<b.se;
        });
        int cur=0;
        for(int i=1;i<=n;i++){
            if(i==1||ns[i].fi!=ns[i-1].fi||ns[i].se!=ns[i-1].se)cur++;
            rk[ns[i].id]=cur;
            sa[i]=ns[i].id;
        }
        if(cur==n)break;
    }
    for(int i=1;i<=n;i++)cout<<sa[i]<<" ";
}
```
### 7.后缀自动机
# P3804 【模板】后缀自动机（SAM）

## 题目描述

给定一个只包含小写字母的字符串 $S$。

请你求出 $S$ 的所有出现次数不为 $1$ 的子串的出现次数乘上该子串长度的最大值。

如果没有这样的子串，输出 $0$。

## 输入格式

一行一个仅包含小写字母的字符串 $S$。

## 输出格式

一个整数，为所求答案。

```cpp
const int N=2e6+5;
int ch[N][26];
int links[N],len[N],cnt[N];
int sz,last;

void extend(int c){
    int cur=++sz;
    len[cur]=len[last]+1;
    cnt[cur]=1;
    int p=last;
    while(p&&!ch[p][c]){
        ch[p][c]=cur;
        p=links[p];
    }
    if(!p){
        links[cur]=1;
    }
    else{
        int q=ch[p][c];
        if(len[p]+1==len[q]){
            links[cur]=q;
        }
        else{
            int clone=++sz;
            len[clone]=len[p]+1;
            links[clone]=links[q];
            memcpy(ch[clone],ch[q],sizeof(ch[q]));
            cnt[clone]=0;

            while(p&&ch[p][c]==q){
                ch[p][c]=clone;
                p=links[p];
            }
            links[q]=links[cur]=clone;
        }
    }
    last=cur;
}

ll solve(){
    vector<int>order(sz+1);
    vector<int>bucket(sz+1,0);
    for(int i=1;i<=sz;i++)bucket[len[i]]++;
    for(int i=1;i<=sz;i++)bucket[i]+=bucket[i-1];
    for(int i=sz;i>=1;i--)order[bucket[len[i]]--]=i;

    for(int i=sz;i>=1;i--){
        int u=order[i];
        if(links[u])cnt[links[u]]+=cnt[u];
    }
    ll ans=0;
    for(int i=2;i<=sz;i++){
        if(cnt[i]>1){
            ans=max(ans,1LL*cnt[i]*len[i]);
        }
    }
    return ans;
}

int main(){
    sz=last=1;
    string s;
    cin>>s;
    for(char c:s)extend(c-'a');
    cout<<solve()<<endl;
}
```



## 四.数据结构
### 1.带权并查集
```cpp
int find(int x){
    if(fa[x]==x)return x;
    int old=fa[x];
    int root=find(old);
    weight[x]+=weight[old];
    return fa[x]=root;
}
// 定义关系：val[x] - val[y] = w
// 返回 true 表示矛盾
bool unite(int x,int y,int w){
    int rx=find(x);
    int ry=find(y);
    // 检查 val[x] - val[y] 是否等于 w
    if(rx==ry)return (weight[x]-weight[y])!=w;
    // 把 rx 挂到 ry 下
    fa[rx]=ry;
    // 计算 val[rx] - val[ry]
    weight[rx]=w-weight[x]+weight[y];
    return false;
}
```

### 2.动态开点线段树
```cpp
const int N=4e5+5;
const ll INF=1e18;
struct {
    int ls,rs;
    ll sum;
    ll lazy;
}tr[N];
int root,cnt;
int new_node(){
    cnt++;
    tr[cnt].ls=tr[cnt].rs=0;
    tr[cnt].sum=tr[cnt].lazy=0;
    return cnt;
}
void pushup(int u){
    tr[u].sum=tr[tr[u].ls].sum+tr[tr[u].rs].sum;
}
void pushdown(int u,int l,int r){
    if(tr[u].lazy==0||!u)return;
    int mid=(l+r)/2;
    int ls=tr[u].ls,rs=tr[u].rs;
    // 左儿子不存在就新建
    if(!ls)ls=tr[u].ls=new_node();
    tr[ls].sum+=tr[u].lazy*(mid-l+1);
    tr[ls].lazy+=tr[u].lazy;
    // 右儿子不存在就新建
    if(!rs)rs=tr[u].rs=new_node();
    tr[rs].sum+=tr[u].lazy*(r-mid);
    tr[rs].lazy+=tr[u].lazy;

    tr[u].lazy=0;
}
// 区间加 [L,R] += v
void add(int &u,ll l, ll r,ll L,ll R,ll v){
    if(!u)u=new_node();
    if(L<=l&&r<=R){
        tr[u].sum+=v*(r-l+1);
        tr[u].lazy+=v;
        return;
    }
    pushdown(u,l,r);
    ll mid=(l+r)/2;
    if(L<=mid)add(tr[u].ls,l,mid,L,R,v);
    if(R>mid)add(tr[u].rs,mid+1,r,L,R,v);
    pushup(u);
}
// 区间查询 [L,R] 和
ll query(int u,ll l,ll r,ll L,ll R){
    if(!u)return 0;
    if(L<=l&&r<=R)return tr[u].sum;
    pushdown(u,l,r);
    ll mid=(l+r)/2,res=0;
    if(l<=mid)res+=query(tr[u].ls,l,mid,L,R);
    if(R>mid)res+=query(tr[u].rs,mid+1,r,L,R);
    return res;
}




typedef long long ll;
const int N=4e5+5;
const ll INF=1e18;
struct {
    int ls,rs;
    ll sum;
    ll lazy;
}tr[N];
int root,cnt;
int new_node(){
    cnt++;
    tr[cnt].ls=tr[cnt].rs=0;
    tr[cnt].sum=tr[cnt].lazy=0;
    return cnt;
}
void pushup(int u){
    tr[u].sum=tr[tr[u].ls].sum+tr[tr[u].rs].sum;
}
void pushdown(int u,ll l,ll r){
    if(tr[u].lazy==0||!u)return;
    ll mid=(l+r)/2;
    int ls=tr[u].ls,rs=tr[u].rs;
    // 左儿子不存在就新建
    if(!ls)ls=tr[u].ls=new_node();
    tr[ls].sum+=tr[u].lazy*(mid-l+1);
    tr[ls].lazy+=tr[u].lazy;
    // 右儿子不存在就新建
    if(!rs)rs=tr[u].rs=new_node();
    tr[rs].sum+=tr[u].lazy*(r-mid);
    tr[rs].lazy+=tr[u].lazy;

    tr[u].lazy=0;
}
// 区间加 [L,R] += v
void add(int &u,ll l, ll r,ll L,ll R,ll v){
    if(!u)u=new_node();
    if(L<=l&&r<=R){
        tr[u].sum+=v*(r-l+1);
        tr[u].lazy+=v;
        return;
    }
    pushdown(u,l,r);
    ll mid=(l+r)/2;
    if(L<=mid)add(tr[u].ls,l,mid,L,R,v);
    if(R>mid)add(tr[u].rs,mid+1,r,L,R,v);
    pushup(u);
}
// 区间查询 [L,R] 和
ll query(int u,ll l,ll r,ll L,ll R){
    if(!u)return 0;
    if(L<=l&&r<=R)return tr[u].sum;
    pushdown(u,l,r);
    ll mid=(l+r)/2,res=0;
    if(L<=mid)res+=query(tr[u].ls,l,mid,L,R);
    if(R>mid)res+=query(tr[u].rs,mid+1,r,L,R);
    return res;
}
```

### 3.线段树
```cpp
const int N=1e5+5;
ll a[N];
ll tree[4*N];
ll lazy[4*N];

void pushup(int p){
    tree[p]=tree[2*p]+tree[2*p+1];
}
void build(int p,int l,int r){
    if(l==r){
        tree[p]=a[l];
        return;
    }
    int mid=(l+r)/2;
    build(2*p,l,mid);
    build(2*p+1,mid+1,r);
    pushup(p);
}
void pushdown(int p,int l,int r){
    if(lazy[p]==0)return;
    int mid=(l+r)/2;
    int L=2*p,R=2*p+1;
    tree[L]+=lazy[p]*(mid-l+1);
    lazy[L]+=lazy[p];
    tree[R]+=lazy[p]*(r-mid);
    lazy[R]+=lazy[p];
    lazy[p]=0;
}
void update(int p,int l,int r,int L,int R,ll v){
    if(l>R||r<L)return;
    if(l>=L&&r<=R){
        tree[p]+=v*(r-l+1);
        lazy[p]+=v;
        return;
    }
    pushdown(p,l,r);
    int mid=(l+r)/2;
    update(2*p,l,mid,L,R,v);
    update(2*p+1,mid+1,r,L,R,v);
    pushup(p);
}
ll query(int p,int l,int r,int L,int R){
    if(l>R||r<L)return 0;
    if(l>=L&&r<=R)return tree[p];
    pushdown(p,l,r);
    int mid=(l+r)/2;
    return query(2*p,l,mid,L,R)+query(2*p+1,mid+1,r,L,R);
}
```
### 4.树链(接上线段树)
```cpp
const int N=1e5+5;
ll a[N];
ll tree[4*N];
ll lazy[4*N];

void pushup(int p){
    tree[p]=tree[2*p]+tree[2*p+1];
}
void build(int p,int l,int r){
    if(l==r){
        tree[p]=a[rk[l]];
        return;
    }
    int mid=(l+r)/2;
    build(2*p,l,mid);
    build(2*p+1,mid+1,r);
    pushup(p);
}
void pushdown(int p,int l,int r){
    if(lazy[p]==0)return;
    int mid=(l+r)/2;
    int L=2*p,R=2*p+1;
    tree[L]+=lazy[p]*(mid-l+1);
    lazy[L]+=lazy[p];
    tree[R]+=lazy[p]*(r-mid);
    lazy[R]+=lazy[p];
    lazy[p]=0;
}
void update(int p,int l,int r,int L,int R,ll v){
    if(l>R||r<L)return;
    if(l>=L&&r<=R){
        tree[p]+=v*(r-l+1);
        lazy[p]+=v;
        return;
    }
    pushdown(p,l,r);
    int mid=(l+r)/2;
    update(2*p,l,mid,L,R,v);
    update(2*p+1,mid+1,r,L,R,v);
    pushup(p);
}
ll query(int p,int l,int r,int L,int R){
    if(l>R||r<L)return 0;
    if(l>=L&&r<=R)return tree[p];
    pushdown(p,l,r);
    int mid=(l+r)/2;
    return query(2*p,l,mid,L,R)+query(2*p+1,mid+1,r,L,R);
}
int n;
vector<int>adj[N];
// ---------- 节点信息 ----------
//int a[N];// 节点初始权值
int fa[N];// 父节点
int dep[N]; // 深度
int sz[N];// 子树大小
int son[N];// 重儿子 (子树最大的儿子)
int top[N];// 当前节点所在重链的顶端节点 (链头)
int id[N];   // 节点 u 剖分后的新编号 (DFS序)
int rk[N];// 新编号对应的原节点 (rk[id[u]] = u)

int cnt=0;// DFS 序时间戳
// ---------- 第一次 DFS: 处理 fa, dep, sz, son ----------
void dfs1(int u,int f){
    fa[u]=f;
    dep[u]=dep[f]+1;
    sz[u]=1;
    son[u]=0;// 0 表示没有重儿子
    for(int v:adj[u]){
        if(v==f)continue;
        dfs1(v,u);
        sz[u]+=sz[v];
        // 取子树最大的儿子作为重儿子
        if(sz[v]>sz[son[u]])son[u]=v;
    }
}
// ---------- 第二次 DFS: 剖分, 分配 id 和 top ----------
void dfs2(int u,int tp){
    top[u]=tp;
    id[u]=++cnt;
    rk[cnt]=u;
    if(!son[u])return;// 叶子节点
     // 【关键】必须优先遍历重儿子，保证同一条重链编号连续
    dfs2(son[u],tp);
     // 遍历轻儿子，每个轻儿子自己作为新链的起点
    for(int v:adj[u]){
        if(v==fa[u]||v==son[u])continue;
        dfs2(v,v);
    }
}
// ---------- 核心: 路径操作 ----------
void update_path(int x,int y,int z){
    //z %= MOD;
    while(top[x]!=top[y]){
        if(dep[top[x]]<dep[top[y]])swap(x,y);
        update(1,1,n,id[top[x]],id[x],z);
        x=fa[top[x]];
    }
    if(dep[x]>dep[y])swap(x,y);
    update(1,1,n,id[x],id[y],z);
}
ll query_path(int x,int y){
    ll res=0;
    while(top[x]!=top[y]){
        if(dep[top[x]]<dep[top[y]])swap(x,y);
        res+=query(1,1,n,id[top[x]],id[x]);
        x=fa[top[x]];
    }
    if(dep[x]>dep[y])swap(x,y);
    res+=query(1,1,n,id[x],id[y]);
    return res;
}
// ---------- 核心: 子树操作 (DFS序连续) ----------
void update_tree(int x, int z){
    update(1,1,n,id[x],id[x]+sz[x]-1,z);
}
ll query_tree(int x){
    return query(1,1,n,id[x],id[x]+sz[x]-1);
}
```

### 5.st表
```cpp
const int N=1e5+5;
const int K=20;

int a[N];
int st[N][K];
int n;

void build(){
    for(int i=1;i<=n;i++){
        st[i][0]=a[i];
    }
    for(int j=1;j<K;j++){
        for(int i=1;i+(1<<j)-1<=n;i++){
            st[i][j]=max(st[i][j-1],st[i+(1<<(j-1))][j-1]);
        }
    }
}
int query(int l,int r){
    int len=r-l+1;
    int k=31 - __builtin_clz(len);
    return max(st[l][k],st[r-(1<<k)+1][k]);
}
```

### 6.左偏树
```cpp
int val[N],lc[N],rc[N],dist[N],fa[N];
bool del[N];
int find(int u){
    return u==fa[u]?u:fa[u]=find(fa[u]);
}
int merge(int x,int y){
    if(!x||!y)return x+y;
    if(val[x]>val[y]||(val[x]==val[y]&&x>y))swap(x,y);
    rc[x]=merge(rc[x],y);
    if(dist[lc[x]]<dist[rc[x]])swap(lc[x],rc[x]);
    dist[x]=dist[rc[x]]+1;
    return x;
}
void solve(){
    int n,m;
    cin>>n>>m;
    dist[0]=-1;
    for(int i=1;i<=n;i++){
        cin>>val[i];
        fa[i]=i;
        lc[i]=rc[i]=0;
        dist[i]=0;
        del[i]=false;
    }
    while(m--){
        int op,x,y;
        cin>>op;
        if(op==1){
            cin>>x>>y;
            if(del[x]||del[y])continue;
            x=find(x),y=find(y);
            if(x==y)continue;
            int ne=merge(x,y);
            fa[x]=fa[y]=ne;
        }
        else{
            cin>>x;
            if(del[x]){
                cout<<-1<<endl;
                continue;
            }
            x=find(x);
            cout<<val[x]<<endl;
            del[x]=true;
            int ne=merge(lc[x],rc[x]);
            fa[x] = fa[lc[x]] = fa[rc[x]] = ne;
            lc[x]=rc[x]=0;
        }
    }
}
```

### 7.替罪羊树
```cpp
const int N = 2e6 + 10;
const double ALPHA = 0.7;

int head;
int ct;

int key[N];
int cnt[N];
int lf[N];
int rt[N];
int sz[N];
int df[N];
int arr[N];
int id;
int top;
int fa;
int sd;

int init(int num) {
    key[++ct] = num;
    lf[ct] = rt[ct] = 0;
    cnt[ct] = sz[ct] = df[ct] = 1;
    return ct;
}

void up(int i) {
    sz[i] = sz[lf[i]] + sz[rt[i]] + cnt[i];
    df[i] = df[lf[i]] + df[rt[i]] + (cnt[i] > 0);
}

void inorder(int i) {
    if (i != 0) {
        inorder(lf[i]);
        if (cnt[i] > 0) {
            arr[++id] = i;
        }
        inorder(rt[i]);
    }
}

int build(int l, int r) {
    if (l > r) return 0;
    int m = l + r >> 1;
    int h = arr[m];
    lf[h] = build(l, m - 1);
    rt[h] = build(m + 1, r);
    up(h);
    return h;
}

void rebuild() {
    if (top != 0) {
        id = 0;
        inorder(top);
        if (id > 0) {
            if (fa == 0) {
                head = build(1, id);
            } else if (sd == 1) {
                lf[fa] = build(1, id);
            } else {
                rt[fa] = build(1, id);
            }
        }
    }
}

bool balance(int i) {
    return ALPHA * sz[i] >= max(df[lf[i]], df[rt[i]]);
}

void add(int i, int f, int s, int num) {
    if (i == 0) {
        if (f == 0) {
            head = init(num);
        } else if (s == 1) {
            lf[f] = init(num);
        } else {
            rt[f] = init(num);
        }
    } else {
        if (key[i] == num) {
            cnt[i]++;
        } else if (key[i] > num) {
            add(lf[i], i, 1, num);
        } else {
            add(rt[i], i, 2, num);
        }

        up(i);

        if (!balance(i)) {
            top = i;
            fa = f;
            sd = s;
        }
    }
}

void add(int num) {
    top = fa = sd = 0;
    add(head, 0, 0, num);
    rebuild();
}

int small(int i, int num) {
    if (i == 0) {
        return 0;
    }

    if (key[i] >= num) {
        return small(lf[i], num);
    } else {
        return sz[lf[i]] + cnt[i] + small(rt[i], num);
    }
}

int getrank(int num) {
    return small(head, num) + 1;
}

int index(int i, int x) {
    if (sz[lf[i]] >= x) {
        return index(lf[i], x);
    } else if (sz[lf[i]] + cnt[i] >= x) {
        return key[i];
    } else {
        return index(rt[i], x - sz[lf[i]] - cnt[i]);
    }
}

int index(int x) {
    return index(head, x);
}

int pre(int num) {
    int pos = getrank(num);
    if (pos == 1) {
        return INT_MIN;
    } else {
        return index(pos - 1);
    }
}

int post(int num) {
    int pos = getrank(num + 1);
    if (pos == sz[head] + 1) {
        return INT_MAX;
    } else {
        return index(pos);
    }
}

void remove(int i, int f, int s, int num) {
    if (key[i] == num) {
        cnt[i]--;
    } else if (key[i] > num) {
        remove(lf[i], i, 1, num);
    } else {
        remove(rt[i], i, 2, num);
    }

    up(i);
    if (!balance(i)) {
        top = i;
        fa = f;
        sd = s;
    }
}

void remove(int num) {
    if (getrank(num) != getrank(num + 1)) {
        top = fa = sd = 0;
        remove(head, 0, 0, num);
        rebuild();
    }
}

void clear() {
    for (int i = 0; i < N; i++) {
        key[i] = cnt[i] = lf[i] = rt[i] = sz[i] = df[i] = 0;
    }

    head = ct = 0;
}
```
### 8.树上启发式合并
# P9233 [蓝桥杯 2023 省 A] 颜色平衡树

## 题目描述

给定一棵树，结点由 $1$ 至 $n$ 编号，其中结点 $1$ 是树根。树的每个点有一个颜色 $C_i$。

如果一棵树中存在的每种颜色的结点个数都相同，则我们称它是一棵颜色平衡树。

求出这棵树中有多少个子树是颜色平衡树。

## 输入格式

输入的第一行包含一个整数 $n$，表示树的结点数。

接下来 $n$ 行，每行包含两个整数 $C_i,F_i$，用一个空格分隔，表示第 $i$ 个结点的颜色和父亲结点编号。

特别地，输入数据保证 $F_1$ 为 $0$，也即 $1$ 号点没有父亲结点。保证输入数据是一棵树。

## 输出格式

输出一行包含一个整数表示答案。
```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
using lll = __int128;
using ull = unsigned long long;

using PII = pair<int, int>;
using PLL = pair<long long ,long long>;

const int N = 2e5 + 10;

int n, diff, ans;
vector<vector<ll>> adj(N);
vector<ll> c(N);
vector<ll> sz(N);
vector<ll> son(N);
vector<ll> fa(N);
vector<ll> col(N);
vector<ll> cnt(N);

void dfs(int u, int f) {
    sz[u] = 1;
    fa[u] = f;
    for (auto &i : adj[u]) {
        if (i == f) continue;
        dfs(i, u);
        sz[u] += sz[i];
    }

    int p = 0;
    for (auto &i : adj[u]) {
        if (i == f) continue;
        if (sz[i] > sz[p]) {
            p = i;
        }
    }

    son[u] = p;
}

void dfs1(int u) {
    if (col[c[u]]) {
        cnt[col[c[u]]]--;
        if (cnt[col[c[u]]] == 0) diff--;
    }

    col[c[u]]++;
    if (col[c[u]]) {
        cnt[col[c[u]]]++;
        if (cnt[col[c[u]]] == 1) diff++;
    }

    for (auto &i : adj[u]) {
        if (i == fa[u]) continue;
        dfs1(i);
    }
}

void dfs2(int u) {
    if (col[c[u]]) {
        cnt[col[c[u]]]--;
        if (cnt[col[c[u]]] == 0) diff--;
    }

    col[c[u]]--;
    if (col[c[u]]) {
        cnt[col[c[u]]]++;
        if (cnt[col[c[u]]] == 1) diff++;
    }

    for (auto &i : adj[u]) {
        if (i == fa[u]) continue;
        dfs2(i);
    }
}

void dsu(int u, int k) {
    for (auto &i : adj[u]) {
        if (i == fa[u] || i == son[u]) continue;
        dsu(i, 0);
    }

    if (son[u] != 0) dsu(son[u], 1);

    if (col[c[u]]) {
        cnt[col[c[u]]]--;
        if (cnt[col[c[u]]] == 0) diff--;
    }

    col[c[u]]++;
    if (col[c[u]]) {
        cnt[col[c[u]]]++;
        if (cnt[col[c[u]]] == 1) diff++;
    }
    

    for (auto &i : adj[u]) {
        if (i == fa[u] || i == son[u]) continue;
        dfs1(i);
    }

    if (diff == 1) ans++;
    if (k == 0) {
        dfs2(u);
    }
}

void solve() {
    cin >> n;

    for (int i = 1; i <= n; i++) {
        int x;
        cin >> c[i] >> x;

        adj[i].push_back(x);
        adj[x].push_back(i);
    }

    dfs(1, 0);
    dsu(1, 0);

    cout << ans << "\n";
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t = 1;
    // cin >> t;

    while (t--) {
        solve();
    }

    return 0;
}
```
### 9.主席树
# P3919 【模板】可持久化线段树 1（可持久化数组）


## 题目描述

如题，你需要维护这样的一个长度为 $ N $ 的数组 $a$，支持如下两种操作：


1. 在某个历史版本上修改某一个位置上的值。

2. 访问某个历史版本上的某一位置的值。


此外，每进行一次操作，就会生成一个新的版本。版本编号即为当前操作的编号（从 $1$ 开始编号，版本 $0$ 表示初始状态数组）。

**对于操作 $2$，即为生成一个完全一样的版本，不作任何改动**。即，询问生成的版本是询问所访问的那个版本的复制。

## 输入格式

输入的第一行包含两个正整数 $ N, M $，分别表示数组的长度和操作的个数。

第二行包含 $ N $ 个整数，其中第 $i$ 个数为 $a_i$。

接下来 $ M $ 行每行包含 $3$ 或 $4$ 个整数，代表两种操作之一（设当前是第 $i$ 次操作）：

1. 对于操作 $1$，格式为 `v 1 p c`，即为在版本 $ v $ 的基础上，将 $ a_{p} $ 修改为 $c$。

2. 对于操作 $2$，格式为 `v 2 p`，即访问版本 $ v $ 中的 $ a_{p} $ 的值，注意：**此时应生成与版本 $v$ 完全相同的新版本**。

## 输出格式

输出包含若干行，依次为每个操作 $2$ 的结果。

```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
using lll = __int128;
using ull = unsigned long long;

using PII = pair<int, int>;
using PLL = pair<long long ,long long>;

const int N = 1e6 + 5;
const int MAXN = 24 * N;

int n, m, idx;
int a[N];
int root[N];
int lf[MAXN];
int rt[MAXN];
int val[MAXN];

inline int build(int l, int r) {
    int root = ++idx;
    if (l == r) {
        val[idx] = a[l];
        return root;
    }

    int mid = l + r >> 1;
    lf[root] = build(l, mid);
    rt[root] = build(mid + 1, r);

    return root;
}

inline int update(int pre, int l, int r, int p, int x) {
    int root = ++idx;
    lf[root] = lf[pre];
    rt[root] = rt[pre];

    if (l == r) {
        val[root] = x;
        return root;
    }

    int mid = l + r >> 1;
    if (p <= mid) lf[root] = update(lf[pre], l, mid, p, x);
    else rt[root] = update(rt[pre], mid + 1, r, p, x);

    return root;
}

inline int query(int pre, int l, int r, int p) {
    if (l == r) {
        return val[pre];
    }

    int mid = l + r >> 1;
    if (p <= mid) return query(lf[pre], l, mid, p);
    return query(rt[pre], mid + 1, r, p);
}

void solve() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
    }

    root[0] = build(1, n);
    for (int i = 1; i <= m; i++) {
        int v, o;
        cin >> v >> o;

        if (o == 1) {
            int p, c;
            cin >> p >> c;
            root[i] = update(root[v], 1, n, p, c);
        } else {
            int p;
            cin >> p;

            root[i] = root[v];
            cout << query(root[v], 1, n, p) << "\n";
        }
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t = 1;
    // cin >> t;

    while (t--) {
        solve();
    }

    return 0;
}
```
### 10.主席树2（静态区间第 k 小）
# P3834 【模板】可持久化线段树 2（静态区间第 k 小）

## 题目背景

这是个非常经典的可持久化权值线段树入门题——静态区间第 $k$ 小。

## 题目描述

如题，给定 $n$ 个整数构成的序列 $a$，将对于指定的闭区间 $[l, r]$ 查询其区间内的第 $k$ 小值。

## 输入格式

第一行包含两个整数，分别表示序列的长度 $n$ 和查询的个数 $m$。  
第二行包含 $n$ 个整数，第 $i$ 个整数表示序列的第 $i$ 个元素 $a_i$。   
接下来 $m$ 行每行包含三个整数 $ l, r, k$ , 表示查询区间 $[l, r]$ 内的第 $k$ 小值。

## 输出格式

对于每次询问，输出一行一个整数表示答案。
```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
using lll = __int128;
using ull = unsigned long long;

using PII = pair<int, int>;
using PLL = pair<long long ,long long>;

const int N = 2e5 + 5;
const int MAXN = 24 * N;

int n, m, idx;
int a[N];
int tmp[N];
int arr[N];
int cnt[N];

int root[N];
int lf[MAXN];
int rt[MAXN];
int sz[MAXN];

int build(int l, int r) {
    int p = ++idx;
    if (l == r) {
        return p;
    }

    int mid = l + r >> 1;
    lf[p] = build(l, mid);
    rt[p] = build(mid + 1, r);

    return p;
}

int update(int pre, int l, int r, int p, int x) {
    int root = ++idx;
    lf[root] = lf[pre];
    rt[root] = rt[pre];
    if (l == r) {
        sz[root] = x;
        return root;
    }

    int mid = l + r >> 1;
    if (p <= mid) {
        lf[root] = update(lf[root], l, mid, p, x);
    } else {
        rt[root] = update(rt[root], mid + 1, r, p, x);
    }

    sz[root] = sz[lf[root]] + sz[rt[root]];
    return root;
}

int query(int l, int r, int p1, int p2, int k) {
    if (l == r) {
        return tmp[l];
    }

    int Lsz = sz[lf[p2]] - sz[lf[p1]];

    int mid = l + r >> 1;
    if (Lsz >= k) {
        return query(l, mid, lf[p1], lf[p2], k);
    }

    return query(mid + 1, r, rt[p1], rt[p2], k - Lsz);
}
void solve() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++) {
        cin >> a[i];
        tmp[i] = a[i];
    }

    sort(tmp + 1, tmp + n + 1);
    int len = unique(tmp + 1, tmp + n + 1) - (tmp + 1);
    for (int i = 1; i <= n; i++) {
        arr[i] = lower_bound(tmp + 1, tmp + len + 1, a[i]) - tmp;
    }

    root[0] = build(1, len);
    for (int i = 1; i <= n; i++) {
        cnt[arr[i]]++;
        root[i] = update(root[i - 1], 1, len, arr[i], cnt[arr[i]]);
    }

    while (m--) {
        int l, r, k;
        cin >> l >> r >> k;

        cout << query(1, len, root[l - 1], root[r], k) << "\n";
    }

}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t = 1;
    // cin >> t;

    while (t--) {
        solve();
    }

    return 0;
}
```
### 11.可持久化并查集
# P3402 【模板】可持久化并查集

## 题目描述

给定 $n$ 个集合，第 $i$ 个集合内初始状态下只有一个数，为 $i$。

有 $m$ 次操作。操作分为 $3$ 种：

 - `1 a b` 合并 $a,b$ 所在集合；

 - `2 k` 回到第 $k$ 次操作（执行三种操作中的任意一种都记为一次操作）之后的状态，保证已经进行过至少 $k$ 次操作（不算本次操作），特别的，若 $k=0$，则表示回到初始状态；

 - `3 a b` 询问 $a,b$ 是否属于同一集合，如果是则输出 $1$，否则输出 $0$。

## 输入格式

第一行两个整数，$n,m$。

接下来 $m$ 行，每行先输入一个数 $opt$。若 $opt=2$ 则再输入一个整数 $k$，否则再输入两个整数 $a,b$，描述一次操作。

## 输出格式

对每个操作 $3$，输出一行一个整数表示答案。
```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
using lll = __int128;
using ull = unsigned long long;

using PII = pair<int, int>;
using PLL = pair<long long ,long long>;

const int N = 1e5 + 5;
const int M = 2e5 + 5;
const int MAXN = 80 * N;

int n, m, idx;

int rootpe[M];
int rootsz[M];

int lf[MAXN];
int rt[MAXN];
int val[MAXN];

int buildpe(int l, int r) {
    int p = ++idx;
    if (l == r) {
        val[p] = l;
        return p;
    }

    int mid = l + r >> 1;
    lf[p] = buildpe(l, mid);
    rt[p] = buildpe(mid + 1, r);

    return p;
}

int buildsz(int l, int r) {
    int p = ++idx;
    if (l == r) {
        val[p] = 1;
        return p;
    }

    int mid = l + r >> 1;
    lf[p] = buildsz(l, mid);
    rt[p] = buildsz(mid + 1, r);

    return p;
}

int update(int k, int x, int l, int r, int i) {
    int p = ++idx;
    lf[p] = lf[i];
    rt[p] = rt[i];
    val[p] = val[i];
    if (l == r) {
        val[p] = x;
        return p;
    }

    int mid = l + r >> 1;
    if (k <= mid) lf[p] = update(k, x, l, mid, lf[i]);
    else rt[p] = update(k, x, mid + 1, r, rt[i]);

    return p;
}

int query(int k, int l, int r, int i) {
    if (l == r) {
        return val[i];
    }

    int mid = l + r >> 1;
    if (k <= mid) {
        return query(k, l, mid, lf[i]);
    } else {
        return query(k, mid + 1, r, rt[i]);
    }
}

int find(int u, int v) {
    int fa = query(u, 1, n, rootpe[v]);
    while (u != fa) {
        u = fa;
        fa = query(u, 1, n, rootpe[v]);
    }
    return u;
}

void  merge (int a, int b, int v) {
    a = find(a, v);
    b = find(b, v);

    if (a != b) {
        int sza = query(a, 1, n, rootsz[v]);
        int szb = query(b, 1, n, rootsz[v]);

        if (sza >= szb) {
            rootpe[v] = update(b, a, 1, n, rootpe[v]);
            rootsz[v] = update(a, sza + szb, 1, n, rootsz[v]);
        } else {
            rootpe[v] = update(a, b, 1, n, rootpe[v]);
            rootsz[v] = update(b, sza + szb, 1, n, rootsz[v]);
        }
    }
}

void solve() {
    cin >> n >> m;
    rootpe[0] = buildpe(1, n);
    rootsz[0] = buildsz(1, n);
    for (int i = 1; i <= m; i++) {
        int op;
        cin >> op;

        if (op == 1) {
            int a, b;
            cin >> a >> b;

            rootpe[i] = rootpe[i - 1];
            rootsz[i] = rootsz[i - 1];
            merge(a, b, i);
        } else if (op == 2) {
            int k;
            cin >> k;

            rootpe[i] = rootpe[k];
            rootsz[i] = rootsz[k];
        } else {
            int a, b;
            cin >> a >> b;

            rootpe[i] = rootpe[i - 1];
            rootsz[i] = rootsz[i - 1];
            if (find(a, i) == find(b, i)) {
                cout << "1\n";
            } else {
                cout << "0\n";
            }
        }
    }
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t = 1;
    // cin >> t;

    while (t--) {
        solve();
    }

    return 0;
}
```
### 12.可持久化tire树
# P6088 [JSOI2015] 字符串树

## 题目背景

萌萌买了一颗字符串树的种子，春天种下去以后夏天就能长出一棵很大的字符串树。字符串树很奇特，树枝上都密密麻麻写满了字符串，看上去很复杂的样子。

## 题目描述

字符串树本质上还是一棵树，即 $N$ 个节点 $N-1$ 条边的连通无向无环图，节点从 $1$ 到 $N$ 编号。与普通的树不同的是，树上的每条边都对应了一个字符串。萌萌和 JYY 在树下玩的时候，萌萌决定考一考 JYY。每次萌萌都写出一个字符串 $S$ 和两个节点 $U,V$，JYY 需要立即回答 $U$ 和 $V$ 之间的最短路径（即 $U,V$ 之间边数最少的路径，由于给定的是一棵树，这样的路径是唯一的）上有多少个字符串以 $S$ 为前缀。

JYY 虽然精通编程，但对字符串处理却不在行。所以他请你帮他解决萌萌的难题。

## 输入格式

输入第一行包含一个整数 $N$，代表字符串树的节点数量。

接下来 $N-1$ 行，每行先是两个数 $U,V$，然后是一个字符串 $S$，表示节点 $U$ 和节点 $V$ 之间有一条直接相连的边，这条边上的字符串是 $S$。输入数据保证给出的是一棵合法的树。

接下来一行包含一个整数 $Q$，表示萌萌的问题数。

接下来 $Q$ 行，每行先是两个数 $U,V$，然后是一个字符串 $S$，表示萌萌的一个问题是节点 $U$ 和节点 $V$ 之间的最短路径上有多少字符串以 $S$ 为前缀。

## 输出格式

输出 $Q$ 行，每行对应萌萌的一个问题的答案。

## 说明/提示

对于 $100\%$ 的数据，$1\leq N,Q\leq 10^5$，输入所有字符串长度不超过 $10$ 且只包含 `a~z` 的小写字母。
```cpp
#include <bits/stdc++.h>

using namespace std;

using ll = long long;
using lll = __int128;
using ull = unsigned long long;

using PII = pair<int, int>;
using PLL = pair<long long ,long long>;

const int N = 1e5 + 5;
const int MAXN = 30 * N;

int n, m, idx;

int root[N];
int cnt[MAXN];
int dep[N];
int fa[N][31];
int trie[MAXN][26];
vector<pair<int, string>> adj[N];

int update(string s, int i) {
    int p = ++idx;
    cnt[idx] = cnt[i] + 1;
    for (int j = 0; j < 26; j++) {
        trie[p][j] = trie[i][j];
    }

    for (int j = 0, pre = p; j < s.size(); j++) {
        trie[pre][s[j] - 'a'] = ++idx;
        i = trie[i][s[j] - 'a'];
        for (int k = 0; k < 26; k++) {
            trie[idx][k] = trie[i][k];
        }
        cnt[idx] = cnt[i] + 1;
        pre = idx;
    }

    return p;
}

void dfs(int u, int f) {
    fa[u][0] = f;
    dep[u] = dep[f] + 1;
    for (int i = 1; i <= 30; i++) {
        fa[u][i] = fa[fa[u][i - 1]][i - 1];
    }

    for (auto &[i, s] : adj[u]) {
        if (i == f) continue;
        root[i] = update(s, root[u]);
        dfs(i, u);
    }
}

int lca(int x, int y) {
    if (dep[x] > dep[y]) swap(x, y);
    int tmp = dep[y] - dep[x];
    for (int i = 0; tmp; i++, tmp>>= 1) {
        if (tmp & 1) {
            y = fa[y][i];
        }
    }

    if (x == y) return x;
    for (int i = 30; i >= 0; i--) {
        if (fa[x][i] != fa[y][i]) {
            x = fa[x][i];
            y = fa[y][i];
        }
    }

    return fa[x][0];
}

int query(int u, int v, string s) {
    int w = lca(u, v);
    u = root[u], v = root[v], w = root[w];

    int ans = 1e9;
    for (int i = 0; i < s.size(); i++) {
        u = trie[u][s[i] - 'a'];
        v = trie[v][s[i] - 'a'];
        w = trie[w][s[i] - 'a'];
    }

    ans = cnt[u] + cnt[v] - 2 * cnt[w];
    return ans;
}

void solve() {
    cin >> n;
    for (int i = 1; i < n; i++) {
        int u, v;
        string s;
        cin >> u >> v >> s;
        adj[u].push_back({v, s});
        adj[v].push_back({u, s});
    }

    root[1] = ++idx;
    dfs(1, 0);

    cin >> m;
    while (m--) {
        int u, v;
        string s;
        cin >> u >> v >> s;

        int ans = query(u, v, s);

        cout << ans << "\n";
    }

}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int t = 1;
    // cin >> t;

    while (t--) {
        solve();
    }

    return 0;
}
```

## 五.数学
### 1.矩阵快速幂
```cpp
struct ju{
    int m[N][N];
};
ju mul(ju &a,ju &b,int n){
    ju res;
    memset(res.m,0,sizeof(res.m));
    for(int i=0;i<n;i++){
        for(int k=0;k<n;k++){
            if(a.m[i][k]==0)continue;
            for(int j=0;j<n;j++){
                res.m[i][j]=(res.m[i][j]+1LL*a.m[i][k]*b.m[k][j])%M;
            }
        }
    }
    return res;
}
ju ksm(ju a,ll b,int n){
    ju res;
    memset(res.m,0,sizeof(res.m));
    for(int i=0;i<n;i++)res.m[i][i]=1;
    while(b){
        if(b&1)res=mul(res,a,n);
        a=mul(a,a,n);
        b>>=1;
    }
    return res;
}
```

### 2.线性基
```cpp
ll b[65];
void insert(ll x){
    for(int i=63;i>=0;i--){
        if((x>>i)&1){
            if(!b[i]){
                b[i]=x;
                return;
            }
            x^=b[i];
        }
    }
}
ll getma(){
    ll ans=0;
    for(int i=63;i>=0;i--){
        if((ans^b[i])>ans)ans^=b[i];
    }
    return ans;
}
```
### 3.Pollard-Rho(高效的质数判断方法)
# P4718 【模板】Pollard-Rho

## 题目描述

Miller Rabin 算法是一种高效的质数判断方法。虽然是一种不确定的质数判断法，但是在选择多种底数的情况下，正确率是可以接受的。

Pollard rho 是一个非常玄学的方式，用于在 $O(n^{\frac{1}{4}})$ 的期望时间复杂度内计算合数 $n$ 的某个非平凡因子。事实上算法导论给出的是 $O(\sqrt p)$，$p$ 是 $n$ 的某个最小因子，满足 $p$ 与 $\frac{n}{p}$ 互质。但是这些都是期望，未必符合实际。但事实上 Pollard rho 算法在实际环境中运行的相当不错。

这里我们要写一个程序，对于每个数字检验是否是质数，是质数就输出 `Prime`；如果不是质数，输出它最大的质因子是哪个。

## 输入格式

第一行，$T$ 代表数据组数（不大于 $350$）

以下 $T$ 行，每行一个整数 $n$，保证 $2 \le n \le {10}^{18}$。

## 输出格式

输出 $T$ 行。

对于每组测试数据输出结果。
```cpp
#include <bits/stdc++.h>
using namespace std;

typedef unsigned long long ull;
typedef __int128 lll;

// 快速乘（利用 __int128）
ull mul_mod(ull a, ull b, ull mod) {
    return (lll)a * b % mod;
}

// 快速幂
ull pow_mod(ull a, ull b, ull mod) {
    ull res = 1;
    while (b) {
        if (b & 1) res = mul_mod(res, a, mod);
        a = mul_mod(a, a, mod);
        b >>= 1;
    }
    return res;
}

// Miller-Rabin 素性测试（确定性基，适用于 64 位整数）
bool is_prime(ull n) {
    if (n < 2) return false;
    if (n == 2 || n == 3) return true;
    if (n % 2 == 0) return false;
    ull d = n - 1;
    int s = 0;
    while ((d & 1) == 0) {
        d >>= 1;
        s++;
    }
    for (ull a : {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37}) {
        if (a >= n) continue;
        ull x = pow_mod(a, d, n);
        if (x == 1 || x == n - 1) continue;
        bool composite = true;
        for (int r = 1; r < s; ++r) {
            x = mul_mod(x, x, n);
            if (x == n - 1) {
                composite = false;
                break;
            }
        }
        if (composite) return false;
    }
    return true;
}

// 二进制 GCD，比欧几里得更快
ull gcd(ull a, ull b) {
    if (a == 0) return b;
    if (b == 0) return a;
    int shift = __builtin_ctzll(a | b);
    a >>= __builtin_ctzll(a);
    do {
        b >>= __builtin_ctzll(b);
        if (a > b) swap(a, b);
        b -= a;
    } while (b);
    return a << shift;
}

// 无符号绝对差
ull diff(ull a, ull b) {
    return a > b ? a - b : b - a;
}

// Pollard-Rho (Brent 变体 + 批量 GCD)
ull pollard(ull n) {
    if (n % 2 == 0) return 2;
    if (n % 3 == 0) return 3;
    static mt19937_64 rng(chrono::steady_clock::now().time_since_epoch().count());
    while (true) {
        ull c = uniform_int_distribution<ull>(1, n - 1)(rng);
        ull x = uniform_int_distribution<ull>(2, n - 2)(rng);
        ull y = x;
        ull q = 1;
        ull g = 1;
        ull r = 1;
        auto f = [&](ull x) { return (mul_mod(x, x, n) + c) % n; };
        while (g == 1) {
            x = y;
            for (ull i = 0; i < r; ++i) y = f(y);
            ull k = 0;
            while (k < r && g == 1) {
                for (ull i = 0; i < min(128ULL, r - k); ++i) {
                    y = f(y);
                    q = mul_mod(q, diff(x, y), n);
                }
                g = gcd(q, n);
                k += 128;
            }
            r <<= 1;
        }
        if (g == n) {
            g = 1;
            do {
                x = f(x);
                g = gcd(diff(x, y), n);
            } while (g == 1);
        }
        if (g != n) return g;
    }
}

ull max_factor;

// 递归分解，更新最大质因子
void factor(ull n) {
    if (n <= max_factor) return;
    if (is_prime(n)) {
        max_factor = max(max_factor, n);
        return;
    }
    ull d = pollard(n);
    factor(d);
    factor(n / d);
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int T;
    cin >> T;
    while (T--) {
        ull n;
        cin >> n;
        // 根据题目，n >= 2；若出现 n=1 可特殊处理
        if (n == 1) {
            cout << 1 << '\n';
            continue;
        }
        if (is_prime(n)) {
            cout << "Prime" << '\n';
        } else {
            max_factor = 0;
            factor(n);
            cout << max_factor << '\n';
        }
    }
    return 0;
}
```

## 六.计算几何
### 1.凸包
```cpp
vector<point> get_tu(vector<point>a){
    sort(a.begin(),a.end(),[&](point u,point v){
        if(u.x==v.x)return u.y<v.y;
        return u.x<v.x;
    });
    vector<point>res;
    int top=-1;
    for(int i=0;i<a.size();i++){
        while(top>=1&&cross(res[top]-res[top-1],a[i]-res[top-1])<eps){
            top--;
            res.pop_back();
        }
        top++;
        res.push_back(a[i]);
    }
    int g=top;
    for(int i=a.size()-1;i>=0;i--){
        while(top>g&&cross(res[top]-res[top-1],a[i]-res[top-1])<eps){
            top--;
            res.pop_back();
        }
        top++;
        res.push_back(a[i]);
    }
    res.pop_back();
    return res;
}
```

### 2.前置知识,封装及函数
```cpp
#include<bits/stdc++.h>
using namespace std;

const double eps=1e-9;
const double PI=acos(-1.0);

// 符号判断
int sgn(double x){
    if(fabs(x)<eps)return 0;
    return x>0?1:-1;
}

//点
struct point{
    double x,y;
    point(){}
    point(double _x,double _y):x(_x),y(_y){}

    // 运算符重载
    point operator + (const point &b)const {return point(x+b.x,y+b.y);}
    point operator - (const point &b)const {return point(x-b.x,y-b.y);}
    point operator * (double k) const {return point(x*k,y*k);}
    point operator / (double k) const {return point(x/k,y/k);}
    
    // 比较运算符（主要用于 sort 去重 和 map/set 排序）
    bool operator == (const point &b)const {
        return sgn(x-b.x)==0&&sgn(y-b.y)==0; 
    }

    bool operator < (const point &b)const {
        return sgn(x-b.x)==0?sgn(y-b.y)<0:x<b.x;
    }

    double len() const { return hypot(x, y); }      // 模长（hypot防溢出）
    double len2() const { return x*x + y*y; }      // 模长平方（比len快，防精度）
    double angle() const { return atan2(y, x); }   // 极角
};

//点积
double dot(point a,point b){
    return a.x*b.x+a.y*b.y;
}
//叉积
double  cross(point a,point b){
    return a.x*b.y-a.y*b.x;
}
//长度
double dist(point a,point b){
    return (a-b).len();
}

//___________直线封装__________________
struct line{
    point s,e;// start(起点), end(终点) 或 直线上任意两点
    line(){}
    line(point _s,point _e):s(_s),e(_e){}

    // 获取方向向量
    point vec() const {return e-s;}
    // 获取单位方向向量（常用于步进）
    point unitvec() const {
        return vec()/vec().len();
    }
};

// 判断点P是否在线段AB上（含端点）
bool pointOnSegment(point p,line l){
    return sgn(cross(p-l.s,l.e-l.s))==0&& // 叉积为0：共线
        sgn(dot(p-l.s,p-l.e))<=0; // 点积<=0：在端点之间
}

// 点P到直线L的投影点（垂足）vvvvvvvvv
point projection(point p,line l){
    point v=l.e-l.s;
    // 如果l是点（长度为0），直接返回l.s，避免除以0
    if(sgn(v.len())==0)return l.s;
    double t=dot(p-l.s,v)/v.len2();// 投影系数t
    return l.s+v*t;
}

// 点P到直线L的距离
double distToLine(point p,line l){
    point v=l.e-l.s;
    if(sgn(v.len())==0)return dist(p,l.s);
    return fabs(cross(v,p-l.s))/v.len();
}

// 点P到线段L的距离（比到直线距离复杂一点）
double distToSegment(point p,line l){
    point v=l.e-l.s;
    if(sgn(v.len())==0)return dist(p, l.s);
    double t = dot(p - l.s, v) / v.len2();
    if (sgn(t) < 0) return dist(p, l.s);       // 投影在线段起点外
    if (sgn(t - 1) > 0) return dist(p, l.e);   // 投影在线段终点外
    return distToLine(p, l);                   // 投影在线段内部
}

// 求两直线交点（前提：必须保证不平行！）
point lineIntersection(line l1, line l2) {
    point v1 = l1.vec(), v2 = l2.vec();
    // 如果平行，cross(v1,v2)==0，此时不能调用此函数！
    double t = cross(l2.s - l1.s, v2) / cross(v1, v2);
    return l1.s + v1 * t;
}

// 判断两线段是否相交（含端点、含共线重叠）
bool segmentIntersect(line l1, line l2) {
    double c1 = cross(l1.vec(), l2.s - l1.s);
    double c2 = cross(l1.vec(), l2.e - l1.s);
    double c3 = cross(l2.vec(), l1.s - l2.s);
    double c4 = cross(l2.vec(), l1.e - l2.s);
    return sgn(c1) * sgn(c2) <= 0 && sgn(c3) * sgn(c4) <= 0;
}

//求多边形面积(鞋带公式)(凹多边形也适用)
double polygonArea(vector<point>& p){
    double area=0;
    int n=p.size();
    for(int i=0;i<n;i++){
        int j=(i+1)%n;
        area+=cross(p[i],p[j]);
    }
    return fabs(area)/2.0;
}

// 判断点P是否在多边形poly内部（含边界）
bool pointInPolygon(point p, vector<point>& poly){
    int n=poly.size();
    bool inside=false;
    for(int i=0,j=n-1;i<n;j=i++){
        if(pointOnSegment(p, line(poly[i], poly[j]))){
            return true;// 在边界上，算内部（题目如果要求“严格内部”，这里返回false）
        }

        // 【第二步】核心射线判断（只统计穿越，完美避开顶点重复计数）
        // 条件：(poly[i].y > p.y) != (poly[j].y > p.y)
        // 含义：边的两个端点，一个在射线上方，一个在下方（等于的情况被忽略了）
        if((sgn(poly[i].y-p.y)>0)!=(sgn(poly[j].y-p.y)>0)){
            // 计算交点横坐标 x_inter
            double x_inter=poly[i].x+(poly[j].x-poly[i].x)*(p.y-poly[i].y)/(poly[j].y-poly[i].y);
            // 如果交点在 P 的右侧，统计一次穿越
            if(sgn(x_inter-p.x)>0){
                inside=!inside;
            }
        }
    }
    return inside;
}
```
### 3.半平面交
# P4196 【模板】半平面交 / [CQOI2006] 凸多边形

## 题目描述

逆时针给出 $n$ 个凸多边形的顶点坐标，求它们交的面积。例如 $n=2$ 时，两个凸多边形如下图：

![](https://cdn.luogu.com.cn/upload/image_hosting/7ieux7g3.png)

则相交部分的面积为 $5.233$。

## 输入格式

第一行有一个整数 $n$，表示凸多边形的个数，以下依次描述各个多边形。第 $i$ 个多边形的第一行包含一个整数 $m_i$，表示多边形的边数，以下 $m_i$ 行每行两个整数，逆时针给出各个顶点的坐标。

## 输出格式

输出文件仅包含一个实数，表示相交部分的面积，保留三位小数。
```cpp
#include<bits/stdc++.h>
using namespace std;
const double eps=1e-9;
const int N=5005;

struct point{
    double x,y;
    point(){}
    point(double x,double y):x(x),y(y){}
    point operator +(const point &b)const {return point(x+b.x,y+b.y);}
    point operator -(const point &b)const {return point(x-b.x,y-b.y);}
    point operator *(double k)const {return point(k*x,k*y);}
};
struct line{
    point s,e;
    double ang;
    line(){}
    line(point s,point e):s(s),e(e){ang=atan2(e.y-s.y,e.x-s.x);}
    point vec()const {return e-s;}
};
double cross(point a,point b){
    return a.x*b.y-a.y*b.x;
}
double polygonArea(vector<point>&p){
    double area=0;
    int n=p.size();
    for(int i=0;i<n;i++){
        int j=(i+1)%n;
        area+=cross(p[i],p[j]);
    }
    return fabs(area)/2.0;
}
point jiao(line l1,line l2){
    point v1=l1.vec(),v2=l2.vec();
    double t=cross(l2.s-l1.s,v2)/cross(v1,v2);
    return l1.s+v1*t;
}
bool onright(line &a,line &b,line &c){
    point p=jiao(a,b);
    return cross(c.e-c.s,p-c.s)<-eps;
}
line l1[N];
double banjiao(vector<line>&ls){
    sort(ls.begin(),ls.end(),[&](line &x,line &y){
        if(fabs(x.ang-y.ang)<=eps)return cross(x.e-x.s,y.s-x.s)<0;
        return x.ang<y.ang;
    });
    int h=1,t=0;
    for(int i=0;i<ls.size();i++){
        if(i>0&&fabs(ls[i].ang-ls[i-1].ang)<eps)continue;
        while(h<t&&onright(l1[t-1],l1[t],ls[i]))t--;
        while(h<t&&onright(l1[h],l1[h+1],ls[i]))h++;
        l1[++t]=ls[i];
    }
    while(h<t&&onright(l1[t-1],l1[t],l1[h]))t--;
    while(h<t&&onright(l1[h],l1[h+1],l1[t]))h++;
    if(t-h+1<3){
       // cout<<"0.000"<<endl;
        return 0;
    }
    vector<point>tu;
    l1[++t]=l1[h];
    for(int i=h;i<t;i++){
        tu.push_back(jiao(l1[i],l1[i+1]));
    }
    double res=polygonArea(tu);
    return res;
}
int main(){
    int n;
    cin>>n;
    vector<line>ls;
    for(int i=0;i<n;i++){
        int m;
        cin>>m;
        vector<point>a(m);
        for(int i=0;i<m;i++)cin>>a[i].x>>a[i].y;
        for(int i=0;i<m;i++)ls.push_back(line(a[i],a[(i+1)%m]));
    }
    double res=banjiao(ls);
    printf("%.3lf",res);
}
```

### 4.角度
```cpp
#include <bits/stdc++.h>
using namespace std;

const double PI = acos(-1.0);
const double EPS = 1e-9;

struct Point {
    double x, y;
};

Point operator-(Point a, Point b) {
    return {a.x - b.x, a.y - b.y};
}

double dot(Point a, Point b) {
    return a.x * b.x + a.y * b.y;
}

double cross(Point a, Point b) {
    return a.x * b.y - a.y * b.x;
}

double length(Point a) {
    return sqrt(dot(a, a));
}

double clampd(double x, double l, double r) {
    return max(l, min(r, x));
}

// 两个向量的无向夹角，返回 [0, pi]
double angleBetween(Point a, Point b) {
    double la = length(a);
    double lb = length(b);
    if (la < EPS || lb < EPS) return 0; // 零向量，按题意处理

    double c = dot(a, b) / (la * lb);
    c = clampd(c, -1.0, 1.0); // 防止浮点误差导致 acos 越界
    return acos(c);
}

// 从向量 a 逆时针转到向量 b 的有向夹角，返回 (-pi, pi]
double orientedAngle(Point a, Point b) {
    return atan2(cross(a, b), dot(a, b));
}

// 从向量 a 逆时针转到向量 b 的角度，返回 [0, 2*pi)
double ccwAngle(Point a, Point b) {
    double t = orientedAngle(a, b);
    if (t < 0) t += 2 * PI;
    return t;
}

// 角 ABC，即 BA 与 BC 的夹角，返回 [0, pi]
double angleABC(Point A, Point B, Point C) {
    return angleBetween(A - B, C - B);
}

// 极角 / 方位角：从 x 轴正方向逆时针到 v，返回 [0, 2*pi)
double polarAngle(Point v) {
    double t = atan2(v.y, v.x);
    if (t < 0) t += 2 * PI;
    return t;
}

// 弧度转角度
double toDegree(double rad) {
    return rad * 180.0 / PI;
}
```
## 七.杂项
### 1.染色
# 1. 染色

## 一、通用定义

- 有 m 种颜色  
- 给 k 个节点排成环染色  
- 约束：相邻两个节点颜色不同，首尾也必须不同  

---

## 二、最终通用公式（背这个）

f(k) = (m - 1)^k + (-1)^k * (m - 1)

---

## 三、配套：线性链的公式（非环）

如果只是一条链，不闭合（首尾无约束）：

g(k) = m * (m - 1)^(k-1)



### 2.区间完美匹配方案数奇偶性
## 条件
- 给你 n 个区间 [l_i, r_i]。
- 要求从每个区间里选一个互不相同的数字（即完美匹配）。
- 只问方案数的奇偶性（输出 0 表示偶数，1 表示奇数），不问具体方案数。

## 结论
- 把每个区间 [l, r] 看成一条边，连接点 l-1 和点 r。
- 建完图后：
- 有环（包括重边）→ 答案是 0
无环（森林/树）→ 答案是 1

### 3.结论
# ICPC 猜结论训练题 - 结论总结（GitHub 兼容版）

## 题目一：混乱三人组（竞赛图）
### 题目背景
n 个选手进行单循环赛（任意两人之间无平局）。三人组成环（A>B, B>C, C>A）称为“混乱三人组”。求最多可能的混乱三人组数。

### 核心结论
设第 i 个人赢了 d_i 场，则：
- 总三人组数：C(n, 3)
- 非混乱三人组数（存在一人胜其他两人）：Σ_{i=1}^n C(d_i, 2)
- 混乱三人组数：C(n, 3) - Σ_{i=1}^n C(d_i, 2)

### 最优策略
通过局部调整法可证：胜场数越平均，非混乱数越小。即所有人的胜场只能为 ⌊(n-1)/2⌋ 或 ⌈(n-1)/2⌉。

### 最终公式
- n 为奇数：C(n,3) - n × C((n-1)/2, 2) = n(n²-1)/24
- n 为偶数：C(n,3) - (n/2) × C(n/2, 2) - (n/2) × C((n-2)/2, 2) = n(n²-4)/24

### 考点
组合数学、图论（竞赛图）、局部调整法。

---

## 题目二：受限爬楼梯
### 题目背景
从底部走到第 n 级台阶，每次跨 1 级或 2 级，但不能连续跨两次 2 级。求走法数。

### 核心结论
递推式为：a_n = a_{n-1} + a_{n-3}
初始值：a_1=1, a_2=2, a_3=3, a_4=4, a_5=6

### 推导逻辑
- DP 推导：设 dp[i][0] 为最后一步是 1 级，dp[i][1] 为最后一步是 2 级。
  dp[i][0] = a_{i-1}
  dp[i][1] = dp[i-2][0] = a_{i-3}
  a_i = dp[i][0] + dp[i][1] = a_{i-1} + a_{i-3}
- 组合直观：最后一步跨 1 级（a_{n-1}），或最后两步固定为 (1级, 2级)（a_{n-3}）。

### 矩阵快速幂优化
当 n 很大时（如 10^18），构造转移矩阵：
向量 V_n = (a_n, a_{n-1}, a_{n-2})^T
转移矩阵 M = 
[1, 0, 1]
[1, 0, 0]
[0, 1, 0]
利用矩阵快速幂，复杂度 O(log n)。

### 考点
简单 DP、组合数学、线性递推、矩阵快速幂。

---

## 题目三：修路方案（生成树计数）
### 题目背景
n 个带标号的城市，恰好修 n-1 条路使得所有城市连通（带标号生成树）。求方案数。

### 核心结论
方案数为 **凯莱公式 (Cayley's formula)**：n^(n-2)

### 推导逻辑
1. **Prüfer 序列**：长度为 n-2 的序列，每个位置有 n 种选择，共 n^(n-2) 种。
2. **一一对应**：每一棵树都能唯一编码为一个 Prüfer 序列，反之亦然（双射）。
3. 因此方案数为 n^(n-2)。

### 考点
图论、Prüfer 序列、组合数学（双射思想）。

---

## 题目四：分数拆分
### 题目背景
求方程 1/x + 1/y = 1/n 的正整数解 (x, y) 的个数（顺序不同算不同解）。

### 核心结论
解的个数等于 n^2 的正因子个数，即 d(n^2)。

### 推导逻辑
1. **代数变形**：通分得 n(x+y) = xy，凑乘积得 (x-n)(y-n) = n^2。
2. **双射**：x-n 和 y-n 是正整数，与 n^2 的正因子对 (a, b) 一一对应。
3. **数论公式**：若 n = p_1^e_1 * p_2^e_2 * ... * p_k^e_k，则
   d(n^2) = (2e_1 + 1)(2e_2 + 1) ... (2e_k + 1)。

### 考点
数论、代数变形、约数个数定理。

---

## 题目五：最大奇因子之和
### 题目背景
f(n) 为 n 的最大奇数因子，S(n) = Σ_{i=1}^n f(i)。求 S(n)。

### 核心结论
分治递推公式：
- n 为偶数：S(n) = n^2 / 4 + S(n / 2)
- n 为奇数：S(n) = (n+1)^2 / 4 + S((n-1) / 2)
  （注：C++ 中整除向下取整，奇数情况可统一写为 S(n) = k^2 + S(⌊n / 2⌋)，其中 k = ⌈n / 2⌉）

### 推导逻辑
将 S(2n) 拆分为奇数项和偶数项：
- 奇数项 f(2i-1) = 2i-1，求和为 n^2。
- 偶数项 f(2i) = f(i)，求和为 S(n)。
- 故 S(2n) = n^2 + S(n)，同理推导奇数情况。

### 复杂度
每次递归 n 减半，时间复杂度 O(log n)，可处理 n ≤ 10^18。

### 考点
数论、分治（Divide and Conquer）、递归。

---

## 教练总结
这一轮训练涵盖了 ICPC 中高频出现的核心套路：
1. **极值问题先转化**（如“混乱三人组”转求“非混乱数”）。
2. **相邻约束看末尾**（如“受限爬楼梯”拆最后两步）。
3. **树形计数找编码**（如 Prüfer 序列，把树转成序列）。
4. **分式方程凑乘积**（如“分数拆分”转约数个数）。
5. **复杂求和找分治**（如“最大奇因子”按奇偶拆分）。

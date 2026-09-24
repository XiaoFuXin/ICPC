
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

## 三.字符串
### 1.KMP
### 2.马拉车
### 3.trie树
### 4.AC自动机
### 5.回文自动机

## 四.数据结构
### 1.带权并查集
### 2.动态开点线段树
### 3.线段树
### 4.树链
### 5.st表
### 6.左偏树
### 7.替罪羊树

## 五.数学
### 1.矩阵快速幂
### 2.线性基

## 六.计算几何
### 1.凸包
### 2.前置知识,封装及函数

## 七.杂项
### 1.染色
### 2.区间完美匹配方案数奇偶性

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

## 六.计算几何
### 1.凸包
```cpp
// ========== 1. 整型坐标 (long long) ==========
struct PointLL { 
    long long x, y; 
    PointLL operator-(const PointLL& p) const { return {x - p.x, y - p.y}; }
};

// 向量叉积 (返回long long，坐标不超过1e9时安全，超1e9请自行替换内部为__int128)
long long cross(const PointLL& a, const PointLL& b) { 
    return a.x * b.y - a.y * b.x; 
}
long long cross(const PointLL& a, const PointLL& b, const PointLL& c) {
    return (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);
}


// ========== 2. 浮点坐标 (long double) ==========
struct PointLD { 
    long double x, y; 
    PointLD operator-(const PointLD& p) const { return {x - p.x, y - p.y}; }
};

long double cross(const PointLD& a, const PointLD& b) { 
    return a.x * b.y - a.y * b.x; 
}
long double cross(const PointLD& a, const PointLD& b, const PointLD& c) {
    return (b.x - a.x) * (c.y - a.y) - (b.y - a.y) * (c.x - a.x);
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

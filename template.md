# ICPC 算法模板 - XiaoFuXin

# 目录

## 一.准备
### 1.火车头
### 2.高精度

## 二.图论
### 1.最大流

## 三.字符串
### 1.KMP
### 2.马拉车

## 四.数据结构
### 1.带权并查集
### 2.动态开点线段树
### 3.线段树
### 4.树链

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

### 2.最大流
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
``cpp
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

# ICPC 算法模板 - XiaoFuXin

# 目录

## 一.准备






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

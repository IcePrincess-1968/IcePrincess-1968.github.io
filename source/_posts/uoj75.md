---
layout: post
date: 2019-01-06
title: "[UOJ #75][UR #6]智商锁 题解"
categories:
- [OI, 图论, 矩阵树定理]
- [OI, 常用技术, 高斯消元]
- [OI, 常用技术, 随机]
- [OI, 常用技术, 折半搜索]
- [OI, 数据结构, 哈希表]
- [OI, 数学, 线性代数, 行列式]
- [OI, 思维]
tags: [UOJ, 图论, 矩阵树定理, 高斯消元, 行列式, 随机, 思维, 折半搜索, 哈希表]
mathjax: true
---

## Original Description
- [Click to Open](http://uoj.ac/problem/75)

## Summarization
- $t$组询问,每次询问一个数$k$，要求构造一个点数不超过$100$的图，使得其生成树个数模$998244353$等于$k$。
- $t\leq 10$，$0\leq k\leq 998244352$。

<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
考虑如果有两个无向图$G_1$和$G_2$，其生成树个数分别为$C_1$和$C_2$，则我们可以把两个图的$1$号点捏在一起，其他点保持不变来获得一个新的图。在这个图中$1$号点是割点，所以其生成树个数等于$C_1C_2$。

考虑随机生成一些点数在$20$左右的图，用矩阵树定理计算其生成树个数。假设我们获得了两个图的集合$S_1$和$S_2$，每个集合都有$10^6$个随机图，那么我们从$S_1$和$S_2$中各挑一个图捏起来，可以获得$10^{12}$个不同的图（$10^{12}$个图对）。理论上有很大概率可以覆盖到所有可能出现的余数。现在要解决的问题是如何快速判断是否存在这样的一个图对。考虑折半搜索，在$S_1$中枚举一个图，假设它的生成树个数是$c$，则我们需要的第二个图的生成树个数是$k\cdot c^{-1}$，提前对第二个图建一个哈希表即可。

如果暴力的生成$S_1$和$S_2$，复杂度会达到$O(20^3\cdot 10^6)$，仍然不能接受。考虑和刚才相似的套路。我们先随机四个大小为$1000$的小集合$s_1,s_2,s_3,s_4$，然后$s_1$和$s_2$中的图两两合并得到$S_1$，$s_3$和$s_4$中的图两两合并得到$S_2$即可。

总时间复杂度$O(10^6\cdot log(998244353)+1000\cdot 20^3+t\cdot 1000000)$，其中的$log$来自求逆元的快速幂。
</details>

## Code
<details>
<summary>Click to view the code</summary>
```cpp
#include <bits/stdc++.h>
#include <unordered_map>
using namespace std;

#define LL long long
#define LB long double
#define ull unsigned long long
#define x first
#define y second
#define pb push_back
#define pf push_front
#define mp make_pair
#define Pair pair<int,int>
#define pLL pair<LL,LL>
#define pii pair<double,double>
#define LOWBIT(x) x & (-x)
// #define LOCAL true

const int INF=2e9;
const LL LINF=2e16;
const int magic=348;
const int MOD=998244353;
const double eps=1e-10;
const double pi=acos(-1);

struct fastio
{
    static const int S=1e7;
    char rbuf[S+48],wbuf[S+48];int rpos,wpos,len;
    fastio() {rpos=len=wpos=0;}
    inline char Getchar()
    {
        if (rpos==len) rpos=0,len=fread(rbuf,1,S,stdin);
        if (!len) return EOF;
        return rbuf[rpos++];
    }
    template <class T> inline void Get(T &x)
    {
        char ch;bool f;T res;
        while (!isdigit(ch=Getchar()) && ch!='-') {}
        if (ch=='-') f=false,res=0; else f=true,res=ch-'0';
        while (isdigit(ch=Getchar())) res=res*10+ch-'0';
        x=(f?res:-res);
    }
    inline void getstring(char *s)
    {
        char ch;
        while ((ch=Getchar())<=32) {}
        for (;ch>32;ch=Getchar()) *s++=ch;
        *s='\0';
    }
    inline void flush() {fwrite(wbuf,1,wpos,stdout);fflush(stdout);wpos=0;}
    inline void Writechar(char ch)
    {
        if (wpos==S) flush();
        wbuf[wpos++]=ch;
    }
    template <class T> inline void Print(T x,char ch)
    {
        char s[20];int pt=0;
        if (x==0) s[++pt]='0';
        else
        {
            if (x<0) Writechar('-'),x=-x;
            while (x) s[++pt]='0'+x%10,x/=10;
        }
        while (pt) Writechar(s[pt--]);
        Writechar(ch);
    }
    inline void printstring(char *s)
    {
        int pt=1;
        while (s[pt]!='\0') Writechar(s[pt++]);
    }
}io;

template<typename T> inline void check_max(T &x,T cmp) {x=max(x,cmp);}
template<typename T> inline void check_min(T &x,T cmp) {x=min(x,cmp);}
template<typename T> inline T myabs(T x) {return x>=0?x:-x;}
template<typename T> inline T gcd(T x,T y) {return y==0?x:gcd(y,x%y);}
inline int add(int x) {if (x>=MOD) x-=MOD;return x;}
inline int add(int x,int MO) {if (x>=MO) x-=MO;return x;}
inline int sub(int x) {if (x<0) x+=MOD;return x;}
inline int sub(int x,int MO) {if (x<0) x+=MO;return x;}
inline void Add(int &x,int y) {x=add(x+y);}
inline void Add(int &x,int y,int MO) {x=add(x+y,MO);}
inline void Sub(int &x,int y) {x=sub(x-y);}
inline void Sub(int &x,int y,int MO) {x=sub(x-y,MO);}
template<typename T> inline int quick_pow(int x,T y) {if (!x) return 0;int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
template<typename T> inline int quick_pow(int x,T y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

const int N=12;

int a[48][48];

struct Graph
{
    vector<Pair> edge;
    int n,det;
    inline void clear() {edge.clear();n=det=0;}
    inline void print()
    {
        printf("%d %d\n",n,int(edge.size()));
        for (auto item : edge) printf("%d %d\n",item.x,item.y);
    }
    inline Graph operator * (Graph other)
    {
        Graph res;res.clear();
        res.n=n+other.n-1;res.det=1ll*det*other.det%MOD;
        for (auto item : edge) res.edge.pb(item);
        for (register int i=0;i<int(other.edge.size());i++)
        {
            Pair ins=other.edge[i];ins.x+=n-1;ins.y+=n-1;
            res.edge.pb(ins);
        }
        return res;
    }
};

inline int Gauss()
{
    int fl=1,ans=1;
    for (register int i=1;i<=N-1;i++)
    {
        int pt=i;
        while (pt<=N-1 && !a[pt][i]) pt++;
        if (pt>N-1) return 0;
        if (pt!=i)
        {
            for (register int j=i;j<=N-1;j++) swap(a[i][j],a[pt][j]);
            fl=-fl;
        }
        ans=1ll*ans*a[i][i]%MOD;int inv=quick_pow(a[i][i],MOD-2);
        for (register int j=i+1;j<=N-1;j++)
            if (a[j][i])
            {
                int tmp=a[j][i];
                for (register int k=i;k<=N-1;k++)
                    a[j][k]=sub(a[j][k]-1ll*a[i][k]*inv%MOD*tmp%MOD);
            }
    }
    if (fl==-1) ans=sub(-ans);
    return ans;
}

inline Graph gen_graph()
{
    Graph res;
    memset(a,0,sizeof(a));
    for (register int i=1;i<=N-1;i++)
        for (register int j=i+1;j<=N;j++)
            if (rand()&1) Add(a[i][i],1),Add(a[j][j],1),Sub(a[i][j],1),Sub(a[j][i],1),res.edge.pb(mp(i,j));
    res.n=N;res.det=Gauss();return res;
}

vector<Graph> s[5];
vector<Pair> S[3];vector<int> inv;
unordered_map<int,int> Mp;

inline void gen_set()
{
    for (register int i=1;i<=4;i++)
    {
        s[i].clear();
        for (register int j=1;j<=600;j++)
        {
            Graph ins=gen_graph();
            if (ins.det) s[i].pb(ins);
        }
    }
    S[1].clear();S[2].clear();inv.clear();
    for (register int i=0;i<int(s[1].size());i++)
        for (register int j=0;j<int(s[2].size());j++)
        {
            int ndet=1ll*s[1][i].det*s[2][j].det%MOD;
            S[1].pb(mp(i,j));inv.pb(quick_pow(ndet,MOD-2));
        }
    for (register int i=0;i<int(s[3].size());i++)
        for (register int j=0;j<int(s[4].size());j++)
        {
            int ndet=1ll*s[3][i].det*s[4][j].det%MOD;
            S[2].pb(mp(i,j));
            Mp[ndet]=int(S[2].size())-1;
        }
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    srand(time(NULL));
    gen_set();int ca,k;
    scanf("%d",&ca);
    while (ca--)
    {
        scanf("%d",&k);bool f=false;
        if (k==0) {puts("2 0");continue;}
        for (register int i=0;i<int(S[1].size());i++)
        {
            int need=1ll*k*inv[i]%MOD;
            if (Mp.find(need)!=Mp.end())
            {
                int ind=Mp[need];
                int id1=S[1][i].x,id2=S[1][i].y,id3=S[2][ind].x,id4=S[2][ind].y;
                Graph g=s[1][id1];g=g*s[2][id2];g=g*s[3][id3];g=g*s[4][id4];
                g.print();f=true;break;
            }
        }
        if (!f) puts("QwQ");
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
</details>
---
layout: post
date: 2019-01-08
title: "[UOJ #37][清华集训2014]主旋律 题解"
categories:
- [OI, Dynamic Programming, 状压dp]
- [OI, 数学, 容斥原理]
tags: [dp, 状压dp, 容斥原理]
mathjax: true
---

## Original Description
- [Click to open](http://uoj.ac/problem/37)

## Summary
- 给定一个$n$个顶点$m$条边的有向图，求它有多少个生成子图是强连通的。
- $n\leq 15,m\leq n(n-1)$

<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
这是一道非常神的状压dp题。

正难则反，我们考虑用所有的生成子图减去不强联通的生成子图就能得到答案。

现在考虑数不强连通的生成子图个数。一个不强联通的生成子图在把SCC缩点后一定会得到一个DAG。考虑一个暴力的做法：我们枚举SCC的代表元素集合$S$，令$f[S]$表示缩点后SCC是$S$的情况下有多少种方案可以连出一个DAG。转移的话考虑到DAG中一定有一系列入度为$0$的点，我们枚举这个入度为$0$的集合，然后从这个入度为$0$的集合向补集任意连边，可以得到转移方程式
$$f[S]=\sum_{T\subset S,T\neq \emptyset}2^{cross[T][S-T]}f[S-T]$$
其中$cross[x][y]$表0示所有起始点在$x$中，结束点在$y$中的边的条数，形式化的，
$$cross[x][y]=\sum_{<u,v>\in E}[u\in x][v\in y]$$
仔细想想发现这个转移方程式是错的，因为我们是任意连边，没有保证$S-T$中所有的点入度都不为$0$。更具体的，对于某一个图，它的入度为$0$的点集的所有子集都会被我枚举到。那么不妨用容斥来解决这个问题。于是我们有正确的转移方程式
$$f[S]=\sum_{T\subset S,T\neq \emptyset}(-1)^{\mid T\mid-1}2^{cross[T][S-T]}f[S-T]$$
由于要枚举SCC，这个算法复杂度奇高。但是我们发现事实上我们只关心SCC个数的奇偶性从而确定容斥系数，而不关心SCC具体是谁。我们考虑令$g_k[T]$表示将点集$T$划分成$k$个SCC的方案数，重定义$f[S]$表示点集$S$能形成多少个不强联通生成子图。考虑枚举入度为$0$的集合$T$，把它划分成若干个SCC来转移，可以得到转移式
$$f[S]=\sum_{T\subset S,T\neq \emptyset}\sum_{k=1}^{\mid T\mid}(-1)^{k-1}g_k[T]2^{cross[T][S-T]}2^{cross[S-T][S-T]}$$
这里$2^{cross[S-T][S-T]}$的意义是除了这些入度为$0$的SCC，剩下的点可以随意胡闹，反正接在入度为$0$的点下面就行了。

将奇偶分开，可以写成
$$f[S]=\sum_{T\subset S,T\neq \emptyset}(\sum_{k=1}^{\frac{\mid T\mid+1}{2}}g_{2k-1}[T]-\sum_{k=1}^{\frac{\mid T\mid}{2}}g_{2k}[T])2^{cross[T][S-T]}2^{cross[S-T][S-T]}$$
令
$$p[S]=\sum_{k=1}^{\frac{\mid S\mid+1}{2}}g_{2k-1}[S]-\sum_{k=1}^{\frac{\mid S\mid}{2}}g_{2k}[S]$$
令$dp[S]$表示$S$这个点集的强连通生成子图的个数，也就是答案，我们发现$p[S]$可以由$dp[S]$转移来。考虑$S$中的某一个点$u$，我们枚举一个包含$u$的点的子集作为第一个SCC，则有转移方程式
$$p[S]=dp[S]-\sum_{T\subset S,u\in T}dp[T]p[S-T]$$
这个负号的意义是因为多了一个集合，所以奇偶交换，原来加的减，原来减的加。

至此我们已经获得了所有的转移
$$dp[S]=2^{cross[S][S]}-f[S]$$
$$f[S]=\sum_{T\subset S,T\neq \emptyset}p[T]2^{cross[T][S-T]}2^{cross[S-T][S-T]}$$
$$p[S]=dp[S]-\sum_{T\subset S,T\neq S,u\in T}dp[T]p[S-T]$$
至于$cross[x][y]$的处理，我们可以从$cross[x|y][0]$开始，每次把一个点从左边拉到右边来维护。

总时间复杂度$O(3^n)$
</details>

## Code
<details>
<summary>Click to view the code</summary>
```cpp
#include <bits/stdc++.h>
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
#define LOWBIT(x) ((x) & (-(x)))
// #define LOCAL true

const int INF=2e9;
const LL LINF=2e16;
const int magic=348;
const int MOD=1e9+7;
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
template<typename T> inline int quick_pow(int x,T y) {int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
template<typename T> inline int quick_pow(int x,T y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

const int MAXN=1e5;

int n,m;
int in[48],out[48],cross[MAXN+48];
int dp[MAXN+48],all[MAXN+48],p[MAXN+48];

int pw[MAXN+48],pos[MAXN+48],popcount[MAXN+48];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d%d",&n,&m);int x,y;
    for (register int i=1;i<=m;i++)
    {
        scanf("%d%d",&x,&y);
        in[y]|=(1<<(x-1));out[x]|=(1<<(y-1));
    }
    pw[0]=1;for (register int i=1;i<=n*(n-1);i++) pw[i]=add(pw[i-1]+pw[i-1]);
    for (register int i=1;i<=n;i++) pos[1<<(i-1)]=i;
    for (register int Mask=1;Mask<=(1<<n)-1;Mask++) popcount[Mask]=popcount[Mask>>1]+(Mask&1);
    dp[0]=all[0]=1;
    for (register int S=1;S<=(1<<n)-1;S++)
    {
        if (popcount[S]==1) {dp[S]=p[S]=all[S]=1;continue;}
        int tmp=0;
        for (register int i=1;i<=n;i++) if (S&(1<<(i-1))) tmp+=popcount[S&out[i]];
        all[S]=dp[S]=pw[tmp];
        int u=LOWBIT(S);
        for (register int sub=(S-u);;sub=(sub-1)&(S-u))
        {
            int T=(sub|u);
            if (T!=S) Sub(p[S],1ll*dp[T]*p[S-T]%MOD);
            if (!sub) break;
        }
        for (register int T=S;T;T=(T-1)&S)
        {
            if (T==S) cross[T]=0;
            else
            {
                int u=pos[LOWBIT(S-T)];
                cross[T]=cross[T+(1<<(u-1))]+popcount[in[u]&T]-popcount[out[u]&(S-T-(1<<(u-1)))];
            }
            Sub(dp[S],1ll*p[T]*pw[cross[T]]%MOD*all[S-T]%MOD);
        }
        Add(p[S],dp[S]);
    }
    printf("%d\n",dp[(1<<n)-1]);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
</details>
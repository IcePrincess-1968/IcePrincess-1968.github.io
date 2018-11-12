---
layout: post
date: 2018-11-11
title: "[NFLSoj #270][六校联合训练 #9]菊凯的疑惑 题解"
categories:
- [OI, Dynamic Programming, 树型dp]
- [OI, 常用技术, 高斯消元, 树上高消]
- [OI, 数学, 斯特林数, 第二类斯特林数]
- [OI, 数学, 容斥原理]
- [OI, 多项式, NTT]
tags: [NFLSoj, 树型dp, 树上高消, 第二类斯特林数, 多项式, 容斥原理]
mathjax: true
---

## Description
有一棵 $n$ 个节点的树，你将从任意一个点出发开始随机游走。具体来说，在点 $u$ 的每个单位时间内，你将会有 $p_u$ 的概率留在原地，有 $1−p_u$ 的概率等概率的向相邻的点移动，直到移动到 $1$ 号点才停下。

现在询问从每个点出发直至停下，所花费的时间的 $k$ 次方的期望。

<!-- more -->

可以证明，答案可以被表示成 $q\times p^{−1}$ 的形式，你需要输出一个正整数 $ans$，使得 $ans\equiv q\times p^{−1}(mod$&nbsp; $998244353)$。保证 $p$ 将不会是 $998244353$ 的倍数。

## Input Format
第一行两个整数 $n$ 和 $k$，含义如题目所示。

接下来 $n−1$ 行，每行两个整数 $u$,$v$,代表一条树边。

接下来一行，$n−1$ 个整数，第 $i$ 个数为 $p′_{i+1}$。$p_{i+1}=p′_{i+1}\times 10^{−6}$ ,代表留在原地的概率。

## Output Format
输出 $n−1$ 行，第 $i$ 个输出表示从 $i+1$ 号点出发直至停下，所花费的时间的 $k 次方的期望。

## Constraints

- 对于 15% 的数据，n,k≤10。

- 对于 30% 的数据，n,k≤50。

- 对于 50% 的数据，n≤1000，k≤100。

- 对于另外 5% 的数据，保证 k=0。

- 对于另外 15% 的数据，保证 k=1。

- 对于另外 15% 的数据，保证 pi=0。

- 对于 95% 的数据，k≤1000。

- 对于 100% 的数据，保证 1≤n≤1e5，0≤k≤1e5，n⋅k≤1e6，0≤p′i<1e60。

## Solution

~~这果真是noip模拟题~~

一个显然的思路是树型dp,我们令$f_{u,i}$表示从$u$出发到根的路径长度的$i$次方的期望。(注意这个和长度期望的$i$次方是两码事)

我们先考虑简单的情况，$i=1$时怎么做。

可以轻易地列出状态转移方程(其中$deg_u$表示节点$u$的度数)
$$f_{u,1}=p_u(f_{u,1}+1)+\frac{(1-p_u)}{deg_u}\sum_{(u,v)\in E}(f_{v,1}+1)$$

注意到这个方程既要从孩子转移来也要从父亲转移来，所以没有明显的阶段性，需要高斯消元。针对树上的高斯消元有树上高消的黑科技可以在线性时间内解出所有的变量，后面再讲。

接下来考虑一般的情况，一个比较显然的想法是利用二项式定理展开，就是暴力展开$(f_{u,i}+1)^k$类似的东西，注意展开后的每一项都要用对应的次方的期望。这样我们可以得到状态转移方程
$$f_{u,i}=\sum_{j=0}^i\binom{i}{j}(p_uf_{u,j}+\frac{(1-p_u)}{deg_u}\sum_{(u,v)\in E}f_{v,j})$$

这样我们可以$O(k)$的转移，加上$O(nk)$个状态，总时间复杂度是$O(nk^2)$，大概可以拿70分。

~~然后就不会了~~

接下来的部分比较神奇

考虑如下等式

$$x^k=\sum_{j=0}^{k}\binom{x}{j}S(k,j)j!$$

其中$S(k,j)$表示第二类斯特林数。

可以这样简证上述等式的正确性：等式的左边可以看做将$k$个不同的球放入$x$个不同的盒子中,每个球都有$x$种放法所以是$x^k$，右边可以看做先枚举有$j$个盒子是有球的，从$x$个盒子中先选出$j$个盒子保证非空，然后假装这$j$个盒子是不可分辨的，那么将球放入的方案数就是第二类斯特林数，最后再乘上$j$个盒子的排列数。

有了这个式子我们发现，我们令$x^k$表示从某一点出发到根的时间的$k$次方的期望，我们只要求出所有的$\binom{x}{i}$的期望就可以了。

我们修改之前定义的状态，令$f_{u,i}$表示从$u$出发到根，所用时间$t$的$\binom{t}{i}$的期望。我们发现组合数有一个非常好的性质：$\binom{t}{i}=\binom{t-1}{i}+\binom{t-1}{i-1}$，这就比二项式定理的递推要很多，因为我们的递推式只有两项，转移的复杂度从$O(k)$降到了$O(1)$。

写出新的状态转移方程：
$$f_{u,i}=p_u(f_{u,i}+f_{u,i-1})+\frac{(1-p_u)}{deg_u}\sum_{(u,v)\in E} (f_{v,i}+f_{v,i-1})$$

~~是时候来化成可以树上高消的形式了~~我们先拆拆括号移项整理，可得
$$(1-p_u)f_{u,i}=\frac{(1-p_u)}{deg_u}\sum_{(u,v)\in E}f_{v,i}+p_uf_{u,i-1}+\frac{(1-p_u)}{deg_u}\sum_{(u,v)\in E}f_{v,i-1}$$
$$f_{u,i}=\frac{\sum_{(u,v)\in E}f_{v,i}}{deg_u}+\frac{p_u}{1-p_u}f_{u,i-1}+\frac{\sum_{(u,v)\in E}f_{v,i-1}}{deg_u}$$

由于我们是按$i$从小到大递推的，所以所有下标为$i-1$的项都是已知项，为了简化表达，我们令
$$g_{u,i}=\frac{p_u}{1-p_u}f_{u,i-1}+\frac{\sum_{(u,v)\in E}f_{v,i-1}}{deg_u}$$

则原式可以写成
$$f_{u,i}=\frac{\sum_{(u,v)\in E}f_{v,i}}{deg_u}+g_{u,i}$$

上式中所有的$v$里面有一个是$u$的父亲(我们暂时不考虑根节点),其他的都是$u$的儿子，我们把父亲单独提出来:
$$f_{u,i}=\frac{\sum_{v\in son_u}f_{v,i}}{deg_u}+\frac{f_{fa_u,i}}{deg_u}+g_{u,i}$$

稍微想一下可以知道，所有的叶节点是没有儿子的，所以它们的$f$值只和父亲相关，倒数第二层的节点，它们的孩子都是叶子节点，可以用它们自己来表示，所以它们也只和父亲相关……依次类推，每个节点的$f$值都是关于父亲的$f$值的一次函数，设$f_{u,i}=k_uf_{fa_u,i}+b_u$，我们要推出$k_u$和$b_u$的表达式。

对于上式，我们把所有的$f_{v,i}$换成$k_vf_{u,i}+b_v$,有
$$f_{u,i}=\frac{\sum_{v\in son_u}(k_vf_{u,i}+b_v)}{deg_u}+\frac{f_{fa_u,i}}{deg_u}+g_{u,i}$$

$$(1-\frac{\sum_{v\in son_u}k_v}{deg_u})f_{u,i}=\frac{f_{fa_u,i}}{deg_u}+g_{u,i}+\frac{\sum_{v\in son_u}b_v}{deg_u}$$

$$f_{u,i}=\frac{1}{deg_u-\sum_{v\in son_u}k_v}f_{fa_u,i}+\frac{\sum_{v\in son_u}b_v}{deg_u-\sum_{v\in son_u}k_v}+g_{u,i}$$

所以
$$ \left\{
\begin{aligned}
k_u &= \frac{1}{deg_u-\sum_{v\in son_u}k_v} \\
b_u &= \frac{\sum_{v\in son_u}b_v}{deg_u-\sum_{v\in son_u}k_v}+g_{u,i}
\end{aligned}
\right.
$$

这样只要从下到上算一遍$k_u$和$b_u$，再根据$f_{root,i}=0$从上到下推一遍就解出了所有的未知数。

这样可以得到95分，还有一个问题是暴力推第二类斯特林数是$O(n^2)$的。这里有一个喜闻乐见的多项式做法。

考虑第二类斯特林数的通项公式:
$$S(n,k)=\frac{1}{k!}\sum_{i=0}^k(-1)^i\binom{k}{i}(k-i)^n$$

这个公式是基于容斥的，考虑枚举至少有$i$个盒子是空的，从$k$个盒子中选出$i$个空盒子是$\binom{k}{i}$，$n$个球每个球有$(k-i)$种放置方案，可得到上式。

对式子做一些变换
$$
\begin{aligned}
S(n,k) &= \frac{1}{k!}\sum_{i=0}^k(-1)^i(k-i)^n\frac{k!}{i!(k-i)!} \\
&= \sum_{i=0}^k\frac{(k-i)^n}{(k-i)!}\frac{(-1)^i}{i!}
\end{aligned}
$$

这显然是一个卷积的形式，用$NTT$加速即可做到$O(klogk)$

总时间复杂度$O(nk+klogk)$

~~终于完结撒花了~~

## Code
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
template<typename T> inline int quick_pow(int x,T y) {int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
template<typename T> inline int quick_pow(int x,T y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

const int MAXN=4e5;

namespace polynomial
{
    const int G=3;
    const int NTT_MAX=8e5;
    int wn_pos[NTT_MAX+48],wn_neg[NTT_MAX+48];
    inline void poly_init()
    {
        for (register int clen=2;clen<=524288;clen<<=1)
            wn_pos[clen]=quick_pow(G,(MOD-1)/clen),wn_neg[clen]=quick_pow(G,(MOD-1)-(MOD-1)/clen);
    }
    inline void poly_ntt(int c[],int len,int fl)
    {
        int i,j,k;
        for (i=(len>>1),j=1;j<len;j++)
        {
            if (i<j) swap(c[i],c[j]);
            for (k=(len>>1);i&k;k>>=1) i^=k;i^=k;
        }
        for (register int clen=2;clen<=len;clen<<=1)
        {
            int wn=(fl==1?wn_pos[clen]:wn_neg[clen]);
            for (j=0;j<len;j+=clen)
            {
                int w=1;
                for (k=j;k<j+(clen>>1);k++)
                {
                    int tmp1=c[k],tmp2=1ll*c[k+(clen>>1)]*w%MOD;
                    c[k]=add(tmp1+tmp2);c[k+(clen>>1)]=sub(tmp1-tmp2);
                    w=1ll*w*wn%MOD;
                }
            }
        }
        if (fl==-1)
        {
            int ilen=quick_pow(len,MOD-2);
            for (i=0;i<len;i++) c[i]=1ll*c[i]*ilen%MOD;
        }
    }
    inline void poly_mul(int A[],int B[],int n,int m,int C[])
    {
        int len=1;while (len<=n+m) len<<=1;
        memset(A+n,0,(len-n)*sizeof(int));
        memset(B+m,0,(len-m)*sizeof(int));
        poly_ntt(A,len,1);poly_ntt(B,len,1);
        for (register int i=0;i<len;i++) C[i]=1ll*A[i]*B[i]%MOD;
        poly_ntt(C,len,-1);
    }
}

int fac[MAXN+48],ifac[MAXN+48];
inline void init_fac()
{
    fac[0]=1;for (register int i=1;i<=MAXN;i++) fac[i]=1ll*fac[i-1]*i%MOD;
    ifac[MAXN]=quick_pow(fac[MAXN],MOD-2);
    for (register int i=MAXN-1;i>=0;i--) ifac[i]=1ll*ifac[i+1]*(i+1)%MOD;
}

int A[MAXN+48],B[MAXN+48],s[MAXN+48];

int n,k;
vector<int> v[MAXN+48];
int d[MAXN+48],p[MAXN+48],dinv[MAXN+48],pinv[MAXN+48];

vector<int> f[MAXN+48];int g[MAXN+48];

int K[MAXN+48],b[MAXN+48];
inline void dfs(int cur,int father)
{
    int ksum=0,bsum=0;
    for (register int i=0;i<int(v[cur].size());i++)
    {
        int y=v[cur][i];
        if (y!=father)
            dfs(y,cur),Add(ksum,K[y]),Add(bsum,b[y]);
    }
    K[cur]=quick_pow(sub(d[cur]-ksum),MOD-2);
    b[cur]=add(1ll*K[cur]*bsum%MOD+1ll*K[cur]*d[cur]%MOD*g[cur]%MOD);
}

inline void Dfs(int cur,int father,int step)
{
    if (cur==1) f[cur][step]=0; else f[cur][step]=add(b[cur]+1ll*K[cur]*f[father][step]%MOD);
    for (register int i=0;i<int(v[cur].size());i++)
    {
        int y=v[cur][i];
        if (y!=father) Dfs(y,cur,step);
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
    polynomial::poly_init();init_fac();
    io.Get(n);io.Get(k);int x,y;
    for (register int i=1;i<=n-1;i++)
    {
        io.Get(x);io.Get(y);
        v[x].pb(y);v[y].pb(x);d[x]++;d[y]++;
    }
    int Inv=quick_pow(int(1e6),MOD-2);
    for (register int i=2;i<=n;i++) io.Get(p[i]),p[i]=1ll*p[i]*Inv%MOD;
    for (register int i=1;i<=n;i++) dinv[i]=quick_pow(d[i],MOD-2),pinv[i]=quick_pow(p[i],MOD-2);
    for (register int i=1;i<=n;i++) f[i].resize(k+10,0),f[i][0]=1;
    for (register int i=1;i<=k;i++)
    {
        for (register int j=2;j<=n;j++)
        {
            g[j]=1ll*p[j]*quick_pow(sub(1-p[j]),MOD-2)%MOD*f[j][i-1]%MOD;
            for (auto y : v[j]) Add(g[j],1ll*f[y][i-1]*dinv[j]%MOD);
        }
        dfs(1,-1);Dfs(1,-1,i);
    }
    for (register int i=0;i<=k;i++) A[i]=1ll*ifac[i]*((i&1)?(MOD-1):1)%MOD;
    for (register int i=0;i<=k;i++) B[i]=1ll*ifac[i]*quick_pow(i,k)%MOD;
    polynomial::poly_mul(A,B,k+1,k+1,s);
    for (register int i=0;i<=k;i++) s[i]=1ll*s[i]*fac[i]%MOD;
    for (register int i=2;i<=n;i++)
    {
        int ans=0;
        for (register int j=0;j<=k;j++) Add(ans,1ll*f[i][j]*s[j]%MOD);
        printf("%d\n",ans);
    } 
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
---
layout: post
date: 2018-12-15
title: "[AtCoder4417][JAGSC2018]Short LIS 题解"
categories: 
- [OI, 思维]
- [OI, 数学]
tags: [AtCoder, 思维]
mathjax: true
---

## Problem Statement

You are given three integers N, A, and B.

Let $P=(P_0,P_1,…,P_{N−1})$ be a permutation of $(0,1,…,N−1)$. $P$ is said good if and only if it satisfies all of the following conditions:

- The length of a longest increasing subsequence of $P$ is at most $2$.

- $P_A$=$B$

Count the number of good permutations modulo 1e9+7.

<!-- more -->

## Constraints
- $1\leq N \leq 10^6$
- $0\leq A\leq N-1$
- $0\leq B\leq N-1$

## Input

Input is given from Standard Input in the following format:

    N A B

## Output
Print the number of good permutations modulo 1e9+7.

## Sample Input 1
    3 0 0

## Sample Output 1
    1
The only good permutation is $(0,2,1)$.

## Sample Input 2
    12 2 3

## Sample Output 2
    5390

## Sample Input 3
    10000 9875 5431

## Sample Output 3
    135608808

## Solution

非常神的一道题

我们考虑一个permutation, 把所有是前缀最小值的点拎出来，我们会发现剩下的数列也一定是一个下降序列，因为任意两个前缀最小值之间如果有一对升序的点，和前面的前缀最小值合起来就形成了一个长度为3的上升序列从而不合法。

设这些前缀最小值为$(a_1,p_{a_1}),(a_2,p_{a_2})\dots(a_k,p_{a_k})$,我们将这些点在平面上画出来，然后两两之间先向下走再向右走，我们我们可以得到一条从$(0,n)$到$(n,0)$的路径，这条路径始终在正方形左上-右下对角线的下方。我们发现，**这种路径和原序列是一一对应的**。因而我们只要对这样的路径进行计数即可。

如果我们将坐标轴旋转$45^。$,可以把问题变成这样：从$(0,0)$出发，每次位移的向量是$(1,1)$或$(1,-1)$且任意时刻必须在$x$轴上方，最终到达$(n,0)$，求路径条数。

这是非常著名的Dyck Path,在不加限制的情况下路径的条数是卡特兰数的第$n$项。

现在考虑$P_A=B$的限制。我们比较希望$P_A$是一个前缀最小值，这样我们就能在路径上明确的找到他。注意到如果$A+B>n-1$的话$P_A$肯定不是前缀最小值，此时我们可以做变换$a=n-1-a,b=n-1-b$，相当于将整个序列reverse之后再将每个数取补。

如果$A+B\leq n-1$的话我们可以证明合法的序列$P_A$一定是前缀最小值。考虑反证法，假设存在一个$C<A$使得$P_C<P_A$,那么可以得到$P_A$后面的数必须都比$P_A$小。因此序列中比$P_A$小的数至少有$(n-1)-A+1=n-A$个，因为$P_A=B$,所以有$n-A\leq B$，即$A+B\geq n$，与之前的$A+B\leq n-1$矛盾。

于是我们只要求过点$(A,P_A)$的Dyck Path有多少条。考虑到$P_A$是前缀最小值，所以一定有这样一个局部$(A,P_A+1)\Rightarrow(A,P_A)\Rightarrow(A+1,P_A)$，所以我们只要分别统计从$(0,n)$到$(A,P_A+1)$和从$(A+1,P_A)$到$(n,0)$的Dyck Path即可。

这种变种的Dyck Path有一种经典优美的求法：

根据终点的横纵坐标差，我们可以得到有多少次向上，多少次向下。不妨设$n$次向上,$m$次向下$(n\geq m)$。先不考虑必须在$x$轴上方的条件，这就是在$n+m$次中选择$n$次向上，$C_{n+m}^n$。再考虑扣除那些到过$x$轴以下的路径。这些路径必然和$y=-1$相交。考虑该路径和$y=-1$的第一个交点以左的部分，我们将这个部分关于$y=-1$对称，可以得到一条从$(0,-2)$出发的路径。接而我们发现刚才那些不合法的路径和这些从$(0,-2)$到目标点的路径一一对应。从$(0,-2)$到目标点，纵坐标多了2，相当于向上$n+1$次，向下$m-1$次，所以这部分有$C_{n+m}^{n+1}$条。综上，合法的Dyck Path共有$C_{n+m}^n-C_{n+m}^{n+1}$

~~完结撒花~~

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

const int MAXN=3e6;

int fac[MAXN+48],ifac[MAXN+48];
inline void init_inv()
{
    fac[0]=1;for (register int i=1;i<=MAXN;i++) fac[i]=1ll*fac[i-1]*i%MOD;
    ifac[MAXN]=quick_pow(fac[MAXN],MOD-2);
    for (register int i=MAXN-1;i>=0;i--) ifac[i]=1ll*ifac[i+1]*(i+1)%MOD;
}

inline int C(int x,int y)
{
    if (x<y) return 0;
    return 1ll*fac[x]*ifac[y]%MOD*ifac[x-y]%MOD;
}

int n,a,b;

inline int calc(int x,int y)
{
    if (x<y) return 0;
    return C(x,(x+y)>>1);
}

inline int solve(int x,int y)
{
    int xx=n-(y-x),yy=n-(x+y);
    return sub(calc(xx,yy)-calc(xx,yy+2));
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    init_inv();
    scanf("%d%d%d",&n,&a,&b);
    if (a+b>n-1) a=n-1-a,b=n-1-b;
    printf("%d\n",1ll*solve(a,b+1)*solve(b,a+1)%MOD);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
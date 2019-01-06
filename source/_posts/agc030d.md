---
layout: post
date: 2019-01-05
title: "[AtCoder Grand Contest 030D]Inversion Sum 题解"
categories: [OI, Dynamic Programming]
tags: [AtCoder, dp]
mathjax: true
---

## Original Description
- [Click to Open](https://agc030.contest.atcoder.jp/tasks/agc030_d)

## Summarization

- 给定一个数列$A_n$,有$q$次操作，第$i$次操作可以选择交换或不交换$A_{X_i}$和$A_{Y_i}$。问所有的$2^q$种操作方法得到的序列的逆序对个数之和是多少。
- $n,q\leq 3000$

<!-- more -->
  
## Solution
<details>
<summary>Click to view the solution</summary>
如果直接对操作进行dp感觉不可行。考虑每一对位置$i$和$j$会在多少种操作结果中成为逆序对。

令$dp[t][i][j]$表示$t$次操作后，$A_i>A_j$的操作方案数。转移的话考虑这次操作$(x,y)$，我们分三种情况讨论：

1. $i$和$j$都不等于$x$或$y$，此时这次操作与$i$和$j$无关，$dp[t][i][j]=dp[t-1][i][j]\times 2$
2. $i$和$j$分别为$x$和$y$，即$i=x,j=y$或$i=y,j=x$，则$dp[t][i][j]=dp[t-1][i][j]+dp[t-1][j][i]$
3. $i$和$j$中有一个是$x$或$y$，以$i=x$为例，则$dp[t][i][j]=dp[t-1][i][j]+dp[t-1][y][j]$

这样我们得到了一个$O(n^3)$的dp。还需要优化。

我们发现第一类转移有$O(n^2)$种，而第二和第三种转移只有$O(n)$种。考虑修改一下dp的定义。令$dp[t][i][j]$表示$t$次操作之后，$a_i>a_j$的概率。这个定义和刚才的区别在于转移的时候都要除以$2$。这样我们有$O(n^2)$中情况是不需要转移的，每次转移的代价降低到$O(n)$，从而总时间复杂度降低到$O(n^2)$。最后只要对于所有的$i<j$累加$dp[q][i][j]\times 2^q$即可。
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

const int MAXN=3000;

int n,q,a[MAXN+48];
int dp[MAXN+48][MAXN+48];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d%d",&n,&q);int l,r,inv2=quick_pow(2,MOD-2);
    for (register int i=1;i<=n;i++) scanf("%d",a+i);
    for (register int i=1;i<=n;i++)
        for (register int j=1;j<=n;j++)
            dp[i][j]=(a[i]>a[j]);
    for (register int ope=1;ope<=q;ope++)
    {
        scanf("%d%d",&l,&r);
        dp[l][r]=dp[r][l]=1ll*add(dp[l][r]+dp[r][l])*inv2%MOD;
        for (register int i=1;i<=n;i++)
        {
            if (i==l || i==r) continue;
            dp[i][l]=dp[i][r]=1ll*add(dp[i][l]+dp[i][r])*inv2%MOD;
            dp[l][i]=dp[r][i]=1ll*add(dp[l][i]+dp[r][i])*inv2%MOD;
        }
    }
    int ans=0;
    for (register int i=1;i<=n-1;i++)
        for (register int j=i+1;j<=n;j++)
            Add(ans,dp[i][j]);
    ans=1ll*ans*quick_pow(2,q)%MOD;
    printf("%d\n",ans);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
</details>
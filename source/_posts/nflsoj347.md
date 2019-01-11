---
layout: post
date: 2018-12-28
title: "[NFLSoj #347][Jiangsu Training Contest #8]计数题 题解"
categories:
- [OI, Dynamic Programming, 背包]
- [OI, 常用技术, 二进制拆分]
tags: [NFLSoj, dp, 背包]
mathjax: true
---


## Description

给大家做一道计数题。

现在有 $n$（$n\leq 50$） 种面值的货币，第 $i$ 种面值是 $s_i$（$s_i\leq 500$）。保证任意两种面值都不同，即当 $i\neq j$ 时 $s_i\neq s_j$。

如果每种面值的货币你都有足够多张，问有多少种方案拼出总面值 $C$（$C\leq 10^{100}$）。两种方案不同当且仅当某一种面值的货币的数量不同。输出答案模 $10^9+7$ 的结果。

<!-- more -->

## Input Format

第一行两个正整数 $n$,$C$，分别表示货币面值的种数和你需要拼的面值。

第二行 $n$ 个正整数 $s_i$。

## Output Format

输出一行答案，模 $10^9+7$。

## Sample Input 1
    2 10
    3 7

## Sample Output 1
    1

## Sample Input 2
    3 10
    3 4 7

## Sample Output 2
    2

## Constraints
对于 $10\%$ 的数据，$n\leq 5，C\leq 100$；

对于另 $10\%$ 的数据，$C\leq 10^5$；

对于另 $10\%$ 的数据，$C\leq 10^9$；

对于另 $20\%$ 的数据，$C\leq 10^{18}$；

对于另 $10\%$ 的数据，$n=2$；

对于另 $10\%$ 的数据，$n=3$；

对于 $100\%$ 的数据，$1\leq n\leq 50$，$1\leq s_i\leq 500$，$C\leq 10^{100}$。

## Solution

对于前$20$分，就是一个比较简单的完全背包，做到$O(nC)$的复杂度是不困难的。

满分的做法比较神奇。

原题相当于求方程$\sum_{i=1}^ns_ix_i=C$的解的个数。

我们把$C$转成二进制数，把所有的$x_i$也看做二进制数，从高位到低位逐位确定二进制位。

令$dp[i][j][k]$表示当前从高位到低位考虑到表示$2^i$的那一位，当前考虑到第$j$个数，$\frac{C-\sum_{i=1}^ns_ix_i}{2^i}=k$的方案数。$k$那一维定义得比较拗口，通俗来讲就是刚开始所有的$x_i$都是$0$，$C$也是$0$，然后从高位到低位一位位添加$C$的二进制位，然后更新已经添加的这些位中，现在还剩余的$C$是$2^i$的多少倍（显然是整数倍）。

根据$\sum_{i=0}^{n}2^i<2^{n+1}$可知，如果$2\times \sum_{i=1}^ns_i<k$,这个状态是无效的。所以$k$这一维最大是$\sum s_i$级别的。

每一层的转移就是普通的背包转移，从$i$到$i-1$的时候，当前的$2^i$可以变成$2\times 2^{i-1}$，相当于$k\times 2$，再更新上下一位$C$的取值，就可以转移了。

本质上相当于做一个二进制的背包。总时间复杂度$O(n^2\sum s_ilogC)$

~~讲的不是很清楚，看代码吧~~

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

int n;char s[548];
int a[148],bit[1048],tot;

inline void TEN_TO_TWO()
{
    int len=strlen(s+1);
    for (register int i=1;i<=len;i++) s[i]-='0';
    reverse(s+1,s+len+1);tot=0;
    while (len)
    {
        bit[++tot]=(s[1]&1);
        for (register int i=len;i>=1;i--)
        {
            if (s[i]&1 && i>1) s[i-1]+=10;
            s[i]/=2;
        }
        while (len && s[len]==0) len--;
    }
}

int dp[2][58][60048];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d%s",&n,s+1);int sum=0;
    for (register int i=1;i<=n;i++) scanf("%d",a+i),sum+=a[i];
    TEN_TO_TWO();int cur=0,nxt=1;dp[cur][0][1]=1;
    for (register int i=tot;i>=1;i--,cur^=1,nxt^=1)
    {
        memset(dp[nxt],0,sizeof(dp[nxt]));
        for (register int j=0;j<=n-1;j++)
            for (register int k=0;k<=2*sum;k++)
                if (dp[cur][j][k])
                {
                    Add(dp[cur][j+1][k],dp[cur][j][k]);
                    if (k-a[j+1]>=0) Add(dp[cur][j+1][k-a[j+1]],dp[cur][j][k]);
                }
        for (register int k=0;k*2+bit[i-1]<=2*sum;k++)
            if (dp[cur][n][k]) Add(dp[nxt][0][k*2+bit[i-1]],dp[cur][n][k]);
    }
    printf("%d\n",dp[cur][0][0]);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
---
layout: post
date: 2018-10-27
title: "[NFLSoj #260][六校联合训练 #7]环 题解"
categories: 
- [OI, 常用技术, 容斥]
- [OI, 图论]
tags: [NFLSoj, 容斥原理, 图论]
mathjax: true
---

## Description

对于一张 n 个点的竞赛图 G，其中 e 条边的方向已经确定，剩下的边朝向是两边等概率的。

求 G 中最小环的期望个数对 1e9+7 取模的结果。

特别地，若 G 无环，则我们认为这种情况下最小环个数为 0。

<!-- more -->

## Input

第一行两个数 n,e。

接下来 e 行，每行两个数 x,y 表示一条确定的边。

## Output

输出一行一个数表示答案。

## Sample 1 & 2

见下发文件。

## Constraints

对于 30% 的数据，n≤300。

对于另 20% 的数据，e=0。

对于另 20% 的数据，e=n(n−1)/2。

对于 100% 的数据，1≤n≤1e5，1≤e≤1e6。

## Solution

竞赛图有这样一个性质：整张图要么是一个DAG，要么存在三元环。因为考虑每一个三元组(i,j,k)，根据抽屉原理三条边至少有两条边是同向的，此时第三条边如果和前两条同向就形成了三元环，否则就是一个DAG。(这里同向指顺时针和逆时针)

于是这题变成了竞赛图三元环计数，有一种线性的做法：考虑容斥，所有的无序三元组(i,j,k)的个数是$C_N^3$，考虑减去那些不是三元环的三元组，我们发现对于一个不是三元环的三元组，有且仅有一个点的度数为2，我们考虑在这种点上统计个数。

我们枚举每一个点，将其出边两两搭配就能得到所有的非三元环情况，注意每个点的n-1条边中有些边确定是出边，设数量为$p$，有些边确定不是出边，还有一些边没有定向，有$\frac{1}{2}$的概率是出边，设数量为$q$，于是一个点的答案是$\frac{p(p-1)}{2}+p\cdot \frac{q}{2}+\frac{q(q-1)}{2}\cdot \frac{1}{4}$

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

const int MAXN=1e5;

int n,e;
int cnt[MAXN+48],deg[MAXN+48];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    io.Get(n);io.Get(e);
    int x,y;
    for (register int i=1;i<=e;i++)
    {
        io.Get(x);io.Get(y);
        cnt[x]++;cnt[y]++;deg[x]++;
    }
    int i2=quick_pow(2,MOD-2),i8=1ll*i2*i2%MOD*i2%MOD,i6=quick_pow(6,MOD-2);
    int ans=1ll*n*(n-1)%MOD*(n-2)%MOD*i6%MOD;
    for (register int i=1;i<=n;i++)
    {
        Sub(ans,1ll*deg[i]*(deg[i]-1)%MOD*i2%MOD);
        int doubt=(n-1)-cnt[i];
        Sub(ans,1ll*deg[i]*doubt%MOD*i2%MOD);
        Sub(ans,1ll*doubt*(doubt-1)%MOD*i8%MOD);
    }
    io.Print(ans,'\n');
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
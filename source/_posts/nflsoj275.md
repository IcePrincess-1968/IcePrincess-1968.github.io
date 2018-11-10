---
layout: post
date: 2018-11-01
title: "[NOIP模拟赛]b 题解"
categories:
- [OI, 常用技术, 根号暴力]
tags: [NOIP模拟赛, 根号暴力]
mathjax: true
---

## Description

给定一个二分图，统计满足$1\leq x<y\leq n,1\leq a<b\leq m$且$(x,a),(x,b),(y,a),(y,b)$之间均有边的四元组(x,y,a,b)的个数

## Constraint
- $1\leq n,m \leq 200,000,1\leq k \leq min(n*m,300,000)$
  
<!-- more -->

## Solution

考虑根号暴力，对于二分图左边的点，如果出边大于$\sqrt{m}$称之为大点，否则称之为小点。

考虑左边两个小点，两个大点和一小一大两个点三种匹配情况。

对于小点和小点的情况，我们考虑对于每个小点$x$向右边的出边集合，枚举所有可能的$(a,b)$，其中$a<b$,这样我们只要统计右边的每一个点对$(a,b)$被覆盖了多少次即可计算。

这个部分可以无脑的用map做，但会多一个log,考虑一种比较精巧的做法，对于每个点对$(a,b)$,我们在下标为a的vector里面添加b,之后我们统计每个vector内每种相同的数有多少个，再$C_x^2$一下就好了。

接下来考虑左边的两个点中有一个是大点的情况，我们枚举每个大点，将它的出边所到达的右边的点标记为１，然后枚举左边的每一个点，看看当前点的出边所到达的右边的点有多少个被标记了，假设第$i$个点有$x_i$个，那么当前大点的贡献就是$\sum_{i=1}^nC_{x_i}^2$，注意扣除一下重复计算的情况。

计算一下时间复杂度。第二部分的时间复杂度比较容易证明，考虑到大点的个数不会超过$\sqrt m$个，所有大点的出边条数的和是$O(m)$的，所以第二部分的时间复杂度是$O(m\sqrt m)$

第一部分的时间复杂度比较难算，我们尝试抽象这个问题：有一个整数集合$\{a_n\}$，要求$\sum a_i\leq m$且$\forall a_i\leq \sqrt m$，我们要证明$\sum {a_i}^2$的最大值是$O(m\sqrt m)$级别的。

我们发现如下性质：如果集合$\{a_i\}$中存在两个元素$a_1$,$a_2$满足$a_1,a_2\leq \frac{\sqrt m}{2}$，则删除这两个元素将这两个元素的和放入，目标函数的值一定更大，因为$a^2+b^2<{(a+b)}^2$。所以满足目标函数最大的集合，一定对于$\forall i$有$\frac{\sqrt m}{2}\leq a_i\leq \sqrt m$，所以集合的大小$n\leq 2\sqrt m$，从而$\sum {a_i}^2$是$O(m\sqrt m)$级别的。

综上总时间复杂度$O(m\sqrt m)$

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
#define LOCAL true

const int INF=2e9;
const LL LINF=2e16;
const int magic=448;
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

const int MAXN=2e5;

int n,m,k;bool type[MAXN+48];
vector<int> v[MAXN+48];

vector<int> ed[MAXN+48];
int cnt[MAXN+48];
inline LL doit_small()
{
    LL res=0;
    for (register int i=1;i<=n;i++)
        if (!type[i])
        {
            for (register int p1=0;p1<int(v[i].size())-1;p1++)
                for (register int p2=p1+1;p2<int(v[i].size());p2++)
                {
                    int v1=v[i][p1],v2=v[i][p2];
                    if (v1>v2) swap(v1,v2);
                    ed[v1].pb(v2);
                }
        }
    for (register int i=1;i<=m;i++)
    {
        for (register int j=0;j<int(ed[i].size());j++)
            res+=cnt[ed[i][j]],cnt[ed[i][j]]++;
        for (register int j=0;j<int(ed[i].size());j++) cnt[ed[i][j]]--;
    }
    return res;
}

int mark[MAXN+48];
inline LL doit_big()
{
    LL res=0;
    for (register int i=1;i<=n;i++)
        if (type[i])
        {
            for (register int j=0;j<int(v[i].size());j++) mark[v[i][j]]=1;
            for (register int j=1;j<=n;j++)
            {
                if (type[j] && j<=i) continue;
                int cnt=0;
                for (register int p=0;p<int(v[j].size());p++) cnt+=mark[v[j][p]];
                res+=1ll*cnt*(cnt-1)/2;
            }
            for (register int j=0;j<int(v[i].size());j++) mark[v[i][j]]=0;
        }
    return res;
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("b.in","r",stdin);
    freopen ("b.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    int x,y;io.Get(n);io.Get(m);io.Get(k);
    for (register int i=1;i<=k;i++)
    {
        io.Get(x);io.Get(y);
        v[x].pb(y);
    }
    for (register int i=1;i<=n;i++) if (int(v[i].size())<=magic) type[i]=false; else type[i]=true;
    printf("%lld\n",doit_small()+doit_big());
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
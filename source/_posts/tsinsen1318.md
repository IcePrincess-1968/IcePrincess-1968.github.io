---
layout: post
date: 2019-01-15
title: "[Tsinsen A1318]Almost(艾雨青) 题解"
categories:
- [OI, 常用技术, 分块]
- [OI, 思维]
- [OI, 常用技术, 二分]
- [OI, 计算几何, 凸包]
tags: [Tsinsen, 分块, 二分, 凸包]
mathjax: true
---

## Original Description
- [Click to open](http://www.tsinsen.com/A1318)

## Summary
- 定义一个序列$A_n$的几乎平均数为$\frac{\sum_{i=1}^nA_i}{n-1}$。
- 现在给定一个序列$A_n$，有$q$次查询，每次查询一个区间$[l,r]$，问$[l,r]$这个区间内几乎平均数最大的子区间的几乎平均数是多少。
- $n\leq 10^5,q\leq 3\times 10^4$。
<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
考虑对原序列求前缀和，则区间$[l,r]$的几乎平均数可以写成$\frac{sum[r]-sum[l-1]}{r-l}$。可以看到这很像一个求两点之间斜率的形式。那么问题可以转化为在$[l,r]$内取两个点，按照上述公式计算斜率后斜率最大。

于是我们有一个朴素的想法：对于一次询问$[l,r]$，我们从右向左依次将点加入，枚举到某一个点的时候，我们以它为左端点，在右侧找一个点使其与之匹配斜率最大。这样做复杂度是$O(n^2q)$的，需要优化。

我们考虑之前枚举左端点后，事实上不用枚举右边的点。我们可以发现，能使得斜率最大的点一定在右边的点所形成的上凸壳中。相当于我们要在上凸壳上找一个点使其与左端点连线的斜率最大。我们发现这个斜率是一个单峰函数，考察相邻两项的差值，我们只要在上凸壳上二分，找到第一个差小于$0$的位置即可。然后将当前点加入单调栈，用单调栈维护凸壳即可。这样复杂度降到$O(qnlogn)$，仍然需要优化。

考虑分块。我们预处理$f[i][j]$表示考虑从第$i$个块开头到第$j$个点的这段区间（或者从第$j$个点到第$i$个块末尾）内斜率最大的点对。以前者为例，$f[i][j]$显然可以从$f[i][j-1]$转移来。然后考虑第$j$个点作为右端点的贡献，像上述一样维护左边点的凸壳，在凸壳上二分即可。注意如果查询点在右边维护的是下凸壳。这个预处理的复杂度是$O(n\sqrt nlogn)$。

接下来考虑查询$[l,r]$。令$belong[i]$表示第$i$个点所属的块的编号。分以下情况讨论：
- $belong[l]=belong[r]$，此时直接暴力构建这不超过$\sqrt n$个点的凸壳即可。
- $belong[l]\neq belong[r]$，我们考虑答案可能的几种情况：
  - 左端点在$belong[l]+1$之后，这时的最值在$f[belong[l]+1][r]$中处理过了。
  - 右端点在$belong[r]-1$之前，这时的最值在$f[belong[r]-1][l]$中处理过了。
  - 左端点在$belong[l]$这个块内且右端点在$belong[r]$这个块内。此时可能作为左端点和右端点的点都不超过$\sqrt n$个，我们暴力构建右端点所在块的凸壳，拉着所有左端点所在块的点二分即可。

查询时间复杂度$O(q\sqrt nlogn)$，从而算法总时间复杂度为$O((n+q)\sqrt nlogn)$。
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
const int magic=967;
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

int n,q,a[MAXN+48];LL sum[MAXN+48];
int N;

struct Seg
{
    int dx;LL dy;
    Seg () {}
    inline Seg(int _x,LL _y) {dx=_x;dy=_y;}
    inline bool operator < (const Seg &other) const {return dy*other.dx<other.dy*dx;}
    inline void print()
    {
        LL up=dy,down=dx;
        if (up==0) {puts("0/1");return;}
        LL g=gcd(myabs(up),myabs(down));
        up/=g;down/=g;
        if (down<0) up=-up,down=-down;
        printf("%lld/%lld\n",up,down);
    }
};

inline Seg gen(pLL pt1,pLL pt2) {return Seg(pt1.x-pt2.x,pt1.y-pt2.y);}

Seg f[110][MAXN+1];
pLL sta[MAXN+48];int stot;
int bel[MAXN+48];

inline pLL query(pLL cur)
{
    int l=1,r=stot;
    while (l<=r)
    {
        int mid=(l+r)>>1;
        if (mid<stot && gen(cur,sta[mid])<gen(cur,sta[mid+1])) l=mid+1; else r=mid-1;
    }
    return sta[l];
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d%d",&n,&q);int l,r;
    for (register int i=1;i<=n;i++) scanf("%d",a+i),sum[i]=sum[i-1]+a[i];
    N=n/magic;if (n%magic) N++;
    for (register int i=1,id=1;i<=n;i++)
    {
        bel[i]=id;
        if (i%magic==0) id++;
    }
    for (register int i=1;i<=N;i++)
    {
        int st=(i-1)*magic+1;
        if (st!=n)
        {
            stot=2;sta[1]=mp(st,sum[st-1]);sta[2]=mp(st+1,sum[st]);
            f[i][st+1]=gen(mp(st,sum[st-1]),mp(st+1,sum[st+1]));
            for (register int j=st+2;j<=n;j++)
            {
                f[i][j]=f[i][j-1];
                pLL chosen=query(mp(j,sum[j]));
                if (f[i][j]<gen(chosen,mp(j,sum[j]))) f[i][j]=gen(chosen,mp(j,sum[j]));
                sta[++stot]=mp(j,sum[j-1]);
                while (stot>=3 && gen(sta[stot-1],sta[stot])<gen(sta[stot-2],sta[stot-1])) sta[stot-1]=sta[stot],stot--;
            }
        }
        int ed=((i==N)?n:i*magic);
        if (ed!=1)
        {
            stot=2;sta[1]=mp(ed,sum[ed]);sta[2]=mp(ed-1,sum[ed-1]);
            f[i][ed-1]=gen(mp(ed-1,sum[ed-2]),mp(ed,sum[ed]));
            for (register int j=ed-2;j>=1;j--)
            {
                f[i][j]=f[i][j+1];
                pLL chosen=query(mp(j,sum[j-1]));
                if (f[i][j]<gen(mp(j,sum[j-1]),chosen)) f[i][j]=gen(mp(j,sum[j-1]),chosen);
                sta[++stot]=mp(j,sum[j]);
                while (stot>=3 && gen(sta[stot],sta[stot-1])<gen(sta[stot-1],sta[stot-2])) sta[stot-1]=sta[stot],stot--;
            }
        }
    }
    while (q--)
    {
        scanf("%d%d",&l,&r);
        if (bel[l]+1>=bel[r])
        {
            stot=2;sta[1]=mp(r,sum[r]);sta[2]=mp(r-1,sum[r-1]);
            Seg ans=gen(mp(r-1,sum[r-2]),mp(r,sum[r]));
            for (register int i=r-2;i>=l;i--)
            {
                pLL chosen=query(mp(i,sum[i-1]));
                if (ans<gen(mp(i,sum[i-1]),chosen)) ans=gen(mp(i,sum[i-1]),chosen);
                sta[++stot]=mp(i,sum[i]);
                while (stot>=3 && gen(sta[stot],sta[stot-1])<gen(sta[stot-1],sta[stot-2])) sta[stot-1]=sta[stot],stot--; 
            }
            ans.print();continue;
        }
        Seg ans=f[bel[l]+1][r];
        if (ans<f[bel[r]-1][l]) ans=f[bel[r]-1][l];
        int st=(bel[r]-1)*magic+1;
        stot=1;sta[1]=mp(r,sum[r]);
        for (register int i=r-1;i>=st;i--)
        {
            sta[++stot]=mp(i,sum[i]);
            while (stot>=3 && gen(sta[stot],sta[stot-1])<gen(sta[stot-1],sta[stot-2])) sta[stot-1]=sta[stot],stot--;
        }
        int ed=magic*bel[l];
        for (register int i=l;i<=ed;i++)
        {
            pLL chosen=query(mp(i,sum[i-1]));
            if (ans<gen(mp(i,sum[i-1]),chosen)) ans=gen(mp(i,sum[i-1]),chosen);
        }
        ans.print();
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
</details>
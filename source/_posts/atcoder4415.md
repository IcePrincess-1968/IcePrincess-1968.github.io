---
layout: post
date: 2018-12-16
title: "[AtCoder4415][JAGSC2018]ADD DIV MAX RESTORE 题解"
categories:
- [OI, 数据结构, 线段树]
tags: [AtCoder, 线段树]
mathjax: true
---

## Problem Statement

You are given an integer sequence $a_0,a_1,…,a_{N−1}$.

You have to perform $Q$ queries, each query is one of the following:

- ```ADD QUERY(t=0 l r x)``` : for each $i$ between $l$ and $r$, inclusive, replace $a_i$ with $a_i+x$.
- ```DIV QUERY(t=1 l r x)``` : for each i between $l$ and $r$, inclusive, replace ai with $floor(a_i⁄x)$, where $floor(y)$ is the biggest integer that is not greater than $y$.
- ```MAX QUERY(t=2 l r x=0)``` : print $max(a_l,a_{l+1},…,a_r)$.
- ```RESTORE QUERY(t=3 l r x=0)``` : for each $i$ between $l$ and $r$, inclusive, set $a_i$ to the initial value of $a_i$, that is, the value given in the input.

<!-- more -->

## Constraints

- All input values are integers.
- $1\leq N,Q\leq 200,000$
- $0\leq a_i\leq 10^8$
- $t_i=0,1,2,3$
- $0\leq l_i\leq r_i\leq N−1$
- $1\leq x_i\leq 1000$(when $t_i\neq 2,3$)

## Input

Input is given from Standard Input in the following format:

    N Q
    a0 a1 ... aN−1
    t1 l1 r1 x1
    t2 l2 r2 x2
    :
    tQ lQ rQ xQ

## Output

For each MAX QUERY, print $max(a_l,a_{l+1},…,a_r)$, one per line.

## Sample Input 1
    5 9
    1 2 3 4 5
    2 0 4 0
    0 0 1 10
    2 0 4 0
    2 2 2 0
    1 0 1 4
    2 0 0 0
    2 1 1 0
    3 0 4 0
    2 0 1 0

## Sample Output 1
    5
    12
    3
    2
    3
    2
- $max(1,2,3,4,5)=5$
- $1,2,3,4,5\rightarrow 11,12,3,4,5$
- $max(11,12,3,4,5)=12$
- $max(3)=3$
- $11,12,3,4,5\rightarrow 2,3,3,4,5$
- $max(2)=2$
- $max(3)=3$
- The array is restored to $1,2,3,4,5$
- $max(1,2)=2$

## Sample Input 2
    4 7
    0 1 0 1
    2 0 3 0
    0 0 3 1
    1 0 3 2
    2 0 3 0
    0 0 3 1
    1 0 3 2
    2 0 3 0

## Sample Output 2
    1
    1
    1

## Sample Input 3
    10 23
    13 1 22 8 28 18 23 9 22 27
    1 3 4 5
    1 8 8 8
    0 3 9 5
    0 2 6 3
    3 0 4 0
    1 1 3 7
    2 2 2 0
    2 3 5 0
    0 1 4 2
    3 0 9 0
    2 0 1 0
    0 3 9 8
    2 1 9 0
    0 8 9 5
    1 5 7 7
    0 3 5 7
    0 7 9 7
    3 3 6 0
    2 1 6 0
    0 1 1 7
    1 4 8 10
    2 0 9 0
    1 5 6 1

## Sample Output 3
    3
    28
    13
    36
    28
    47

## Solution

看到这个除法的时候以为是吉司机线段树相关，但又有RESTORE操作，不知道该怎么弄。

听了题解发现很神。除法难就难在不知道该怎么在线段树上维护懒标记。我们可以像这样维护标记：
$$x\rightarrow \frac{x+d-a}{d}+e$$

这里的$a\in [0,d-1]$,否则可以除出去加到$e$上

首先考虑加法操作，这个比较简单，直接在$e$上加就可以了

然后考虑除法操作，设要除以x,我们先通分：
$$\frac{d-a}{d}+e=\frac{d-a+de}{d}$$

现在新的除数变成了$dx$,我们拿$d-a+de$整除$dx$的结果作为新的$e$，拿$dx$减模数作为新的$a$即可。

这里要注意的是$dx$一直乘下去可能会爆long long，但因为加法操作的和不会超过$10^8$,所以$dx$和$1e9$取一个min就可以了。

最后考虑标记的合并。因为每次都会有pushdown操作，所以父节点的标记一定是后于子节点的，所以拿着父节点的标记对着子节点先加，再除，最后加即可。

这样这题剩下的部分就是基本的线段树操作了。至于RESTORE操作，可以额外维护一个flushed标记表示是否被清空。

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

const int INF=1e9;
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

const int MAXN=2e5;

int n,q;
int a[MAXN+48];

namespace SegmentTree
{
    struct Tag
    {
        LL a,d,e;
        Tag () {}
        inline void clear() {a=1;d=1;e=0;}
        inline Tag(LL _a,LL _d,LL _e) {a=_a;d=_d;e=_e;}
        inline Tag operator + (LL other) {return Tag(a,d,e+other);}
        inline Tag operator / (LL other)
        {
            LL tmp=d-a+d*e,nd=d*other;
            LL ne=tmp/nd,na=nd-tmp%nd;
            check_min(nd,1ll*INF);check_min(na,1ll*INF);
            return Tag(na,nd,ne);
        }
        inline void merge(Tag other)
        {
            Tag tmp=(*this);
            tmp=(tmp+(other.d-other.a))/other.d+other.e;
            (*this)=tmp;
        }
        inline int getval(int x) {return (x+d-a)/d+e;}
    };
    Tag lazy[MAXN*4];int ori[MAXN*4],maxn[MAXN*4];bool flushed[MAXN*4];
    inline void pushup(int cur)
    {
        maxn[cur]=max(maxn[cur<<1],maxn[cur<<1|1]);
    }
    inline void pushdown(int cur)
    {
        if (flushed[cur])
        {
            flushed[cur<<1]=flushed[cur<<1|1]=true;
            lazy[cur<<1].clear();lazy[cur<<1|1].clear();
            maxn[cur<<1]=ori[cur<<1];maxn[cur<<1|1]=ori[cur<<1|1];
            flushed[cur]=false;
        }
        lazy[cur<<1].merge(lazy[cur]);lazy[cur<<1|1].merge(lazy[cur]);
        maxn[cur<<1]=lazy[cur].getval(maxn[cur<<1]);
        maxn[cur<<1|1]=lazy[cur].getval(maxn[cur<<1|1]);
        lazy[cur].clear();
    }
    inline void build(int cur,int l,int r)
    {
        lazy[cur].clear();flushed[cur]=false;
        if (l!=r)
        {
            int mid=(l+r)>>1;
            build(cur<<1,l,mid);build(cur<<1|1,mid+1,r);
            pushup(cur);ori[cur]=maxn[cur];
        }
        else
        {
            maxn[cur]=ori[cur]=a[l];
        }
    }
    inline void modify(int op,int cur,int left,int right,int x,int l,int r)
    {
        if (left<=l && r<=right)
        {
            if (!op) {lazy[cur]=lazy[cur]+x;maxn[cur]+=x;return;}
            if (op==1) {lazy[cur]=lazy[cur]/x;maxn[cur]/=x;return;}
            if (op==3) {flushed[cur]=true;maxn[cur]=ori[cur];lazy[cur].clear();return;}
        }
        pushdown(cur);int mid=(l+r)>>1;
        if (left<=mid) modify(op,cur<<1,left,right,x,l,mid);
        if (mid+1<=right) modify(op,cur<<1|1,left,right,x,mid+1,r);
        pushup(cur);
    }
    inline int query(int cur,int left,int right,int l,int r)
    {
        if (left<=l && r<=right) return maxn[cur];
        pushdown(cur);int mid=(l+r)>>1,res=-1;
        if (left<=mid) check_max(res,query(cur<<1,left,right,l,mid));
        if (mid+1<=right) check_max(res,query(cur<<1|1,left,right,mid+1,r));
        pushup(cur);return res;
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
    scanf("%d%d",&n,&q);
    for (register int i=1;i<=n;i++) scanf("%d",a+i);
    SegmentTree::build(1,1,n);
    int op,l,r,x;
    while (q--)
    {
        scanf("%d%d%d%d",&op,&l,&r,&x);++l;++r;
        if (op!=2) SegmentTree::modify(op,1,l,r,x,1,n); else printf("%d\n",SegmentTree::query(1,l,r,1,n));
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
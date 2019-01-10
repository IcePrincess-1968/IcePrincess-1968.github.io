---
layout: post
date: 2019-01-10
title: "[CodeChef JUMP]Jump Mission 题解"
categories:
- [OI, 常用技术, cdq分治]
- [OI, Dynamic Programming]
- [OI, 数据结构, 线段树, 李超树]
tags: [CodeChef, cdq分治, dp, 线段树, 李超树]
mathjax: true
---

## Original Description
- [Click to open](https://www.codechef.com/OCT15/problems/JUMP)

## Summary
- 有$n$座山峰，第$i$座山峰高度为$H_i$，有两个属性值$P_i,A_i$。一个人初始在$1$号山峰，要到$n$号山峰。一个人能从$i$号山峰跳到$j$号山峰当且仅当$i<j$且$P_i<P_j$，要花费$(H_i-H_j)^2$的代价。每到一座山峰$i$(包括$1$号山峰)，这个人都要额外花费$A_i$的代价。问这个人最少需要多少花费。
- $n\leq 300000,H_i\leq 600000$，$P_i$是一个$1$到$n$的排列。

<!-- more -->

## Solution
<details>
<summary>Click to view the solution</summary>
这个看上去很像dp+斜率优化，先写出转移方程式:
$$dp[i]=min_{j=1}^{i-1}dp[j]+(H_i-H_j)^2+A_i$$
将括号打开，我们有
$$dp[i]=min_{j=1}^{i-1}dp[j]+A_j+H_i^2+H_j^2-2H_iH_j$$
接着我们发现了一个问题：如果化成了斜率优化的式子，右边的$H_i$没有单调性。~~然后就自闭了~~

尝试从别的角度考虑。我们观察这个转移方程式，发现右边可以写成一个一次函数的形式。除了只和$i$有关的项，剩下的是$-2H_jx+dp[j]+H_j^2$。$H_i$相当于自变量。这样我们只要维护$1$到$j-1$的这些直线，然后决策$i$的时候求这些直线在$H_i$处的最小值即可。插入直线和查询某点处的最小值，我们可以用李超树实现。

现在还有$P_i<P_j$才能转移的条件。这个和[NFLSoj 比赛](https://iceprincess-1968.cf/blog/nflsoj218/)的套路是一样的。考虑cdq分治。令$solve(l,r)$表示处理$dp[l]$到$dp[r]$。我们先$solve(l,mid)$，然后考虑左边对右边的贡献。把左边和右边都按照$P_i$排序，然后类似归并排序那样，如果左边的$P_i$小，就把对应的直线加到李超树里，否则拿着右边的$H_i$在李超树里查询。注意当$l=r$时，$solve(l,r)$要给$dp$值加上$A_l+H_l^2$。

虽然李超树添加直线的复杂度是$O(log^2n)$的，但是这题里面加的都是全局的直线，所以添加的复杂度其实是$O(logn)$，加上cdq分治的$O(logn)$，总时间复杂度是$O(nlog^2n)$。
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
const LL LINF=2e18;
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

const int MAXN=6e5;

int n;LL p[MAXN+48],a[MAXN+48],h[MAXN+48];
LL dp[MAXN+48];

inline LL Query(LL k,LL b,LL pos)
{
    if (k>0) return LINF;
    return k*pos+b;
}

namespace LiChaoTree
{
    int lson[MAXN*20+48],rson[MAXN*20+48];LL k[MAXN*20+48],b[MAXN*20+48];int Root,tot;
    inline int Create()
    {
        ++tot;lson[tot]=rson[tot]=0;
        k[tot]=1;b[tot]=0;return tot;
    }
    inline void build() {tot=Root=0;}
    inline void modify(int &cur,LL curk,LL curb,int l,int r)
    {
        if (!cur) cur=Create();
        if (k[cur]>0) {k[cur]=curk;b[cur]=curb;return;}
        if (l==r)
        {
            if (Query(curk,curb,l)<=Query(k[cur],b[cur],l)) k[cur]=curk,b[cur]=curb;
            return;
        }
        int mid=(l+r)>>1;
        if (curk<=k[cur])
        {
            if (Query(curk,curb,mid)<=Query(k[cur],b[cur],mid))
            {
                modify(lson[cur],k[cur],b[cur],l,mid);
                k[cur]=curk;b[cur]=curb;
            }
            else
                modify(rson[cur],curk,curb,mid+1,r);
        }
        else
        {
            if (Query(curk,curb,mid)<=Query(k[cur],b[cur],mid))
            {
                modify(rson[cur],k[cur],b[cur],mid+1,r);
                k[cur]=curk;b[cur]=curb;
            }
            else
                modify(lson[cur],curk,curb,l,mid);
        }
    }
    inline LL query(int cur,int pos,int l,int r)
    {
        if (!cur) return LINF;
        LL res=Query(k[cur],b[cur],pos);
        if (l==r) return res;
        int mid=(l+r)>>1;
        if (pos<=mid) check_min(res,query(lson[cur],pos,l,mid)); else check_min(res,query(rson[cur],pos,mid+1,r));
        return res;
    }
}

int seq[MAXN+48];
inline bool cmp(int x,int y) {return p[x]<p[y];}

inline void solve(int l,int r)
{
    if (l==r) {if (l!=1) dp[l]+=h[l]*h[l]+a[l];return;}
    int mid=(l+r)>>1;solve(l,mid);LiChaoTree::build();
    for (register int i=l;i<=r;i++) seq[i]=i;
    sort(seq+l,seq+mid+1,cmp);sort(seq+mid+1,seq+r+1,cmp);
    int k1,k2;
    for (k1=l,k2=mid+1;k1<=mid && k2<=r;)
    {
        int x=seq[k1],y=seq[k2];
        if (p[x]<p[y]) LiChaoTree::modify(LiChaoTree::Root,h[x]*(-2),h[x]*h[x]+dp[x],1,MAXN),k1++;
        else check_min(dp[y],LiChaoTree::query(LiChaoTree::Root,h[y],1,MAXN)),k2++;
    }
    while (k2<=r)
    {
        int y=seq[k2++];
        check_min(dp[y],LiChaoTree::query(LiChaoTree::Root,h[y],1,MAXN));
    }
    solve(mid+1,r);
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d",&n);
    for (register int i=1;i<=n;i++) scanf("%lld",p+i);
    for (register int i=1;i<=n;i++) scanf("%lld",a+i);
    for (register int i=1;i<=n;i++) scanf("%lld",h+i);
    for (register int i=2;i<=n;i++) dp[i]=LINF;dp[1]=a[1];
    solve(1,n);
    printf("%lld\n",dp[n]);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
</details>
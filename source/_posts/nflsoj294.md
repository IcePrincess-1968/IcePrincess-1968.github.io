---
layout: post
date: 2018-11-07
title: "[NFLSoj #294]c 题解"
categories:
- [OI, 常用技术, 莫队, 树上莫队]
- [OI, 常用技术, 分块]
tags: [NFLSoj, 分块, 树上莫队]
mathjax: true
---

## Description

给一棵$n$个点,带边权的树。

有$m$次询问,每次给出$x$, $y$,求最小的非负整数$s$,满足树上$x$到$y$路径上的边权都不等于$s$。

<!-- more -->

## Input Format

第一行共两个正整数$n$, $m$,分别表示点数和询问数。

接下来$n − 1$行,每行共3个正整数$x$, $y$, $v$,分别表示有一条连接$x$,$y$,边权为$v$的边。

接下来$m$行,每行共2个正整数$x$, $y$,表示询问的两个顶点。

## Output Format

对于每一个询问输出一行,共一个非负整数表示这次询问的答案。

## Sample Input
<pre>
7 6
2 1 1
3 1 2
1 4 0
4 5 1
5 6 3
5 7 4
1 3
4 1
2 4
2 5
3 5
3 7
</pre>

## Sample Output
<pre>
0
1
2
2
3
3
</pre>

## Constraints
- 对于$40\%$ 的数据,$n, m \leq 1000$, $0 \leq v \leq 100$。
- 另有$20\%$ 的数据,满足输入的树是一条链。
- 另有$30\%$ 的数据,满足$0 \leq v \leq 1$。
- 对于$100\%$ 的数据,$n, m \leq 100000$, $0 \leq v \leq 100000$。

## Solution

这种求$mex$的问题无法高效地用数据结构维护，考虑莫队。

我们先考虑如果树是一条链的情况。这时就是一个普通的序列莫队。一个朴素的想法是用一个BIT来维护每种数是否出现过，如果增加操作中$cnt=1$就加入BIT,如果减少操作中$cnt=0$就从BIT中删掉。查询的时候只要在BIT中二分就可以了，用类似线段树二分的方法这部分可以做到一个log,通过合理选取块的大小，该算法的复杂度可以做到$O(n\sqrt{nlogn}+mlogn)$

这个做法有一定几率被卡常，考虑如何把根号下的log去掉。我们注意到在莫队的端点移动过程中，总共会有$n\sqrt n$次加入和删除操作，而查询操作只有$m$次，所以我们应该设法用减少插入删除的复杂度，增加查询复杂度的方式来平衡两边的复杂度。

考虑分块，我们对每一个块，记录$bucket[i]$表示第$i$个块中有多少个数存在。插入删除的时候我们只要维护cnt和对应的bucket即可，复杂度为$O(1)$，查询的时候，我们先找到第一个有空缺的bucket,再在bucket中暴力查找第一个空缺的数，时间复杂度$O(\sqrt n)$，这样总复杂度降到$O((m+n)\sqrt n)$,可以比较轻松地通过。

现在考虑树上的情况，我们可以把序列莫队变成树上莫队。有关路径查询的树上莫队有一种非常神的做法。

我们考虑维护这棵树的欧拉dfs序（即进入子树和出子树的时候都加入序列）,这样对于每个节点$i$我们有一个$st[i]$表示它在序列中的第一次出现位置和一个$ed[i]$表示它在序列中的第二次出现位置。我们注意到对于树上的一条链$(u,v)$，不妨设$st[u]<st[v]$，则dfs序中的$[ed[u],st[v]]$这个区间，$(u,v)$上的点会在区间中出现1次，之外的点会出现0次或两次，所以我们在dfs序上莫队的时候只要记录一个每个点的visited数组，如果没有visited就insert,否则就delete即可。

注意几个特殊情况：
1. 当$u$是$v$的祖先的时候，上述的区间是不对的，显然我们应该用$[st[u],st[v]]$这个区间。
2. 当$u$不是$v$的祖先的时候，上述的做法没有包含$lca(u,v)$，所以最后要单独将$lca(u,v)$加入一下。

本题中可以将边权赋给对应的儿子节点，从而在查询一条链的时候$lca$的权值不在路径内，上述的情况2就不用考虑了，情况一要注意将区间改为$[st[u]+1,st[v]]$

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

int n,m;
vector<Pair> v[MAXN+48];
int st[MAXN+48],ed[MAXN+48],seq[MAXN*2+48],val[MAXN+48],ind;
int anc[MAXN+48][21],depth[MAXN+48];

int cnt[MAXN+48],bucket[MAXN+48],N;

inline int calc()
{
    int tar=N;
    for (register int i=1;i<=N-1;i++)
        if (bucket[i]<magic) {tar=i;break;}
    for (register int i=(tar-1)*magic;i<=tar*magic-1;i++)
        if (!cnt[i]) return i;
}

inline void dfs(int cur,int father)
{
    st[cur]=++ind;seq[ind]=cur;
    for (register int i=0;i<int(v[cur].size());i++)
    {
        int y=v[cur][i].x;
        if (y!=father)
        {
            depth[y]=depth[cur]+1;val[y]=v[cur][i].y;
            anc[y][0]=cur;
            for (register int j=1;j<=17;j++) anc[y][j]=anc[anc[y][j-1]][j-1];
            dfs(y,cur);
        }
    }
    ed[cur]=++ind;seq[ind]=cur;
}

inline int getlca(int u,int v)
{
    if (depth[u]<depth[v]) swap(u,v);
    for (register int i=17;i>=0;i--) if (depth[anc[u][i]]>=depth[v]) u=anc[u][i];
    if (u==v) return u;
    for (register int i=17;i>=0;i--) if (anc[u][i]!=anc[v][i]) u=anc[u][i],v=anc[v][i];
    return anc[u][0];
}

struct node
{
    int u,v,ind;
    inline void input() {io.Get(u);io.Get(v);}
    inline bool operator < (const node &other) const
    {
        if (u/magic!=other.u/magic) return u/magic<other.u/magic;
        return v<other.v;
    }
}q[MAXN+48];

bool visited[MAXN+48];int fans[MAXN+48];

inline void ins(int cur)
{
    cnt[val[cur]]++;
    if (cnt[val[cur]]==1) bucket[val[cur]/magic+1]++;
}

inline void del(int cur)
{
    cnt[val[cur]]--;
    if (!cnt[val[cur]]) bucket[val[cur]/magic+1]--;
}

inline void doit(int pos)
{
    if (!visited[seq[pos]]) visited[seq[pos]]=true,ins(seq[pos]);
    else visited[seq[pos]]=false,del(seq[pos]);
}

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    io.Get(n);io.Get(m);int x,y,c;
    for (register int i=1;i<=n-1;i++)
    {
        io.Get(x);io.Get(y);io.Get(c);
        v[x].pb(mp(y,c));v[y].pb(mp(x,c));
    }
    depth[1]=1;dfs(1,-1);
    for (register int i=1;i<=m;i++)
    {
        q[i].input();q[i].ind=i;
        if (st[q[i].u]>st[q[i].v]) swap(q[i].u,q[i].v);
        if (getlca(q[i].u,q[i].v)==q[i].u) q[i].u=st[q[i].u]+1,q[i].v=st[q[i].v];
        else q[i].u=ed[q[i].u],q[i].v=st[q[i].v];
    }
    sort(q+1,q+m+1);N=100000/magic+1;
    memset(cnt,0,sizeof(cnt));memset(visited,false,sizeof(visited));
    for (register int i=q[1].u;i<=q[1].v;i++) doit(i);
    fans[q[1].ind]=calc();int L=q[1].u,R=q[1].v;
    for (register int i=2;i<=m;i++)
    {
        while (L<q[i].u) doit(L++);
        while (L>q[i].u) doit(--L);
        while (R<q[i].v) doit(++R);
        while (R>q[i].v) doit(R--);
        fans[q[i].ind]=calc();
    }
    for (register int i=1;i<=m;i++) printf("%d\n",fans[i]);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
---
layout: post
date: 2018-9-24
title: "AtCoder Code Festival 2016 Final G: Zigzag MST 题解"
categories:
- [OI, 图论, 最小生成树]
tags: [atcoder, 最小生成树]
mathjax: true
---

## Problem Statement

We have a graph with N vertices, numbered 0 through N−1. Edges are yet to be added.

We will process Q queries to add edges. In the i-th (1≦i≦Q) query, three integers Ai,Bi and Ci will be given, and we will add infinitely many edges to the graph as follows:

<!-- more -->

- The two vertices numbered Ai and Bi will be connected by an edge with a weight of Ci.
- The two vertices numbered Bi and Ai+1 will be connected by an edge with a weight of Ci+1.
- The two vertices numbered Ai+1 and Bi+1 will be connected by an edge with a weight of Ci+2.
- The two vertices numbered Bi+1 and Ai+2 will be connected by an edge with a weight of Ci+3.
- The two vertices numbered Ai+2 and Bi+2 will be connected by an edge with a weight of Ci+4.
- The two vertices numbered Bi+2 and Ai+3 will be connected by an edge with a weight of Ci+5.
- The two vertices numbered Ai+3 and Bi+3 will be connected by an edge with a weight of Ci+6.
- ...
 
Here, consider the indices of the vertices modulo N. For example, the vertice numbered N is the one numbered 0, and the vertice numbered 2N−1 is the one numbered N−1.

The figure below shows the first seven edges added when N=16,Ai=7,Bi=14,Ci=1:

![picture](https://atcoder.jp/img/code-festival-2016-final/5b0258fb4255f846a4e10ce875362baf.png)

After processing all the queries, find the total weight of the edges contained in a minimum spanning tree of the graph.

## Constraints

    2 ≦ N ≦ 200,000
    1 ≦ Q ≦ 200,000
    0 ≦ Ai,Bi ≦ N−1
    1 ≦ Ci ≦ 1e9

## Input

The input is given from Standard Input in the following format:

    N Q
    A1 B1 C1
    A2 B2 C2
    :
    AQ BQ CQ

## Output

Print the total weight of the edges contained in a minimum spanning tree of the graph.

## Sample Input 1

    7 1
    5 2 1

## Sample Output 1

    21

The figure below shows the minimum spanning tree of the graph:

![picture](https://atcoder.jp/img/code-festival-2016-final/f1a6c3cfd52c386e6da5c8c761a78521.png)

Note that there can be multiple edges connecting the same pair of vertices.

## Sample Input 2

    2 1
    0 0 1000000000

## Sample Output 2

    1000000001

Also note that there can be self-loops.

## Sample Input 3

    5 3
    0 1 10
    0 2 10
    0 4 10

## Sample Output 3

    42

## Solution

对kruskal算法的灵活运用

我们考虑kruskal算法的精髓，不在于每条边连接的是哪两个点，而在于任意两点之间是否连通

对于每组边，首先我们考虑(Bi,Ai+1),因为(Ai,Bi)的边权比(Bi,Ai+1)小，所以我们一定会先处理(Ai,Bi),如果这条边没加进去，说明Ai和Bi已经连通了，如果这条边加进去了，那么此时Ai和Bi变得连通了，于是我们会发现处理(Bi,Ai+1)时Ai和Bi一定是连通的，所以这是Ai+1这个点我们可以改连到Ai上，把这条边换成(Ai,Ai+1)

依次类推，第二条边(Ai+1,Bi+1),Ai+1,Ai,Bi都是连通的，所以我们可以把Bi+1连到Bi上

最终我们会发现，对于每一组边，我们都是先在Ai和Bi之间连边，然后从Ai和Bi出发顺时针连边，所以我们可以把它拆成三部分：Ai和Bi的横跨边，从Ai出发的链和Bi出发的链

Ai和Bi的横跨边一共只有1e5条，可以不管。对于剩下的链,我们会发现一些很好的性质:如果某一条链当前在连的过程中，发现要连的两个点已经连通了，那么可以立刻停止这条链的连接过程，因为注意到每条链的边权都是公差为2的等差数列，所以已经连起来的那条边属于的链后面的边都会比当前链后面的边的边权小，当前链直接被废掉

因为一共只会向图中连n-1条边，所以直接用堆维护这个过程，复杂度正确

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

const int MAXN=2e5;

int n,m;

struct node
{
    int A,B,C;
    inline void input() {io.Get(A);io.Get(B);io.Get(C);}
}a[MAXN+48];

inline Pair query(int id,int ti)
{
    if (ti==1) return mp(a[id].A,a[id].B);
    if ((a[id].A+1)%n==a[id].B && !(ti&1)) return mp(0,0);
    if (ti&1)
    {
        Pair res=mp(a[id].B+ti/2-1,a[id].B+ti/2);
        res.x%=n;res.y%=n;
        return res;
    }
    else
    {
        Pair res=mp(a[id].A+ti/2-1,a[id].A+ti/2);
        res.x%=n;res.y%=n;
        return res;
    }
}

namespace DSU
{
    int pre[MAXN+48];
    inline void init() {for (register int i=0;i<=n;i++) pre[i]=i;}
    inline int find_anc(int x) {if (pre[x]!=x) pre[x]=find_anc(pre[x]);return pre[x];}
    inline bool issame(int x,int y) {return find_anc(x)==find_anc(y);}
    inline void update(int x,int y) {x=find_anc(x);y=find_anc(y);pre[x]=y;}
}

priority_queue<pair<int,pair<Pair,bool> > > q;

int main ()
{
    io.Get(n);io.Get(m);
    for (register int i=1;i<=m;i++)
    {
        a[i].input();
        q.push(mp(-a[i].C,mp(mp(a[i].A,a[i].B),false)));
        if (a[i].A+1!=a[i].B) q.push(mp(-a[i].C-1,mp(mp(a[i].A,(a[i].A+1)%n),true)));
        q.push(mp(-a[i].C-2,mp(mp(a[i].B,(a[i].B+1)%n),true)));
    }
    LL ans=0;DSU::init();
    for (register int cnt=0;cnt<n-1;)
    {
        int len=q.top().x;Pair res=q.top().y.x;bool type=q.top().y.y;q.pop();
        if (DSU::issame(res.x,res.y)) continue;
        cnt++;ans-=len;
        DSU::update(res.x,res.y);
        if (type)
        {
            res.x=(res.x+1)%n;res.y=(res.y+1)%n;
            len-=2;
            q.push(mp(len,mp(res,type)));
        }
    }
    io.Print(ans,'\n');
    io.flush();return 0;
}
```
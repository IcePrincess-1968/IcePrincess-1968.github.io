---
layout: post
date: 2018-11-04
title: "Codeforces #70E: Information Reform 题解"
categories:
- [OI, Dynamic Programming]
tags: [Codeforces, dp]
mathjax: true
---

## Description

Thought it is already the XXI century, the Mass Media isn't very popular in Walrusland. The cities get news from messengers who can only travel along roads. The network of roads in Walrusland is built so that it is possible to get to any city from any other one in exactly one way, and the roads' lengths are equal.

<!-- more -->

The North Pole governor decided to carry out an information reform. Several cities were decided to be chosen and made regional centers. Maintaining a region center takes k fishlars (which is a local currency) per year. It is assumed that a regional center always has information on the latest news.

For every city which is not a regional center, it was decided to appoint a regional center which will be responsible for keeping this city informed. In that case the maintenance costs will be equal to dlen fishlars per year, where len is the distance from a city to the corresponding regional center, measured in the number of roads along which one needs to go.

Your task is to minimize the costs to carry out the reform.

## Input

The first line contains two given numbers n and k (1 ≤ n ≤ 180, 1 ≤ k ≤ 105).

The second line contains n - 1 integers di, numbered starting with 1 (di ≤ di + 1, 0 ≤ di ≤ 105).

Next n - 1 lines contain the pairs of cities connected by a road.

## Output

On the first line print the minimum number of fishlars needed for a year's maintenance. On the second line print n numbers, where the i-th number will represent the number of the regional center, appointed to the i-th city. If the i-th city is a regional center itself, then you should print number i.

If there are several solutions to that problem, print any of them.

## Examples

input

8 10

2 5 9 11 15 19 20

1 4

1 3

1 7

4 6

2 8

2 3

3 5

output

38

3 3 3 4 3 4 3 3 

## Solution

一道非常神的dp题。

我们可以考虑如下性质：对于任意一棵子树，其中所有被子树外的regional center控制的点，一定都是被同一个regional center控制的，否则如果被多个regional center控制，到子树的根节点最近的regional center一定比其他的优。

考虑如下dp状态：$dp[i][j]$表示考虑以$i$为根的子树，在$j$一定是regional center且$i$被$j$控制的情况下，所有点都被控制的最小花费，其中建立$j$的费用$k$也被计算在内。另外记录$best[i]$表示令$dp[i][j]$取得最小值的$j$。

转移不是非常复杂，考虑$i$的每一个孩子$v$，如果$v$选择和$i$共用一个regional center,则$v$子树的贡献是$dp[v][j]-k$，注意建立$j$的费用在$i$处已经算过了所以要扣除。另外一种情况是$v$没有选择和$i$共用一个regional center，这时$v$子树的贡献是$dp[v][best[v]]$，注意根据上面考虑的性质，$v$选择的regional center必定在$v$子树内才是最优的，所以不用考虑子树$i$向外勾连两个regional center从而导致建立费用算重的情况。更深入的来说,这个dp的转移中有一部分可能是错误的，但只有正确的部分可能是正确答案。

综上，状态转移方程为$dp[i][j]=\sum_{v\in son_i}min(dp[v][best[v]],dp[v][j]-k)$,最终$dp[1][best[1]]$的值就是第一问的答案。

考虑第二问。我们可以倒着考虑$dp[1][best[1]]$的组成，顺着树dfs一遍，每个孩子的$dp[v][j]-k$和$dp[v][best[v]]$比较一下大小就可以得出它是被哪个节点控制的了。

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

const int MAXN=200;

int n,k;
int dp[MAXN+48][MAXN+48],best[MAXN+48];
int d[MAXN+48],dist[MAXN+48][MAXN+48];
vector<int> v[MAXN+48];

inline void floyd()
{
    for (register int i=1;i<=n;i++)
        for (register int j=1;j<=n;j++)
            dist[i][j]=(i==j?0:INF);
    for (register int i=1;i<=n;i++)
        for (register int j=0;j<int(v[i].size());j++)
            dist[i][v[i][j]]=1;
    for (register int k=1;k<=n;k++)
        for (register int i=1;i<=n;i++)
            for (register int j=1;j<=n;j++)
                if (k!=i && k!=j && i!=j)
                    check_min(dist[i][j],dist[i][k]+dist[k][j]);
}

inline void dfs(int cur,int father)
{
    for (register int i=1;i<=n;i++) dp[cur][i]=k+d[dist[cur][i]];
    for (register int i=0;i<int(v[cur].size());i++)
    {
        int y=v[cur][i];
        if (y!=father)
        {
            dfs(y,cur);
            for (register int j=1;j<=n;j++) dp[cur][j]+=min(dp[y][j]-k,dp[y][best[y]]);
        }
    }
    best[cur]=1;for (register int i=2;i<=n;i++) if (dp[cur][i]<dp[cur][best[cur]]) best[cur]=i;
}

int ans[MAXN+48];
inline void getans(int cur,int father,int tar)
{
    ans[cur]=tar;
    for (register int i=0;i<int(v[cur].size());i++)
    {
        int y=v[cur][i];
        if (y!=father) getans(y,cur,dp[y][tar]-k<=dp[y][best[y]]?tar:best[y]);
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
    io.Get(n);io.Get(k);int x,y;
    for (register int i=1;i<=n-1;i++) io.Get(d[i]);
    for (register int i=1;i<=n-1;i++)
    {
        io.Get(x);io.Get(y);
        v[x].pb(y);v[y].pb(x);
    }
    floyd();dfs(1,-1);
    printf("%d\n",dp[1][best[1]]);
    getans(1,-1,best[1]);
    for (register int i=1;i<=n;i++) printf("%d ",ans[i]);printf("\n");
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
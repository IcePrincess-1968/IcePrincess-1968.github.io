---
layout: post
date: 2018-12-19
title: "[AtCoder Grand Contest 029D]Grid game 题解"
categories:
- [OI, 思维]
tags: [AtCoder, 思维]
mathjax: true
---

## Problem Statement

Takahashi and Aoki will play a game using a grid with $H$ rows and $W$ columns of square cells. There are $N$ obstacles on this grid; the $i$-th obstacle is at $(X_i,Y_i)$. Here, we represent the cell at the $i$-th row and $j$-th column $(1\leq i\leq H,1\leq j\leq W)$ by$(i,j)$ . There is no obstacle at $(1,1)$, and there is a piece placed there at $(1,1)$.

<!-- more -->

Starting from Takahashi, he and Aoki alternately perform one of the following actions:

- Move the piece to an adjacent cell. Here, let the position of the piece be $(x,y)$. Then Takahashi can only move the piece to $(x+1,y)$, and Aoki can only move the piece to $(x,y+1)$. If the destination cell does not exist or it is occupied by an obstacle, this action cannot be taken.
- Do not move the piece, and end his turn without affecting the grid.

The game ends when the piece does not move twice in a row.

Takahashi would like to perform as many actions (including not moving the piece) as possible before the game ends, while Aoki would like to perform as few actions as possible before the game ends. How many actions will Takahashi end up performing?

## Constraints

- $1\leq H,W\leq 2\times 10^5$
- $0\leq N\leq 2\times 10^5$
- $1\leq X_i\leq H$
- $1\leq Y_i\leq W$
- if $i\neq j,(X_i,Y_i)\neq (X_j,Y_j)$
- $(X_i,Y_i)\neq (1,1)$
- $X_i$ and $Y_i$ are integers.

## Input

Input is given from Standard Input in the following format:

    H W N
    X1 Y1
    .
    .
    .
    Xn Yn

## Output

Print the number of actions Takahashi will end up performing.

## Sample Input 1 
    3 3 1
    3 2

## Sample Output 1 
    2

For example, the game proceeds as follows:

- Takahashi moves the piece to $(2,1)$.
- Aoki does not move the piece.
- Takahashi moves the piece to $(3,1)$.
- Aoki does not move the piece.
- Takahashi does not move the piece.

Takahashi performs three actions in this case, but if both players play optimally, Takahashi will perform only two actions before the game ends.

## Sample Input 2 
    10 10 14
    4 3
    2 2
    7 3
    9 10
    7 7
    8 1
    10 10
    5 4
    3 4
    2 8
    6 4
    4 4
    5 8
    9 2

## Sample Output 2 
    6

## Sample Input 3 
    100000 100000 0

## Sample Output 3 
    100000

## Solution

感觉这题放D有点水了。

首先一个显然的性质是只要A还有路可走，他就一定会把棋子向下移，否则只要B也停下来游戏就玩完了。

那么B就有选择是否在当前这一列结束游戏的权利。

直接模拟移动的过程，如果选择在当前列结束，就找一下这一列当前点下方最靠上的障碍物（包括地图的下边界）算一下步数，否则如果可以向右走的话就向右走。

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

const int MAXN=2e5;

int h,w,n;
vector<int> v[MAXN+48];int pt[MAXN+48];
map<Pair,int> Mp;

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d%d%d",&h,&w,&n);int ans=h,x,y;
    for (register int i=1;i<=n;i++) scanf("%d%d",&x,&y),v[y].pb(x),Mp[mp(x,y)]=1;
    for (register int i=1;i<=w;i++) sort(v[i].begin(),v[i].end()),pt[i]=0;
    x=1;y=1;int cnt=0;
    for (;;)
    {
        // cerr<<"*"<<x<<' '<<y<<' '<<cnt<<endl;
        while (pt[y]<=int(v[y].size())-1 && v[y][pt[y]]<x) pt[y]++;
        int lim=(pt[y]<=int(v[y].size())-1)?v[y][pt[y]]:h+1;
        check_min(ans,cnt+lim-x);
        if (lim==x+1) break;if (y==w) break;
        ++x;++cnt;
        if (Mp.find(mp(x,y+1))==Mp.end()) ++y;
    }
    printf("%d\n",ans);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
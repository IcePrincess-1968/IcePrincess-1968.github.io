---
layout: post
date: 2018-12-19
title: "[AtCoder Grand Contest 029A]Irreversible operation 题解"
categories:
- [OI, 思维]
tags: [AtCoder, 思维]
mathjax: true
---

## Problem Statement

There are $N$ Reversi pieces arranged in a row. (A *Reversi piece* is a disc with a black side and a white side.) The state of each piece is represented by a string $S$ of length $N$. If $S_i=$'`B`', the $i$-th piece from the left isshowing black; If $S_i=$'`W`', the $i$-th piece from the left is showing white.

<!-- more -->

Consider performing the following operation:

- Choose $i$ $(1\leq i<N)$ such that the $i$-th piece from the left is showing black and the $(i+1)$-th piece from the left is showing white, then flip both of those pieces. That is, the $i$-th piece from the left is now showing white and the $(i+1)$-th piece from the left is now showing black.

Find the maximum possible number of times this operation can be performed.

## Constraints
- $1\leq \mid S\mid \leq 2\times 10^5$

- $S_i=$'`B`' or '`W`'

## Input
Input is given from Standard Input in the following format:
    
    S

## Output

Print the maximum possible number of times the operation can be performed.

## Sample Input 1 
    BBW

## Sample Output 1 
    2
The operation can be performed twice, as follows:
- Flip the second and third pieces from the left.
- Flip the first and second pieces from the left.

## Sample Input 2 
    BWBWBW

## Sample Output 2 
    6

## Solution

我们尝试考虑当没有操作能做的时候，整个序列长什么样子。我们发现一定是所有的W在左边，所有的R在右边，否则一定会出现RW的结构。

接而我们发现每次交换一个RW就将一个在某个W之前的某个R换到后面，也就是说，对于每一个R,它要和它后面的每一个W做一次交换。

于是只要算每个R后面有多少个W即可。或者换一个角度，你可以把R和W想象成1和0,然后类似冒泡排序，需要的就是逆序对个数次交换。

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

char s[MAXN+48];int n;LL ans;

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    io.getstring(s+1);n=strlen(s+1);
    for (register int i=n,cnt=0;i>=1;i--)
        if (s[i]=='W') cnt++; else ans+=cnt;
    io.Print(ans,'\n');
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
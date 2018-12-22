---
layout: post
date: 2018-12-22
title: "[AtCoder Regular Contest 059F]Unhappy Hacking 题解"
categories:
- [OI, Dynamic Programming]
- [OI, 思维]
tags: [AtCoder, 思维, dp]
mathjax: true
---

## Problem Statement

Sig has built his own keyboard. Designed for ultimate simplicity, this keyboard only has $3$ keys on it: the $0$ key, the $1$ key and the backspace key.

To begin with, he is using a plain text editor with this keyboard. This editor always displays one string (possibly empty). Just after the editor is launched, this string is empty. When each key on the keyboard is pressed, the following changes occur to the string:

- The $0$ key: a letter $0$ will be inserted to the right of the string.
- The $1$ key: a letter $1$ will be inserted to the right of the string.
- The backspace key: if the string is empty, nothing happens. Otherwise, the rightmost letter of the string is deleted.

Sig has launched the editor, and pressed these keys $N$ times in total. As a result, the editor displays a string $s$. Find the number of such ways to press the keys, modulo $10^9+7$.

<!-- more -->

## Constraints

- $1\leq N\leq 5000$
- $1\leq \mid s\mid \leq N$
- $s$ consists of the letters $0$ and $1$.

## Partial Score

- $400$ points will be awarded for passing the test set satisfying $1\leq N\leq 300$.

## Input

The input is given from Standard Input in the following format:

    N
    s

## Output

Print the number of the ways to press the keys $N$ times in total such that the editor displays the string $s$ in the end, modulo $10^9+7$.

## Sample Input 1
    3
    0

## Sample Output 1
    5

We will denote the backspace key by $B$. The following $5$ ways to press the keys will cause the editor to display the string $0$ in the end: $00B$, $01B$, $0B0$, $1B0$, $BB0$. In the last way, nothing will happen when the backspace key is pressed.

## Sample Input 2
    300
    1100100

## Sample Output 2
    519054663

## Sample Input 3
    5000
    01000001011101000100001101101111011001000110010101110010000

## Sample Output 3
    500886057

## Solution

这题思维还是挺神仙的。

我们可以发现一个重要的性质：其实给定的串$s$里面的内容是没有意义的，有意义的只有串的长度$n$。因为所有的$2^n$种串出现的方案数肯定是一样的。所以我们只要算出$N$次操作后长度为$n$的方案数，再除以$2^n$即可。

剩下的就是一个简单dp。令$dp[i][j]$表示考虑了前$i$次操作，当前的位数是$j$的方案数。转移非常简单，考虑下一次是加$0$，加$1$（这两种是等价的，都是使得$j+1$）还是删掉（注意$j-1$要和$0$取$max$）即可。

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

const int MAXN=5000;

int n,len;
char s[MAXN+48];

int dp[MAXN+48][MAXN+48];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    scanf("%d%s",&n,s+1);len=strlen(s+1);
    dp[0][0]=1;
    for (register int i=0;i<=n-1;i++)
        for (register int j=0;j<=i;j++)
            if (dp[i][j])
            {
                Add(dp[i+1][max(j-1,0)],dp[i][j]);
                Add(dp[i+1][j+1],1ll*dp[i][j]*2%MOD);
            }
    int inv=quick_pow(2,len);inv=quick_pow(inv,MOD-2);
    printf("%d\n",1ll*dp[n][len]*inv%MOD);
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
---
layout: post
date: 2018-11-08
title: "[AtCoder Grand Contest 011E]Increasing Numbers 题解"
categories:
- [OI, 思维]
tags: [AtCoder, 思维]
mathjax: true
---

## Problem Statement

We will call a non-negative integer increasing if, for any two adjacent digits in its decimal representation, the digit to the right is greater than or equal to the digit to the left. For example, 1558, 11, 3 and 0 are all increasing; 10 and 20170312 are not.

<!-- more -->

Snuke has an integer N. Find the minimum number of increasing integers that can represent N as their sum.

## Constraints

- $1 \leq N \leq 10500000$

## Input

The input is given from Standard Input in the following format:

    N

## Output

Print the minimum number of increasing integers that can represent N as their sum.

## Sample Input 1

    80

## Sample Output 1

    2

One possible representation is 80=77+3.

## Sample Input 2

    123456789

## Sample Output 2

    1

123456789 in itself is increasing, and thus it can be represented as the sum of one increasing integer.

## Sample Input 3

    20170312

## Sample Output 3

4

## Sample Input 4

    7204647845201772120166980358816078279571541735614841625060678056933503

## Sample Output 4

    31

## Solution

很神的一道题

首先我们可以发现一个性质：任何一个好数都可以拆成不超过9个全1数的和，更贴切地说，任何一个好数可以拆成它个位数个全1数的和。

因此我们想到一个方向：我们试图将原数$N$分解成尽可能少的全1数的和，然后每9个全1数我们都能将其合成一个好数，这样就能用最少的好数组成$N$。换句话来说，一个数$N$能表示成$k$个好数的和，当且仅当它能被表示成$9k$个全1数的和。

接下来是非常关键~~神奇~~的一步

对于一个全1数$\underbrace{11\dots 1}_{x个}$，我们可以将其表示为$\frac{10^x-1}{9}$

这样假设$N$可以被表示成$k$个全1数，第$i$个数的长度是$a_i$，我们有如下等式：
$$\sum_{i=1}^{9k}\frac{10^{a_i}-1}{9}=N$$

移项，可得
$$\sum_{i=1}^{9k}10^{a_i}=9N+9k$$

我们发现左边的东西是一些10的次幂相加，而一个数要分解成若干个10的次幂相加的形式，最少需要的就是数字和个10的次幂。因此我们判断一个数$N$能否表示成$k$个全1数的和，只要判断$9N+9k$的数字和是否小于等于$9k$。

记$N$的位数是$L$,这样我们可以在$O(L)$的时间内check一个答案，注意到答案具有单调性，所以可以二分答案，这样复杂度是$O(LlogL)$。

事实上有一个更简单的线性做法：我们不用二分答案，只要从小到大枚举答案即可，每次我们给目标数+9即可，这样每轮均摊的进位次数是$O(1)$的，在进位的时候动态更新一下数字和即可，复杂度$O(L)$。

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

const int MAXN=1e6;

int len;char s[MAXN+48];
int b[MAXN+48];

int main ()
{
#ifdef LOCAL
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
#endif
    io.getstring(s+1);len=strlen(s+1);
    for (register int i=len,j=1;i>=1;i--,j++) b[j]=s[i]-'0';
    for (register int i=1;i<=len;i++) b[i]*=9;
    for (register int i=1;i<=len;i++) b[i+1]+=b[i]/10,b[i]%=10;
    if (b[len+1]) len++;
    int sum=0;for (register int i=1;i<=len;i++) sum+=b[i];
    for (register int ans=1;;++ans)
    {
        sum-=b[1];b[1]+=9;int pt=1;
        while (b[pt]>9) sum-=b[pt+1],b[pt+1]+=b[pt]/10,b[pt]%=10,pt++;
        for (register int i=1;i<=pt;i++) sum+=b[i];
        if (sum<=9*ans) {printf("%d\n",ans);return 0;}
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
```
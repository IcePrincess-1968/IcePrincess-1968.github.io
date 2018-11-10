---
layout: post
date: 2018-10-01
title: "[TopCoder SRM565DIV1C]UnknownTree 题解"
categories:
- [OI, 思维]
- [OI, 其它, 分类讨论题]
tags: [TopCoder, 思维, 分类讨论]
mathjax: true
---

## Problem Statement

You are given three int[]s: distancesA, distancesB, and distancesC. Each of these int[]s has exactly N elements. We are interested in trees that satisfy the following conditions:

<!-- more -->

The tree has exactly N + 3 vertices.

Each vertex has a different label. The set of all labels is { A, B, C, 0, 1, ..., N-1 }.

Each edge of the tree has a positive integer length.

For each i (0 <= i < N), the distance between the vertex A and the vertex i is distancesA[i].

For each i (0 <= i < N), the distance between the vertex B and the vertex i is distancesB[i].

For each i (0 <= i < N), the distance between the vertex C and the vertex i is distancesC[i].

Find the number of trees that satisfy all of the conditions above, and return the number modulo 1,000,000,009.
 
## Definition

Class:  UnknownTree

Method: getCount

Parameters: int[], int[], int[]

Returns: int

Method signature: int getCount(int[] distancesA, int[] distancesB, int[] distancesC)

(be sure your method is public)
    
## Notes

- The distance between two vertices s and t is defined as the sum of lengths of all edges on the only simple path between s and t.

- Two trees T1 and T2 are different when T1 has an edge u-v such that in T2 the edge u-v is either not present at all, or it has a different length than the edge u-v in T1.
 
## Constraints

- distancesA, distancesB, and distancesC will contain the same number of elements.

- distancesA, distancesB, and distancesC will contain between 1 and 50 elements, inclusive.

- Each element of distancesA, distancesB, and distancesC will be between 1 and 100,000,000, inclusive.
 
## Examples

0)  

{1}

{2}

{3}

Returns: 6

1)  
        
{1, 2}

{1, 2}

{1, 2}

Returns: 1

2)  
        
{5, 4}

{3, 2}

{2, 1}

Returns: 8

3)  
        
{2, 4, 2}

{1, 3, 3}

{4, 6, 4}

Returns: 2

4)  
        
{4, 6, 1, 5, 3, 2, 5}

{4, 2, 3, 1, 3, 2, 1}

{5, 7, 2, 6, 4, 3, 6}

Returns: 12

5)  
        
{6, 4, 5, 6, 8, 1, 5, 6, 4, 2}

{4, 2, 3, 4, 6, 1, 3, 4, 2, 2}

{6, 4, 5, 6, 8, 3, 5, 6, 4, 4}

Returns: 9000

6)  
        
{8, 5, 6, 8, 6, 5, 6, 10, 8, 5, 10, 8, 7, 9, 7, 1, 11, 5, 9, 6, 6, 1, 6, 9, 8, 4, 12, 7, 5, 7, 6, 8, 12, 8, 6, 6, 5, 8, 5, 3, 3, 4, 8, 6, 6, 8, 8, 9, 7, 5}

{9, 6, 7, 9, 7, 6, 7, 11, 9, 6, 11, 9, 8, 10, 8, 2, 12, 6, 10, 7, 7, 4, 7, 10, 9, 5, 13, 8, 6, 8, 7, 9, 13, 9, 7, 7, 6, 9, 6, 4, 4, 5, 9, 7, 7, 9, 9, 10, 8, 6}

{8, 9, 6, 8, 2, 5, 6, 10, 8, 5, 10, 8, 7, 9, 1, 5, 11, 5, 9, 6, 6, 7, 6, 9, 8, 4, 12, 7, 5, 7, 6, 8, 12, 8, 6, 6, 5, 8, 1, 7, 3, 4, 8, 6, 6, 8, 8, 3, 7, 5}

Returns: 770724166

7)  
        
{33030780, 30296205, 16842859, 28857842, 37928939, 27190807, 48689043, 33328845, 24254103, 3962046,
31043603, 25699520, 11297547, 27045586, 31603483, 23207518, 44089781, 48470539, 52366295, 39786470,
45623279, 21593844, 38639305, 27260993, 43899542, 36162768, 21640232, 43580853, 33826577, 30501815,
51470990, 2157904, 27823597, 9550575, 39234641, 24163007, 34155133, 42504989, 35821444, 36054200,
29026389, 29716374, 41764139, 19392309, 44258194, 19987908, 56722905, 46771885, 32668277, 40665175}

{16191697, 13457122, 3776, 12018759, 21089856, 10351724, 31849960, 16489762, 7415020, 12877037,
14204520, 8860437, 9035480, 10206503, 14764400, 6368435, 27250698, 31631456, 35527212, 22947387,
28784196, 4754761, 21800222, 10421910, 27060459, 19323685, 4801149, 26741770, 16987494, 13662732,
34631907, 18996987, 10984514, 7288508, 22395558, 7323924, 17316050, 25665906, 18982361, 19215117,
12187306, 12877291, 24925056, 2553226, 27419111, 3148825, 39883822, 29932802, 15829194, 23826092}

{19337227, 16602652, 3149306, 15164289, 24235386, 13497254, 34995490, 19635292, 10560550, 16030119,
17350050, 12005967, 12188562, 13352033, 17909930, 3215353, 30396228, 34776986, 38672742, 26092917,
31929726, 7907843, 24945752, 13567440, 30205989, 22469215, 7946679, 29887300, 20133024, 16808262,
37777437, 22150069, 14130044, 10441590, 25541088, 10469454, 20461580, 28811436, 22127891, 22360647,
15332836, 16022821, 28070586, 5706308, 30564641, 6294355, 43029352, 33078332, 18974724, 26971622}

Returns: 101733071

This problem statement is the exclusive and proprietary property of TopCoder, Inc. Any unauthorized use or reproduction of this information without the prior written consent of TopCoder, Inc. is strictly prohibited. (c)2010, TopCoder, Inc. All rights reserved.

## Solution

非常好(fan)的分类讨论题

不妨尝试考虑A,B,C三个点在树中的位置关系，发现无外乎两种：Y字型，即三个点在树上有LCA,和链状。

先考虑Y字型，对于一棵树，它可能是Y字型的条件是树上到A,B,C三个点距离之和最小的点是唯一的，这个点就是A,B,C的LCA，设其为root。确定了这个LCA之后，我们就能确定AB,AC,BC三条链的长度：用root-A,root-B,root-C加一加就有。

接下来我们希望找出root-A,root-B,root-C上的所有点，我们可以发现到A和B距离等于AB且到A和C距离等于AC的点在root-A分支上，同理可以确定出root-B和root-C分支，这里要判掉几个不合法的情况：每个点到A/B/C和root都要有距离且同一个分支上的两个点到root的距离必须不同,每个点最多只能存在于一个分支上。

最后我们要确定这个“电风扇”下面挂的点有多少种方案，我们考虑以A为根把树提起来，尝试为每个不在“电风扇”里的点找父亲，父亲应该满足深度比当前点小且当前点与父亲到A/B/C的距离差相等（这个值实际上是当前点和父亲的边的权值），注意不要漏了A,B,C三个点。

再考虑链状，首先应该枚举中间的关键点是哪一个，这样有三种情况，以A为例，考虑确定AB和AC的长度，以AB为例，如果AB之间有其他点，那么这个点集是到A和到B的距离和最小的点的集合；如果AB之间没有点，那么树上所有的关键点到A和到B的距离差的绝对值应该相等，AC也是同理，这样有12种情况，接下来为每个点确定父亲，过程和上面一样。有些要特判的不合法情况也和上面一样。

在实现上，为了简便，可以把Y型的A,B,C分别与root匹配形成三组两个点的情况解决，链状可以把B和C分别和A匹配形成两组两个点的情况，从而节省代码量。

这题主要难在以下几点：

1. 判断每个分支上有哪些点，要能发现距离和，距离差的性质

2. 对不合法情况的特判比较繁琐

3. 对于每个点找到合法父亲的个数，直接累乘就是方案数，这些方案确实都是合法的，但是一下子就看出来可以直接这样计算并不容易。

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
const int MOD=1e9+9;
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

template <typename T> inline void check_max(T &x,T cmp) {x=max(x,cmp);}
template <typename T> inline void check_min(T &x,T cmp) {x=min(x,cmp);}
template <typename T> inline T myabs(T x) {return x>=0?x:-x;}
inline int add(int x) {if (x>=MOD) x-=MOD;return x;}
inline int sub(int x) {if (x<0) x+=MOD;return x;}
inline void Add(int &x,int y) {x=add(x+y);}
inline void Sub(int &x,int y) {x=sub(x-y);}

bool visited[148];
int distA[148],distB[148],distC[148],n;
inline bool cmpA(int x,int y) {return distA[x]<distA[y];}
inline bool cmpB(int x,int y) {return distB[x]<distB[y];}
inline bool cmpC(int x,int y) {return distC[x]<distC[y];}

class UnknownTree
{
    inline bool equ(int x,int y,int z) {return x==y && x==z;}
    inline int Y()
    {
        int minn=INF,root=-1;
        for (register int i=1;i<=n;i++)
            if (distA[i]+distB[i]+distC[i]<minn) minn=distA[i]+distB[i]+distC[i];
        for (register int i=1;i<=n;i++)
            if (distA[i]+distB[i]+distC[i]==minn)
                {if (root==-1) root=i; else return 0;}
        int AB=distA[root]+distB[root],AC=distA[root]+distC[root],BC=distB[root]+distC[root];
        memset(visited,false,sizeof(visited));visited[root]=true;
        vector<int> ptA,ptB,ptC;ptA.clear();ptB.clear();ptC.clear();
        for (register int i=1;i<=n;i++)
        {
            if (i==root) continue;
            if (distA[i]+distB[i]==AB && distA[i]+distC[i]==AC)
            {
                if (visited[i]) return 0;
                if (distA[i]>=distA[root]) return 0;
                ptA.pb(i);visited[i]=true;
            }
            if (distA[i]+distB[i]==AB && distB[i]+distC[i]==BC)
            {
                if (visited[i]) return 0;
                if (distB[i]>=distB[root]) return 0;
                ptB.pb(i);visited[i]=true;
            }
            if (distA[i]+distC[i]==AC && distB[i]+distC[i]==BC)
            {
                if (visited[i]) return 0;
                if (distC[i]>=distC[root]) return 0;
                ptC.pb(i);visited[i]=true;
            }
        }
        sort(ptA.begin(),ptA.end(),cmpA);
        sort(ptB.begin(),ptB.end(),cmpB);
        sort(ptC.begin(),ptC.end(),cmpC);
        for (register int i=0;i<int(ptA.size())-1;i++)
            if (distA[ptA[i]]==distA[ptA[i+1]]) return 0;
        for (register int i=0;i<int(ptB.size())-1;i++)
            if (distB[ptB[i]]==distB[ptB[i+1]]) return 0;
        for (register int i=0;i<int(ptC.size())-1;i++)
            if (distC[ptC[i]]==distC[ptC[i+1]]) return 0;
        int pt[148];
        for (register int i=1;i<=n;i++) pt[i]=i;
        sort(pt+1,pt+n+1,cmpA);int ans=1,cnt;
        for (register int i=1;i<=n;i++)
            if (!visited[pt[i]])
            {
                cnt=0;
                for (register int j=1;j<=i-1;j++)
                    if (distA[pt[i]]>distA[pt[j]] && equ(distA[pt[i]]-distA[pt[j]],distB[pt[i]]-distB[pt[j]],distC[pt[i]]-distC[pt[j]])) cnt++;
                if (equ(distA[pt[i]],distB[pt[i]]-AB,distC[pt[i]]-AC)) cnt++;
                if (equ(distB[pt[i]],distA[pt[i]]-AB,distC[pt[i]]-BC)) cnt++;
                if (equ(distC[pt[i]],distA[pt[i]]-AC,distB[pt[i]]-BC)) cnt++;
                if (!cnt) return 0;
                ans=1ll*ans*cnt%MOD;
            }
        return ans;
    }
    inline int calc(int AB,int AC)
    {
        if (!AB || !AC) return 0;
        vector<int> ptB,ptC;ptB.clear();ptC.clear();
        memset(visited,false,sizeof(visited));
        for (register int i=1;i<=n;i++)
        {
            if (distA[i]+distB[i]==AB)
            {
                if (visited[i]) return 0;
                if (distC[i]!=AC+distA[i]) return 0;
                ptB.pb(i);visited[i]=true;
            }
            if (distA[i]+distC[i]==AC)
            {
                if (visited[i]) return 0;
                if (distB[i]!=AB+distA[i]) return 0;
                ptC.pb(i);visited[i]=true;
            }
        }
        sort(ptB.begin(),ptB.end(),cmpB);
        sort(ptC.begin(),ptC.end(),cmpC);
        for (register int i=0;i<int(ptB.size())-1;i++) if (distB[ptB[i]]==distB[ptB[i+1]]) return 0;
        for (register int i=0;i<int(ptC.size())-1;i++) if (distC[ptC[i]]==distC[ptC[i+1]]) return 0;
        int pt[148];
        for (register int i=1;i<=n;i++) pt[i]=i;
        sort(pt+1,pt+n+1,cmpA);int ans=1,cnt;
        for (register int i=1;i<=n;i++)
            if (!visited[pt[i]])
            {
                cnt=0;
                for (register int j=1;j<=i-1;j++)
                    if (distA[pt[i]]>distA[pt[j]] && equ(distA[pt[i]]-distA[pt[j]],distB[pt[i]]-distB[pt[j]],distC[pt[i]]-distC[pt[j]])) cnt++;
                if (equ(distA[pt[i]],distB[pt[i]]-AB,distC[pt[i]]-AC)) cnt++;
                if (equ(distB[pt[i]],distA[pt[i]]-AB,distC[pt[i]]-(AB+AC))) cnt++;
                if (equ(distC[pt[i]],distA[pt[i]]-AC,distB[pt[i]]-(AB+AC))) cnt++;
                if (!cnt) return 0;
                ans=1ll*ans*cnt%MOD;
            }
        return ans;
    }
    inline int Line(vector<int> B,vector<int> A,vector<int> C)
    {
        for (register int i=1;i<=n;i++) distA[i]=A[i-1];
        for (register int i=1;i<=n;i++) distB[i]=B[i-1];
        for (register int i=1;i<=n;i++) distC[i]=C[i-1];
        vector<int> AB,AC;AB.clear();AC.clear();
        int maxn=-INF,minn=INF;
        for (register int i=1;i<=n;i++) maxn=max(maxn,myabs(distA[i]-distB[i])),minn=min(minn,myabs(distA[i]-distB[i]));
        if (maxn==minn) AB.pb(maxn);
        minn=INF;
        for (register int i=1;i<=n;i++) minn=min(minn,distA[i]+distB[i]);
        AB.pb(minn);
        maxn=-INF;minn=INF;
        for (register int i=1;i<=n;i++) maxn=max(maxn,myabs(distA[i]-distC[i])),minn=min(minn,myabs(distA[i]-distC[i]));
        if (maxn==minn) AC.pb(maxn);
        minn=INF;
        for (register int i=1;i<=n;i++) minn=min(minn,distA[i]+distC[i]);
        AC.pb(minn);
        int res=0;
        for (auto d1 : AB)
            for (auto d2 : AC)
                Add(res,calc(d1,d2));
        return res;
    }
    public:
        inline int getCount(vector<int> A,vector<int> B,vector<int> C)
        {
            n=int(A.size());
            for (register int i=1;i<=n;i++) distA[i]=A[i-1];
            for (register int i=1;i<=n;i++) distB[i]=B[i-1];
            for (register int i=1;i<=n;i++) distC[i]=C[i-1];
            return add(add(add(Y()+Line(B,A,C))+Line(A,B,C))+Line(A,C,B));
        }
};

#ifdef LOCAL
int main ()
{
    double TIME=clock();
    freopen ("a.in","r",stdin);
    freopen ("a.out","w",stdout);
    cerr<<"Running..."<<endl;
    vector<int> aa,bb,cc;int nn;
    UnknownTree A;
    while (cin>>nn)
    {
        aa.clear();bb.clear();cc.clear();int x;
        for (register int i=1;i<=nn;i++) cin>>x,aa.pb(x);
        for (register int i=1;i<=nn;i++) cin>>x,bb.pb(x);
        for (register int i=1;i<=nn;i++) cin>>x,cc.pb(x);
        cout<<A.getcount(aa,bb,cc)<<endl;
    }
    io.flush();
#ifdef LOCAL
    cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
    return 0;
}
#endif
```

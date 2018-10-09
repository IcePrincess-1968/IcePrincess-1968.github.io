---
layout: post
date: 2018-10-09
title: "TopCoder SRM198DIV1C: Soma 题解"
categories:
- [OI, 其它, 大模拟]
tags: [TopCoder, 大模拟]
mathjax: true
---

## Description

Soma is a three dimensional puzzle invented by Piet Hein. You have seven pieces which are formed by joining cubes at their faces. (They are all of the non-convex shapes that can be so formed with four or fewer cubes). Six of the pieces are formed from four cubes and one is formed by three cubes. There are 27 cubes total. The pieces can be described by the following arrays which show how high cubes are stacked in each grid cell, and are also shown in the picture below.

<!-- more -->

1 1 1 &nbsp;&nbsp;&nbsp;&nbsp; 1 1 1 &nbsp;&nbsp;&nbsp;&nbsp;  0 1 1

1 0 0 &nbsp;&nbsp;&nbsp;&nbsp; 0 1 0 &nbsp;&nbsp;&nbsp;&nbsp;  1 1 0


1 1  &nbsp;&nbsp;&nbsp;&nbsp; 0 2 &nbsp;&nbsp;&nbsp;&nbsp;  2 0 &nbsp;&nbsp;&nbsp;&nbsp;  1 2

1 0 &nbsp;&nbsp;&nbsp;&nbsp;  1 1 &nbsp;&nbsp;&nbsp;&nbsp;  1 1 &nbsp;&nbsp;&nbsp;&nbsp;  0 1

![picture](https://odzkskevi.qnssl.com/8d93f359d0caf2bcb680cb356dbb5470?v=1539001699)

The pieces can be translated and rotated into any orientation, to build larger shapes, but can not be reflected (as in a mirror) or disassembled. Pieces may touch, but not intersect. Each piece is used exactly once in a solution.

Given pattern (a shape composed of 27 cubes, not necessarily connected), try to arrange the seven soma pieces into the same shape as pattern. In other words, you are constructing pattern using the seven pieces. Return the number of distinct solutions for pattern.

What is meant by "distinct": In a valid solution each cube in pattern will be occupied by a cube from exactly one of the seven pieces. You could assign a number between 1 and 7 inclusive to each pattern cube to indicate which piece occupies that pattern cube. Two solutions are distinct if this assignment is different at one or more pattern cubes. Thus removing a piece which has rotational symmetry, rotating it, and putting it back "in the same place" does not produce a new distinct solution. But a rearrangement of some or all of the pieces which is equivalent to rotating or reflecting the entire pattern (assuming pattern has such symmetry) is considered distinct by this definition. For example the pattern, "21112", can be constructed in exactly two distinct ways using the pieces "211" and "12". The distinct ways are "211"-"12" and "21"-"112".

The pattern will be specified in a String[] similar to the arrays showing the individual pieces above. Each character indicates how many cubes are stacked at that location, starting from a common plane at a height of zero.

For example:

{"333",
 "333",
 "333"}

specifies a 3x3x3 cube which is possible to construct with the seven soma pieces in 11520 distinct (as defined above) ways, so return 11520.

## Definition
   	
Class: Soma

Method: letMeCountTheWays

Parameters: String[]

Returns: int

Method signature: int letMeCountTheWays(String[] pattern)

(be sure your method is public)

## Notes
- 	If no rotational symmetries are involved, there are 24 possible orthogonal orientations resulting from rotations in three dimensions. Visualize the 3x2 "L" shaped piece (which has no rotational symmetry). The top (long end) of the "L" can point in six directions x,y,z,-x,-y,-z. For each of those, the short leg of the "L" can point in one of four directions. 6 x 4 = 24
- 	Each of the other six pieces does have some rotational symmetry, and thus fewer possible distinct orientations.
- 	The rotation of a point about a line passing through the origin can be calculated using a single, vector by matrix, multiplication: [x y z]*M=[rx ry rz] where M is a 3 by 3 matrix, [x y z] is the original point and [rx ry rz] is the rotated point.
- 	Vector by matrix multiplication is defined as: for(i) { r[i]=0 ; for(j) { r[i]+=p[j]*M[j][i] } } where p is the original point and r is the rotated point.
- 	The matrix for 90 degree rotation about the x axis is: {&nbsp;{1,0,0},{0,0,1},{0,-1,0}&nbsp;}
- 	The matrix for 90 degree rotation about the y axis is: {&nbsp;{0,0,-1},{0,1,0},{1,0,0}&nbsp;}
- 	The matrix for 90 degree rotation about the z axis is: {&nbsp;{0,1,0},{-1,0,0},{0,0,1}&nbsp;}
- 	The easiest way to generate all possible orthogonal orientations is to rotate about the x axis (0,90,180 or 270 degrees), then about the y axis (0,90,180 or 270 degrees) then about the z axis (0,90,180,270 degrees). That is 64 combinations of rotations (4x4x4). Try all 64 and throw away those that produce duplicate results.
- 	Sequences of rotations in three dimensions are non-commutative. The order in which you apply the rotations matters.
- 	There are 240 fundamental patterns to form the cube, proven by the great mathematician, Prof. John H. Conway. 240 x 24 rotations x 2 (for mirror reflection) = 11520. There are 96 ways to rotate individual pieces in each of the 11520 "distinct" ways. 11520 x 96 = 1105920 which is the "over a million ways to build a cube" number the manufacturer uses.
 
## Constraints
- 	pattern will contain between 2 and 27 elements, inclusive.
- 	Each element of pattern will consist of between 2 and 27 characters, inclusive.
- 	Each element of pattern will have the same number of characters.
- 	Each character in each element of pattern will be a digit between '0' and '9' inclusive.
- 	The sum of all the digits in pattern will be exactly equal to 27.
 
## Examples

0) 

{"333",
 "333",
 "333"}

Returns: 11520

The cube.

1) 	
     	
{"345",
 "234",
 "123"}

Returns: 2800

The crystal.

2) 

{"3330000",
 "0033300",
 "0000333"}

Returns: 28

The wall.

3) 	

{"21111111",
 "21111111",
 "21111111"}

Returns: 0

The chase lounge, impossible.

4) 	

{"67",
 "77"}

Returns: 1520

The tower.

5) 	

{"010000000000000000000000000",
 "110000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000000",
 "000000000000000000000000022",
 "000000000000000000000000022",
 "000000000000000000000002222",
 "000000000000000000000002222"}

Returns: 76

Replication.

6) 	

{"11100110001",
 "01001100111",
 "00000000000",
 "20002012011",
 "11011001001"}

Returns: 1

Disjoint.

7) 	

{"121",
 "222",
 "121",
 "121",
 "333"}

Returns: 78

The monument.

8) 	

{"020",
 "010",
 "010",
 "020",
 "343",
 "353"}

Returns: 42

The gallows.

This problem statement is the exclusive and proprietary property of TopCoder, Inc. Any unauthorized use or reproduction of this information without the prior written consent of TopCoder, Inc. is strictly prohibited. (c)2010, TopCoder, Inc. All rights reserved. 

## Solution

超级大模拟，不仅代码难写，而且搜索的时候还可能超时

首先预处理所有的soma的24种翻转并剔除掉重复的，旋转可以用题面中notes里的矩阵来实现，判断重复可以计算一下两个soma对应点的位移向量，如果全部一样就是相同的soma，只是位置不一样

搜索的时候找第一个没有被覆盖的格子，然后选一个soma往里面放就行

为了避免超时和尽量减少代码量，应该注意：**STL处理方便 但是速度慢，内置数组处理较复杂 但是速度快，因此在时间不紧的预处理过程中 尽可能的使用STL模板，在搜索中尽可能使用标准的数组和指针**

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
inline int quick_pow(int x,int y) {int res=1;while (y) {if (y&1) res=1ll*res*x%MOD;x=1ll*x*x%MOD;y>>=1;}return res;}
inline int quick_pow(int x,int y,int MO) {int res=1;while (y) {if (y&1) res=1ll*res*x%MO;x=1ll*x*x%MO;y>>=1;}return res;}

int n,m;

struct Matrix
{
	int b[4][4];
	Matrix () {memset(b,0,sizeof(b));}
	inline void clear() {memset(b,0,sizeof(b));}
	inline Matrix (int x,int y,int z) {memset(b,0,sizeof(b));b[1][1]=x;b[1][2]=y;b[1][3]=z;}
	inline void init_I() {for (register int i=1;i<=3;i++) b[i][i]=1;}
	inline bool operator < (const Matrix &other) const
	{
		if (b[1][3]!=other.b[1][3]) return b[1][3]<other.b[1][3];
		if (b[1][1]!=other.b[1][1]) return b[1][1]<other.b[1][1];
		return b[1][2]<other.b[1][2];
	}
	inline bool operator == (const Matrix &other) const
	{
		for (register int i=1;i<=3;i++)
			for (register int j=1;j<=3;j++)
				if (b[i][j]!=other.b[i][j]) return false;
		return true;
	}
	inline Matrix operator + (Matrix ma)
	{
		Matrix res;res.clear();
		for (register int i=1;i<=3;i++)
			for (register int j=1;j<=3;j++)
				res.b[i][j]=b[i][j]+ma.b[i][j];
		return res;
	}
	inline Matrix operator - (Matrix ma)
	{
		Matrix res;res.clear();
		for (register int i=1;i<=3;i++)
			for (register int j=1;j<=3;j++)
				res.b[i][j]=b[i][j]-ma.b[i][j];
		return res;
	}
	inline Matrix operator * (Matrix ma)
	{
		Matrix res;res.clear();
		for (register int i=1;i<=3;i++)
			for (register int j=1;j<=3;j++)
				for (register int k=1;k<=3;k++)
					res.b[i][j]+=b[i][k]*ma.b[k][j];
		return res;
	}
}rx,ry,rz;

inline Matrix quick_pow(Matrix x,int y)
{
	Matrix res;res.clear();res.init_I();
	while (y)
	{
		if (y&1) res=res*x;
		x=x*x;y>>=1;
	}
	return res;
}

typedef vector<Matrix> soma;

struct somapos
{
	int x,y,z;
	somapos () {}
	inline somapos(int xx,int yy,int zz) {x=xx;y=yy;z=zz;}
	inline bool operator < (const somapos &other) const
	{
		if (z!=other.z) return z<other.z;
		if (x!=other.x) return x<other.x;
		return y<other.y;
	}
}P[48];int tot=0;

soma base[48];
vector<soma> candidate[48];
int Cx[48][48][5],Cy[48][48][5],Cz[48][48][5];int ctot[48],wtot[48];

inline soma Move(soma ori,Matrix pos,somapos topos)
{
	Matrix delta;delta.clear();
	for (register int i=1;i<=3;i++) delta.b[1][i]=-pos.b[1][i];
	delta.b[1][1]+=topos.x;delta.b[1][2]+=topos.y;delta.b[1][3]+=topos.z;
	for (register int i=0;i<int(ori.size());i++) ori[i]=ori[i]+delta;
	return ori;
}

inline soma getsoma(soma ori,int dx,int dy,int dz)
{
	for (register int k=0;k<int(ori.size());k++)
	for (register int i=2;i<=3;i++)
		for (register int j=1;j<=3;j++)
			assert(ori[k].b[i][j]==0);
	for (register int i=0;i<int(ori.size());i++)
	{
		ori[i]=ori[i]*quick_pow(rx,dx);
		ori[i]=ori[i]*quick_pow(ry,dy);
		ori[i]=ori[i]*quick_pow(rz,dz);
	}
	sort(ori.begin(),ori.end());
	return ori;
}

inline bool issame(soma x,soma y)
{
	Matrix cmp=x[0]-y[0];
	for (register int i=1;i<int(x.size());i++)
		if (!(x[i]-y[i]==cmp)) return false;
	return true;
}

inline void init_soma()
{
	for (register int i=1;i<=7;i++) base[i].clear(),candidate[i].clear();
	
	base[1].pb(Matrix(1,1,1));base[1].pb(Matrix(2,1,1));base[1].pb(Matrix(3,1,1));base[1].pb(Matrix(1,2,1));
	base[2].pb(Matrix(1,1,1));base[2].pb(Matrix(2,1,1));base[2].pb(Matrix(3,1,1));base[2].pb(Matrix(2,2,1));
	base[3].pb(Matrix(2,1,1));base[3].pb(Matrix(3,1,1));base[3].pb(Matrix(1,2,1));base[3].pb(Matrix(2,2,1));
	base[4].pb(Matrix(1,1,1));base[4].pb(Matrix(2,1,1));base[4].pb(Matrix(1,2,1));
	base[5].pb(Matrix(2,1,1));base[5].pb(Matrix(2,1,2));base[5].pb(Matrix(1,2,1));base[5].pb(Matrix(2,2,1));
	base[6].pb(Matrix(1,1,1));base[6].pb(Matrix(1,1,2));base[6].pb(Matrix(1,2,1));base[6].pb(Matrix(2,2,1));
	base[7].pb(Matrix(1,1,1));base[7].pb(Matrix(2,1,1));base[7].pb(Matrix(2,1,2));base[7].pb(Matrix(2,2,1));

	rx.clear();ry.clear();rz.clear();
	rx.b[1][1]=1;rx.b[2][3]=1;rx.b[3][2]=-1;
	ry.b[1][3]=-1;ry.b[2][2]=1;ry.b[3][1]=1;
	rz.b[1][2]=1;rz.b[2][1]=-1;rz.b[3][3]=1;
	
	for (register int i=1;i<=7;i++) sort(base[i].begin(),base[i].end());
	for (register int i=1;i<=7;i++)
		for (register int dx=0;dx<=3;dx++)
			for (register int dy=0;dy<=3;dy++)
				for (register int dz=0;dz<=3;dz++)
				{
					soma ns=getsoma(base[i],dx,dy,dz);bool f=true;
					for (register int j=0;j<int(candidate[i].size());j++)
						if (issame(candidate[i][j],ns)) {f=false;break;}
					if (f) candidate[i].pb(ns);
				}
}

bool exist[48][48][48];

class Soma
{
	int ans;
	int a[48][48];
	inline int dfs(int used)
	{
		int res=0;
		if (used==127) return 1;
		int tox,toy,toz;
		for (register int i=1;i<=27;i++)
			if (exist[P[i].x][P[i].y][P[i].z]) {tox=P[i].x;toy=P[i].y;toz=P[i].z;break;}
		for (register int i=1;i<=7;i++)
			if (!(used&(1<<(i-1))))
			{
				for (register int j=1;j<=ctot[i];j++)
				{
					int deltax=tox-Cx[i][j][1],deltay=toy-Cy[i][j][1],deltaz=toz-Cz[i][j][1];
					bool valid=true;
					for (register int k=2;k<=wtot[i];k++)
					{
						int xx=Cx[i][j][k]+deltax,yy=Cy[i][j][k]+deltay,zz=Cz[i][j][k]+deltaz;
						if (!exist[xx][yy][zz]) {valid=false;break;}
					}
					if (valid)
					{
						for (register int k=1;k<=wtot[i];k++)
						{
							int xx=Cx[i][j][k]+deltax,yy=Cy[i][j][k]+deltay,zz=Cz[i][j][k]+deltaz;
							exist[xx][yy][zz]=false;
						}
						used|=(1<<(i-1));
						res+=dfs(used);
						for (register int k=1;k<=wtot[i];k++)
						{
							int xx=Cx[i][j][k]+deltax,yy=Cy[i][j][k]+deltay,zz=Cz[i][j][k]+deltaz;
							exist[xx][yy][zz]=true;
						}
						used^=(1<<(i-1));
					}
				}
			}
		return res;
	}
	inline void toarray()
	{
		for (register int i=1;i<=7;i++)
		{
			ctot[i]=int(candidate[i].size());
			wtot[i]=((i==4)?3:4);
			for (register int j=1;j<=ctot[i];j++)
				for (register int k=1;k<=wtot[i];k++)
				{
					Cx[i][j][k]=candidate[i][j-1][k-1].b[1][1];
					Cy[i][j][k]=candidate[i][j-1][k-1].b[1][2];
					Cz[i][j][k]=candidate[i][j-1][k-1].b[1][3];
				}
		}
	}
	public:
		inline int letMeCountTheWays(vector<string> pattern)
		{
			n=int(pattern.size());m=int(pattern[0].size());
			for (register int i=1;i<=n;i++)
				for (register int j=1;j<=m;j++)
					a[i][j]=pattern[i-1][j-1]-'0';
			init_soma();toarray();
			tot=0;memset(exist,false,sizeof(exist));
			for (register int i=1;i<=n;i++)
				for (register int j=1;j<=m;j++)
					for (register int k=1;k<=a[i][j];k++)
					{
						P[++tot]=somapos(i,j,k);
						exist[i][j][k]=true;
					}
			sort(P+1,P+tot+1);
			for (register int i=1;i<=27;i++) ind[P[i].x][P[i].y][P[i].z]=i;
			return dfs(0);
		}
};

#ifdef LOCAL
int main ()
{
	double TIME=clock();
	freopen ("a.in","r",stdin);
	freopen ("a.out","w",stdout);
	cerr<<"Running..."<<endl;
	int nn;vector<string> vv;string ss;
	Soma A;
	while (cin>>nn)
	{
		vv.clear();
		for (register int i=1;i<=nn;i++) cin>>ss,vv.pb(ss);
		double t=clock();
		cout<<A.letMeCountTheWays(vv)<<endl;
		cerr<<"Time: "<<(clock()-t)/CLOCKS_PER_SEC<<endl;
	}
	io.flush();
#ifdef LOCAL
	cerr<<"Exec Time: "<<(clock()-TIME)/CLOCKS_PER_SEC<<endl;
#endif
	return 0;
}
#endif
```
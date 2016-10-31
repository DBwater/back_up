---
title: 连通二分图计数
date: 2016-07-25 12:15:46
tags: [acm,图论]
---
图论--连通二分图计数
<!--more-->
**问题**：含有两边分别为 n 和 m 个不同的点的连通二分图有多少种，即同构的图算是多个。

 记含有n,m个点的连通图个数为 F[n][m].
    含有n,m个点的不连通图个数为 G[n][m].
    含有n,m个点的图的个数为 H[n][m] = 2^(n*m).
    有 F[n][m]+G[n][m]=H[n][m]；F[n][m]=H[n][m]-G[n][m].
 因此要求 F[n][m] 只需求 G[n][m]；
求 G[n][m]：枚举1号点所在的连通块大小为 k1 ( 1 ~ n ) + k2 ( 0 ~ m ) 且 k1、k2 不能同时取最大值。
            当1号点所在连通块大小为 k1+k2 时： 
                 k1个点选取情况为组合数:C[n-1][k1-1]。
                 k2个点选取情况为组合数 :C[m][k2].
                 该连通块的种类数确定 k1+k2 个点后,连通块的种类数为 F[k1][k2].
                 剩余 n-k1 + m-k2 个点组成的图的种类数 H[n-k1][m-k2].
                 因此情况数为 C[n-1][k1-1]×C[m][k2]×F[k1][k2]×H[n-k1][m-k2].
　　　　　G[n]为所有枚举的情况数总和

 ```c++
#include<iostream>
#include<cstdio>
using namespace std;
const int mod=1e9+7;
const int maxn=20;
typedef long long ll;
ll F[maxn][maxn],G[maxn][maxn];//连通，非连通个数
ll H[maxn][maxn];//图的总数
ll C[maxn][maxn];
ll fact[maxn*maxn];
int main()
{
	int n=11,m=11;
	fact[0]=1;
	for(int i=1;i<=n*n;i++)fact[i]=fact[i-1]*3%mod;//3的次方
	C[0][0]=1;
	for(int i=1;i<n;i++){
		C[i][0]=C[i][i]=1;
		for(int j=1;j<i;j++){
			C[i][j]=(C[i-1][j-1]+C[i-1][j])%mod;
		}
	}
	G[0][0]=1;
	for(int i=1;i<=10;i++){
			for(int j=0;j<=10;++j){
				for(int k1=1;k1<=i;k1++){
					for(int k2=0;k2<=j;k2++){
						G[i][j]+=C[i-1][k1-1]*C[j][k2]%mod*F[k1][k2]%mod*fact[(i-k1)*(j-k2)]%mod;
					}
				}
					F[i][j]=((fact[i*j]-G[i][j]%mod)+mod)%mod;
			}
		}
	while(~scanf("%d%d",&n,&m)){
		printf("%lld\n",F[n][m]);
	}
}

 ```

---
title: 2016 Multi-University Training Contest 1题解
date: 2016-07-22 17:39:34
tags: acm
---
多校题解
<!--more-->
吐槽:特么这么弱玩你妹的acm，回家种田多好TAT。。。。
# [A.Abandoned country](http://acm.hdu.edu.cn/showproblem.php?pid=5723)
**题意:**
	给一个图求他的最小生成树，在最小生成树基础上求任意两点之间距离的平均值

**思路:**
	最小生成树直接并查集求就行，对于每条边，统计所有的路径用到此边的次数，也就是边的两端的点数之积。那么这条边的总贡献就是次数*边 权。最后得到所有边的贡献之和再除以总路径数n*(n-1)/2n∗(n−1)/2就是答案。dfs统计每个点的子树节点个数a，就是其中一段的点数。另一边的点数用总数减去就行。

**代码:**
### [查看代码](https://github.com/DBwater/acm_solve/blob/master/hdu5723.cpp)



# [B.Chess](http://acm.hdu.edu.cn/showproblem.php?pid=5724)
**题意:**
	给一个n*20的棋盘放上一些棋子每行的棋子只能往右边移动一格，如果右边有棋子就跳过这个棋子移动到下一个空的格子。直达最后没有棋子可以移动。先手移动赢输出yes，否则输出no。

**思路:**
	就是一个sg值的递推问题，状态压缩先把每一行可能出现的状态的sg值预处理出来然后，然后根据组合游戏的结论，把每一行的状态的sg异或就能得到答案。为0则先手必败，反之亦然。

**代码:**
### [查看代码](https://github.com/DBwater/acm_solve/blob/master/hdu5724.cpp)



# [C.Game](http://acm.hdu.edu.cn/showproblem.php?pid=5725)
**题意**:
**思路**:
**代码**:



# [D.GCD](http://acm.hdu.edu.cn/showproblem.php?pid=5726)
**题意:**
	给n个元素和L,R,求ans=gcd(L,L+1...R),并求出总共有多少区间的gcd等于ans。

**思路:**
	首先用rmq维护每段区间的gcd，因为能发现从左到右gcd一定是递减的，然后对于每个左区间,二分计算gcd(L-(L+n,L+m))相等有m-n+1个,用map统计答案。时间复杂度(n*lognlogn);

**代码:**
### [查看代码](https://github.com/DBwater/acm_solve/blob/master/hdu5726.cpp)



# [E.Necklace](http://acm.hdu.edu.cn/showproblem.php?pid=5727)
**题意:**
	给定n个阴珠子，n个阳珠子，把他们串成项链（相同的珠子不能相邻，即一个阳一个阴），m个信息表示第u个阳珠子如果和第v个阴珠子相邻就会使阳珠子变暗淡，问最少有多少个阳珠子会变暗淡。

**思路:**
	首先可以把阴珠子的位置确定,时间复杂度n!，然后如阳珠子能插入对应的阴珠子前一个阴珠子之间不会变暗淡就连一条边，否则不连，然后就是跑最大图匹配得出最多有多少个阳珠子不变暗淡。总数减去就是答案。总时间复杂度就是O(n!*(n+n))。

**代码:**
### [查看代码](https://github.com/DBwater/acm_solve/blob/master/hdu5727.cpp)



# [F.PowMod](http://acm.hdu.edu.cn/showproblem.php?pid=5728)
**题意**:
	k=sigm(phi(i*n))%mod[1<=i<=m],然后求k的无限k次方%p的结果.

**思路**:
	这题可以先要推出公式递归减小n,m的范围,具体推导过程可以看这里:[戳这里](http://blog.csdn.net/wust_zzwh/article/details/51966450).
递归求出以后根据指数循环节定理，了解指数循环节:[戳这里](http://blog.csdn.net/acdreamers/article/details/8236942).
提供一个类似题目的链接：[BZOJ3884](http://blog.csdn.net/skywalkert/article/details/43955611).

**代码**:
### [查看代码](https://github.com/DBwater/acm_solve/blob/master/hdu5728.cpp)



# [G.Rigid Frameworks](http://acm.hdu.edu.cn/showproblem.php?pid=5729)
**题意**:
	矩形是不稳定的，会变成平行四边形(对边也是平行的)，但是可以在矩形对角线加边，通过构成三角形使这个矩形稳定下来。给一n×m的矩形，可以在单位矩形里加两种对角线（从左上到右下，从左下到右上两种），或者不加对角线，问使这个n×m的矩形稳定下来的方案数。

**思路**:
	n×m的矩形有如下性质：对于任意一行，这一行的每一条竖边永远保持平行；同样，对于任意一列，这一行的每一条横边永远保持平行。
	就是说每一行或者每一列至少有一个短棍支撑就满足稳定，那么这个问题就能转换成连通二分图的数目有多少个了。每一行当成左边节点，每一列当成右边的节点，然后使他们连通就行。
	注意本题的总方案总数(即满足题意的方案数+不满足题意的方案数)是2^(n*m)不是2^(n+m)。
[连通二分图计数](http://www.dbwater.net/2016/07/25/liantong/)

**代码**:
### [查看代码](https://github.com/DBwater/acm_solve/blob/master/hdu5729.cpp)
	

# [H.Shell Necklace](http://acm.hdu.edu.cn/showproblem.php?pid=5730)
**题意**:
**思路**:
**代码**:



# [I.Solid Dominoes Tilings](http://acm.hdu.edu.cn/showproblem.php?pid=5731)
**题意**:
**思路**:
**代码**::



# [J.Subway](http://acm.hdu.edu.cn/showproblem.php?pid=5732)
**题意**:
**思路**:
**代码**:





---
title: luogu1273有线电视网|题解
date: 2018-5-24 16:16:16
tags: [dp,题解]
categories: [算法]
---

其实是个`背包问题`

开始时没有分清费用和价值的实际意义以至于没写出来.后来思考良久后明白:

* 费用: 一颗子树能满足的用户数量
* 价值: 一颗子树满足的用户支付额与运费的差额
* 最后答案应为最大的价值不小于0的费用
* 注意是费用"恰为"的树上背包,需要设置一个"非法"状态的表示值.常常我们使用`-1`表示,但由于本题的价值本就存在负数,所以"非法"状态的表示不能使用-1

设`d(k,u,n)`为节点u已有前k个儿子且需要满足n个用户时的最大价值

转移为:`d(k,u,n)=max{d(k-1,u,n),d(MAX,v,n-j)+d(k-1,u,j)},0<j<MAXn_of_u`

且:`d(MAX,v,n-j)`和`d(k-1,u,j)`都合法

边界为`d(k,u,0) = 0`

最后处理到达u点的花费

`d(MAX,u,n) = d(MAX,u,n) - cost_of_way_to_u`

ps:以前写树上背包都是两个函数相互递归实现,空间较之递推大了一倍,于是本题就被卡空间了.所以第一次使用了递推实现树上背包

代码如下:

```c++
//by:beautyyu
#include <iostream>
#include <cstdio>
#include <string>
#include <cstring>
#include <algorithm>
#include <cmath>
#define ull unsigned long long
using namespace std;
struct edge{
    int v,nxt;
}e[3010];
const int PWP = -0x3fff;
int head[3010],num[3010],wei[3010],val[3010],dp[3010][3010];
bool deal[3010];
void count_(int rt){
    // calculate how many users a subtree have
    for (int i = head[rt];i;i = e[i].nxt){
        count_(e[i].v);
        num[rt] += num[e[i].v];
    }
    return ;
}
void d(int rt){
    dp[rt][0] = 0;
    for (int i = head[rt];i;i = e[i].nxt){
        int &v = e[i].v;
        d(v);
        for (int j = num[rt];j >= 1;-- j)
            for (int k = 1;k <= num[v] && k <= j;++ k)
                if(dp[rt][j - k] != PWP && dp[v][k] != PWP)
                    dp[rt][j] = max(dp[rt][j],dp[rt][j - k] + dp[v][k]);
    }
    for (int i = 1;i <= num[rt];++ i)
        if (dp[rt][i] != PWP)
            dp[rt][i] -= wei[rt];
    return ;
}
int main (){
    int n,m,al,be,cnt = 0;
    cin >> n >> m;
    for (int i = 0;i <= n;++ i)
        for (int j = 0;j <= m;++ j)
            dp[i][j] = PWP;
    for (int i = 1;i <= n - m;++ i){
        int k;
        scanf("%d",&k);
        for (int j = 1;j <= k;++ j){
            scanf("%d%d",&al,&be);
            wei[al] = be;
            e[++ cnt] = edge{al,head[i]};
            head[i] = cnt;
        }
    }
    for (int i = n - m + 1;i <= n;++ i){
        scanf("%d",&val[i]);
        num[i] = 1;
        dp[i][1] = val[i];
    }
    count_(1);
    //read in and first set over
    d(1); 
    for (int i = num[1];i >= 0;-- i)
        if (dp[1][i] >= 0){
            cout << i;
            return 0;
        }
    return 0;
}
```

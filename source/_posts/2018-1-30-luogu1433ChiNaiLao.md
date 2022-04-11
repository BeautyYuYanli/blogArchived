---
title: luogu1433吃奶酪|题解
date: 2018-1-30 17:30:11
tags: [题解,搜索]
categories: [算法]
---

##看楼下的dalao都用了奇妙的操作

菜鸡啥也不会 只能想办法剪剪枝了
（勉强还是ac了
首先 通过观察可知这题其实是在求全排列
**对于所有的排列方式 求出最小花费**
我习惯通过swap的交换来实现全排列
此外就是 ***当当前花费已经大于已知的最佳答案 即减去这条枝*** 这样一个简单的剪枝
而这个剪枝在 ***尽快找到一个较优解*** 的情况下可以剪去更多的废枝
所以在开始搜索前通过***距离原点的远近***排序 可以稍微地“尽快找最优解”（虽然这有点玄学 我解释不了 但对于这题确实有效）

```c++
#include <cstdlib>
#include <cstdio>
#include <iostream>
#include <algorithm>
#include <string>
#include <cstring>
#include <ctime>
#include <cmath>
using namespace std;
struct cheeseNode{
    double x,y;
}chee[16];
int n;
double ans = 2100000000.0;
double cou_dis(cheeseNode& al,cheeseNode& be){
     return sqrt((al.x - be.x)*(al.x - be.x)+(al.y - be.y)*(al.y - be.y));
}
void dfs(int handle,double dis){
    if (handle > n){
        ans = min(ans,dis);
        return;
    }
    if (dis >= ans) return;
    for (int i = handle;i <= n;++ i){
        swap(chee[i],chee[handle]);
        dfs(handle + 1,dis + cou_dis(chee[handle],chee[handle - 1]));
        swap(chee[i],chee[handle]);
    }
    return ;
}
bool cmp (cheeseNode al,cheeseNode be){
    return ((al.x * al.x + al.y * al.y) < (be.x * be.x + be.y * be.y));
}
int main (){
    // freopen ("workbroad.in","r",stdin);
    // freopen ("workbroad.out","w",stdout);
    cin >> n;
    double al,be;
    for (int i = 1;i <= n;++ i){
        scanf("%lf%lf",&al,&be);
        chee[i] = (cheeseNode){al,be};
    }
    sort(chee + 1,chee + n + 1,cmp);
    dfs(1,0);
    printf("%.2lf",ans);
    return 0;
}
```
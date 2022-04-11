---
title: 一种基于数值的dp遍历方法 Codeforces1475G Strange Beauty|题解
date: 2021-2-03
tags: [题解,dp,筛法]  
categories: [算法]
mathjax: true
---

# 一种基于数值的dp遍历方法 Codeforces1475G Strange Beauty|题解

原题: [1475G - Strange Beauty](http://codeforces.com/problemset/problem/1475/G)

显然, 读题后我们可以发现: 对于一个`beauty array`, 将其从小到大排序后, 每一个元素(除第一个)必然能被其前一个元素整除.

将其抽象为图: 对于一条链, 每一个结点(除第一个)都能被其上一个结点整除.

于是, 我们可以轻易得到这样一个树形dp的思路:

> 新增一个值为1的虚拟点作为根结点, 形成一颗有根树. 将数组a从小到大排序后依次插入树中. 对于一个准备加入树的结点, 其可选择的父结点即树中所有可以将其整除的结点. 对于所有可选择的父结点, 找出距离根结点最远的一个, 将新结点挂载为其子结点.
> 
> 完成所有插入操作后, 选择从根到叶最长的一条链, 这条链即最后留下的`beauty array`

这个思路显然是正确的, 但在这道题中行不通. 因为这道题的数据规模是$2\times 10^5$, 而这种做法的最坏复杂度可达到$n^2$

如何处理这个规模的问题? 注意到数据范围中的不寻常之处: 数据大小也为$2\times 10^5$, 而非通常的$10^9$. 这启发我们应当使用一种基于数值的方法进行优化.

注意到, 插入操作是dp中寻找最优子结构的过程. dp的另一种做法是根据子结构更新"父结构", 也即:

将"对于一个新结点, 寻找可将新结点整除的父结点" 改为"对于已在树上的一个结点, 寻找能被其整除, 挂载到其下的子结点"

这个过程即, 遍历该结点的所有倍数. 类似于埃氏筛, 这个过程的复杂度是$N/1+N/2+...+N/N$, 约为$NlogN$, 满足本题的需求.

于是, 我们得到了官方题解所使用的方法:

> Let's calculate for each number $x$ how many times it occurs in the array $a$. Let's denote this number as $cnt[x]$.
> 
> Let's use the dynamic programming method. Let $dp(x)$ be equal to the maximum number of numbers not greater than $x$ such that for each pair of them one of the conditions above is satisfied. More formally, if $dp(x)=k$, then there exists numbers $b_1,b_2,…,b_k (b_i\leq x)$ from the array $a$ such that for all $1 \leq i, j \leq k$ one of the conditions above is satisfied.
> 
> Then to calculate $dp(x)$ you can use the following formula:
> 
> $dp(x) = cnt(x) + \max \limits_{y = 1,\ x\ mod\ y = 0}^{x-1} dp(y)$
> 
> Note that to calculate $dp(x)$ you need to go through the list of divisors of $x$. For this, we use the sieve of Eratosthenes.

一种基于该方法的实现:

```cpp
#include <cstdio>
#include <iostream>
#include <cstdlib>
#include <cstring>
#include <string>
#include <algorithm>
#include <cmath>
#define LL long long
using namespace std;
LL T;
LL n, arr[300000], cnt[300000], dp[300000];
int main (){
    cin >> T;
    while (T--){
        scanf("%lld", &n);
        for (LL i = 1; i <= 200000; ++ i)
            cnt[i] = 0;
        for (LL i = 1; i <= n; ++ i){
            scanf("%lld", &arr[i]);
            cnt[arr[i]] ++;
        }
        for (LL i = 1; i <= 200000; ++ i)
            dp[i] = cnt[i];
        sort(arr + 1, arr + 1 + n);
        LL m = unique(arr + 1, arr + 1 + n) - arr - 1;
        for (LL k = 1;k <= m; ++ k){
            LL i = arr[k];
            for (LL j = i * 2; j <= 200000; j += i){
                dp[j] = max(dp[j], dp[i] + cnt[j]);
            }
        }
        printf("%lld\n", n - *max_element(dp + 1, dp + 200001));
    }
    return 0;
}
```

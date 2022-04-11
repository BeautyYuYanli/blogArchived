---
title: SDOI2009_SuperGCD|题解
date: 2018-9-04
tags: [高精度,题解]
categories: [算法]
---

一道恶心人的毒瘤题:高精度gcd

注意到,**在高精度运算中取模是通过重复相减实现的**

所以对于高精度gcd,**辗转相除法和更相减损术是一样的**

那档燃是写**更相减损术**啦

这毒瘤题还卡常,所以就搞了几个小把戏:

1. 把高精度减法直接重载为`-=`
2. 采取亿进制(事实上甚至可以写成万亿进制)

不得不说高精度运算是很坑的算法,建议除了`构造函数`和重载`赋值符`之外所有成员函数都应该用`const`修饰

以及亿进制下的输出也是一个很麻烦的事情...

代码如下

```c++
#include <iostream>
#include <cstdio>
#include <cstdlib>
#include <cmath>
#include <cstring>
#include <string>
#include <algorithm>
#include <ctime>
#define LL long long
using namespace std;
struct big_int{
        LL data[25000],len;
        big_int(char *al){
                LL al_len = strlen(al),base = 1;
                data[len = 0] = 0;
                for (LL p = al_len - 1;p >= 0;p --){
                        if (base >= 100000000)
                                ++ len,data[len] = 0,base = 1;
                        data[len] += (al[p] - '0') * base;
                        base *= 10;
                }
                len ++;
                return ;
        }
        void print()const{
                LL len = this->len;
                if (!len) {
                        cout <<"0\n";
                        return ;
                }
                len --;
                LL b = 10000000;
                while((data[len] / b) == 0)
                        b /= 10;
                printf("%lld",data[len]);
                string out = "";
                for (LL i = len - 1;i >= 0;-- i){
                        LL b = 10000000,d = data[i];
                        while(b){
                                out += d / b + '0';
                                d %= b;
                                b /= 10;
                        }
                }
                cout << out << endl;
                return ;
        }
        bool operator < (const big_int &al)const{
                if (len != al.len)
                        return len < al.len;
                for (LL i = len - 1;i >= 0;-- i)
                        if (data[i] != al.data[i])
                                return data[i] < al.data[i];
                return 0;
        }
        big_int operator -= (big_int &al){
                for (LL i = 0;i < al.len;++ i)
                        data[i] -= al.data[i];
                for (LL i = 0;i < len;++ i)
                        if (data[i] < 0)
                                data[i] += 100000000,
                                data[i + 1] -= 1;
                while (data[-- len] == 0 && len > 0);
                len ++; 
                if (len == 1 && data[0] == 0)
                        len = 0;
                return *this;
        }
};
LL low_gcd(LL a,LL b){
        return b?low_gcd(b,a % b):a;
}
int main(){
        char alpha[20000],beta[20000];
        scanf("%s%s",alpha,beta);
        if (strlen(alpha) <= 18 && strlen(beta) <= 18){
                LL al,be;
                sscanf(alpha,"%lld",&al);
                sscanf(beta ,"%lld",&be);
                cout << low_gcd(al,be);
        }
        else{
                big_int al = big_int(alpha),be = big_int(beta);
                while (al < be || be < al){
                        if (be < al)
                                al -= be;
                        else
                                be -= al;
                }
                al.print();
        }
        return 0;
}
```

文结
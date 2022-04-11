---
title: a-star学习笔记
date: 2018-3-04 12:12:12
tags: [a*,搜索,笔记]
categories: [算法]
---

#a*学习笔记

[这篇文章](https://www.jianshu.com/p/8905d4927d5f)是鄙人学习`aStar`主要参考的文章,私以为对`aStar`的讲解十分有逻辑了

`aStar`本质上是一个宽度优先算法 -对于一个状态 拓展出它能转移的所有状态 将这些状态推入队列 再逐个拓展 直到目标状态

但不同的是`aStar`算法中的队列变成了优先队列-对于`更有可能优`的状态我们优先去拓展它。而优的判定就通过一个`启发函数`来实现

###a*基本概念

a*一般用于解决最小花费/最大价值问题

1. `起始状态start`: 由题面给出的起始状态
2. `目标状态goal`: 由题面给出的目标状态
3. `已使用花费g_cost`: 由`start`转移到`now`的花费
4. `估计花费h_cost` : 由`now`转移到`goal`的估计花费,在寻路问题中一般使用曼哈顿估价
5. `花费评估f_cost`: 评估本条道路的总花费,即`f_cost = g_cost + h_cost`
6. `开启列表openset`: 目前已拓展出的状态,一般是一个以`f_cost`作为优先度的堆
7. `关闭列表offset`: 禁用拓展的状态和处理完成的状态
8. `追溯表comeFrom`: 存储父子关系,一般在询问拓展路径的情况下用到

###a*操作方式

1. 从`openset`中取出F最小的点`now`,并加入`offset`
2. **判读该点是否为`goal`,真则完成搜索过程**
3. 对于`now`转移得`neighbor`,对于一个`neighbor`而言:
   1. 它在offset中:它被continue了
   2. 它在openset中:
      1. 它的G值优于openset中的那位,则将其替代,更新comeFrom
      2. 它的G值不如openset中的那位,则不管它
   3. 它不在openset中:加入openset

`a*`的操作原理大约是这样 更具体的讲解可以翻阅篇首提到的博客

###值得注意的是

1. 当一个状态被放入`openset`之后,它可能还会被*其他状态所拓展来的替代*,即上边的`3-2-1`的情况,因此:**当`goal`进入`openset`中后,我们并未得到最优路径,当它进入`offset`时的路径才是最优的**,这点与朴素的bfs是不同的
2. 对于上边的`3-2-2`的情况:由于新状态的G值优于原有状态的G值,因此它的F值也更优,直接扔进堆中一定在原有的状态之上,也就是说:**3-2-2和3-3并不需要区别处理**
3. `offset`即bfs中的判重操作,可以是布尔数组也可以是其他东西

##a* 模板

*以下均为个人向,c++内容*

```c++
    //status
    struct node{
        int ;// value about the state
        int f,g,h;//three cost function
        void forecast(){//update the f_cost and g_cost
            f = g + (h = /*pass*/);
            return ;
        }
        bool ifgoal(){//check if it is goal
            return ;
        }
        bool operator < (const node &al) const{//declear the priority
            return f > al.f;
        }
        void putoffset(){//put the state into the offset
            return;
        }
        bool ifoffset(){//check if the state is in the offset
            return;
        }
    };
    priority_queue<node> openset;
    /*(?) offset*/
    bool astar(){
        while (!openset.empty()){
            node r = openset.top();openset.pop();
            if (r.ifgoal()) return 1;
            // when the goal node in the offset ,it is the real best way;
            r.putoffset();
            for (){
                node th = /*a new state*/;
                if (/*it is forbidden*/ || th.ifoffset()) continue;
                th.forecast();
                openset.push(th);
            }
        }
        return 0;
    }
```
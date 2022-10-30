---
title: AcWing 240. 食物链
tags: [并查集, 图]
categories: [Algorithm Solution, AcWing]
---

## [240. 食物链 - AcWing题库](https://www.acwing.com/problem/content/242/)

我们从 $A$ 吃 $B$ ，$B$ 吃 $C$ ，$C$ 吃 $A$ 这个关系入手，不难发现，三者之间的关系具有**传递性**，即这个集合内的元素构成偏序关系

我们可以用一组**向量**来描述这个关系，我们定义向量 $\overrightarrow{AB}$ 表示 $A$ 吃 $B$ ，同理，$B$ 吃 $C$ 有：$\overrightarrow{BC}$ ，那么 $C$ 吃 $A$ 就有：$\overrightarrow{CA}=\overrightarrow{AB}+\overrightarrow{BC}$ 

如果我们给每个向量赋予一个非负数的话，由于非负数的加法会越加越大，因此我们通过**余数**来判断**某个数字表示的是哪种关系**

也就是说，所有带有**关系传递性质的定义，都可以通过上述的过程来描述**

另一方面，由于这里涉及到判断两个动物是否属于同类，也就是去询问两个变量是否处于同一个集合内，不难想到要用并查集

再者，对于某个确定编号的动物而言，其余所有动物跟它的关系不外乎三种：跟它同种类型、吃它、被它吃

在这里，对其余所有节点描述关系过于复杂，我们仅描述当前节点与其父节点的关系，其余的所有节点之间的关系都可以间接推出

我们对 $x$ 与 $p[x]$ 的定义如下

* 若 $x-p[x]$ 余数为 0 ，表示 $x$  与 $p[x]$ 属于同一类
* 若 $x-p[x]$ 的余数为 1 ，表示 $x$ 吃 $p[x]$ 
* 若 $x-p[x]$ 的余数为 2 ，表示 $x$ 被 $p[x]$ 吃

关于余数的记录，我们另外需要一个数组 `d[N]` ，表示当前节点与父节点的关系

需要特别说明的是，我们所定义的「关系」，是满足**加法原理**的，可以直接相加，最终取模就行

剩下的，就是判断什么情况会导致最终结果 `res` 变化了

```cpp
#include <iostream>
using namespace std;

const int N = 5e4 + 10;

int p[N], d[N];

int find(int x)
{
    if(x != p[x])
    {
        int t = find(p[x]);
        d[x] = d[x] + d[p[x]];
        p[x] = t;
    }
    return p[x];
}

int main()
{
    cin.tie(0);
    ios::sync_with_stdio(false);
    int n, m, res = 0;
    cin >> n >> m;
    for(int i = 1; i <= n; i++)
    {
        p[i] = i;
        d[i] = 0;
    }
    while(m--)
    {
        int t, x, y;
        cin >> t >> x >> y;
        if(x > n || y > n) res++;
        else
        {
            if(t == 1)
            {
                int px = find(x), py = find(y);
                if(px != py)
                {
                    p[px] = py;
                    d[px] = d[y] - d[x];
                }
                else if(px == py && (d[x] - d[y]) % 3 != 0) res++;
            }
            else if(t == 2)
            {
                int px = find(x), py = find(y);
                if(px == py && (d[x] - d[y] - 1) % 3 != 0) res++;//必须要是dx比dy多一，写成(dx-dy)%3 == 1 是错误的
                else if(px != py)
                {
                    p[px] = py;
                    d[px] = d[y] - d[x] + 1;
                }
            }
        }
    }
    cout << res << endl;
    return 0;
}
```


---
title: KMP
tags: [KMP, 字符串]
categories: OI
---

## KMP

KMP 算法是用于快速在一个较长的字符串 $S$ 中匹配模式串 $P$ 的算法

需要说明的是，$S$ 与 $P$ 的下标均是从 1 开始存储的

我们定义指针 $i$ 指向 $S$ ，指针 $j$ 指向 $P$ ，当前匹配的字符是 $s[i]$ 与 $p[j+1]$ ，$i$ 的前一个为 $i-1$ ，$j+1$ 的前一个为 $j$ 

如果当前字符不匹配，我们需要考虑的是：最短需要将字符串 $P$ 移动到哪里才能够继续匹配，即我们需要将 $j$ 移动到 $j'$ 后才能继续刚才的匹配

那么现在的问题是，$j'$ 如何求出？只要能做到这个，$next$ 数组就能求出，因为 $j'=next[j]$ 

所以，查找部分的代码如下：

```cpp
for(int i = 1, j = 0; i <= n; i++)
{
    while(j && s[i] != p[j + 1]) j = next[j];
    if(s[i] == p[j + 1])j++;
    if(j == p.length())
    {
        //匹配成功
        //如果需要继续匹配，可以写接着写 j = next[j]
    }
}
```

我们的重点放到求 $next$ 数组上

这里同样可以按照刚才的思路，我们在 $P$ 上同样定义两个指针 $i$ 和 $j$ ，当 $p[i]$ 与 $p[j+1]$ 不匹配时，我们需要考虑的是将 $j$ 移动到哪个哪个位置才可以继续匹配，该位置就是 $next[j]$ 

当移动到新位置时，会有两种可能：

* 当 `j!=0` 时，表明当前的最长前后缀的长度就是 $j$ 
* 当 `j==0` 时，相当于当前比较的是第 1 个字符与第 $i$ 个字符是否相同，也就是求当前以 $i$ 为结尾的字符串的最长前后缀的长度

我们将两种情况合起来，就能写出求 $next$ 数组的代码

需要注意的是，$next[1] = 0$ ，这是默认的，所以 $i$ 从 2 开始

```cpp
for(int i = 2, j = 0; i <= n; i++)
{
    while(j && s[i] != s[j + 1]) j = next[j];
    if(s[i] == s[j + 1]) j++;
    next[i] = j;
}
```

​	 

## 题目

### [831. KMP字符串 - AcWing题库](https://www.acwing.com/problem/content/833/) 

标准的 KMP 模板代码，直接套用就行

```cpp
#include <iostream>
using namespace std;

const int N = 1e5 + 10, M = 1e6 + 10;

int n, m;

char p[N], s[M];

int ne[N];

int main()
{
    cin >> n >> p + 1 >> m >> s +1;
    
    for(int i = 2, j = 0; i <= n; i++)
    {
        while(j && p[i] != p[j + 1]) j = ne[j];
        if(p[i] == p[j + 1]) j++;
        ne[i] = j;
    }
    
    for(int i = 1, j = 0; i <= m; i++)
    {
        while(j && s[i] != p[j + 1]) j = ne[j];
        if(s[i] == p[j + 1]) j++;
        if(j == n)
        {
            printf("%d ", i - j);
            j = ne[j];
        }
    }
    
    return 0;
}
```


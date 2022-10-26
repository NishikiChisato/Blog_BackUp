---
title: Trie树的实现及运用
tags: [Trie]
categories: OI
---

## Trie 树

Trie 树是用于快速**存储与查询**字符串的数据结构

我们用一个二维数组 `son[N][26]` 来模拟一颗 Trie 树，第一维表示节点，第二维表示当前节点的所有孩子

当然，如同单链表一样，我们需要加上一个变量 `idx` 来表示当前用到了哪个节点（理解为当前已经使用的节点也可以）

除此之外，我们需要一个数组 `cnt[N]` 来记录以当前字符为结尾的字符串的个数

需要说明的是，$N$ 的意义为这棵 Trie 树当中的所有节点个数，`idx` 的初始值为 0 

```cpp
const int N = 1e5 + 10;
int son[N][26], cnt[N], idx = 0;
```

下面我们给出 Trie 树的两个基本操作：插入和查找

```cpp
void insert(char* str)//插入单词str
{
    int p = 0;
    for(int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if(son[p][u] == 0) son[p][u] = ++idx;//如果节点不存在那么我们先创建
        p = sun[p][u];
    }
    cnt[p]++;//这里千万不能写出idx，因为cnt表示的是出现次数，插入一次只会加一
}

int query(char* str)//查询单词str出现的次数
{
    int p = 0;
    for(int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if(son[p][u] == 0) return 0;
        p = son[p][u];
    }
    return cnt[p];
}
```

​	 

## 题目

### [835. Trie字符串统计 - AcWing题库](https://www.acwing.com/problem/content/837/)

Trie 树的模板题，没什么好说的，把代码记下来就行

```cpp
#include <iostream>
using namespace std;

const int N = 1e5 + 10;

char str[N];

int trie[N][26], cnt[N], idx;

void insert(char* str)
{
    int p = 0;
    for(int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if(trie[p][u] == 0) trie[p][u] = ++idx;
        p = trie[p][u];
    }
    cnt[p]++;
}

int query(char* str)
{
    int p = 0;
    for(int i = 0; str[i]; i++)
    {
        int u = str[i] - 'a';
        if(trie[p][u] == 0) return 0;
        p = trie[p][u];
    }
    return cnt[p];
}

int main()
{
    int n;
    cin >> n;
    while(n--)
    {
        char op[2];
        scanf("%s%s", op, str);
        if(op[0] == 'I') insert(str);
        else printf("%d\n", query(str));
    }
    return 0;
}
```

​	 

### [143. 最大异或对 - AcWing题库](https://www.acwing.com/problem/content/145/)

这道题就是 Trie 树用于存储数字的典范了

由于是求所有数字当中最大的异或对，因此我们先想一个朴素的暴力做法，即直接枚举两个数

```cpp
for(int i = 0; i < n; i++)
    for(int j = 0; j < n; j++)
        res = max(res, nums[i] ^ nums[j]);
```

由于对称性，$(a_i,a_j)$ 和 $(a_j,a_i)$ 会被重复计算，因此有一个很容易想到的优化是 $j$ 直接枚举到 $i-1$ ，即

```cpp
for(int i = 0; i < n; i++)
    for(int j = 0; j < i; j++)
        res = max(res, nums[i] ^ nums[j]);
```

更进一步，如果我们可以将内层循环给优化掉的话，整体就只剩下 $O(n)$ 的时间复杂度了

外层循环可以帮我们固定一个数 $a_i$ ，而内层循环是不断地去**枚举**每一个小于 $i$ 的数，进而找到最大的异或对

实际上，在这个过程当中有很多数是不必要的，假设我们当前能够通过某种手段去确定一个数，使得这个数范围合法并且它于 $a_i$ 的异或结果达到最大，那么我们就不需要去枚举其他的所有数了，这样内层循环便也被优化掉了

由于我们算的结果是异或，因此我们是可以求出与 $a_i$ 异或结果最大的数的（我们设其为 $x$ ）。在具体的实现上，我们只需要让 $x$ 的每一位位表示都与 $a_i$ 的相反即可

所以，现在我们的问题是，有什么办法可以在一堆**无序**的数当中寻找某一个数。想到这里，答案就已经是呼之欲出了，只能是 Trie 树（如果这堆数有序，我们是可以用二分的，但遗憾的是这里没有）

我们用 Trie 树来存储每个数的各个位表示，由于每个数最多 30 位，有 $1e5$ 个数

因此数的高度最大为 30 ，叶子节点最大为 $1e5$ ，我们直接认为 Trie 树当中所有的节点个数为 $30\times 1e5$ 就行（根本达不到好吧）

接下来我们将情况推广到更一般的情况，具体的 `query` 函数该如何写

实际上很简单，在每一次循环的时候，我们找出当前遍历得到的位是什么，我们**尽可能**往相反的位表示的方向走就行，再用一个数来记录结果就行

```cpp
int query(int x)
{
    int p = 0, res = 0;
    for(int i = 30; i >= 0; i--)
    {
        int t = x >> i & 1;
        if(son[p][!t] != 0) p = son[p][!t], res = 2 * res + !t;
        else p = son[p][t], res = 2 * res + t;
    }
    return res;
}
```

这样，我们便实现了快速查找跟 $a_i$ 按位异或最大的数 $x$ ，我们在所有 $a_i$ 异或 $x$ 的结果中找最大值即可（$x$ 仅仅是与 $a_i$ 异或值最大的数，并不是最终的结果）

完整代码如下：

```cpp
#include <iostream>
#include <algorithm>
using namespace std;

const int N = 1e5 + 10, M = 30 * N;

int son[M][2], idx = 0;

void insert(int x)
{
    int p = 0;
    for(int i = 30; i >= 0; i--)
    {
        int t = x >> i & 1;
        if(son[p][t] == 0) son[p][t] = ++idx;
        p = son[p][t];
    }
}

int query(int x)
{
    int p = 0, res = 0;
    for(int i = 30; i >= 0; i--)
    {
        int t = x >> i & 1;
        if(son[p][!t] != 0) p = son[p][!t], res = 2 * res + !t;
        else p = son[p][t], res = 2 * res + t;
    }
    return res;
}

int main()
{
    int n;
    cin >> n;
    
    int res = 0;
    for(int i = 0; i < n; i++)
    {
        int x;
        scanf("%d", &x);
        insert(x);
        res = max(res, query(x) ^ x);
    }
    
    cout << res << endl;
    
    return 0;
}
```

 
---
title: DP 数组空间压缩
tags: [动态规划, 总结]
categories: Algorithm Solution
---

我们在 [120. 三角形最小路径和](https://nishikichisato.github.io/2022/09/05/Algorithm Solution/120.-三角形最小路径和/) 这里说过一种 `dp` 数组压缩的方式：滚动数组压缩

其实质是将所有的偶数位全部投影到 `dp[0]` ，所有奇数位全部投影到 `dp[1]` 

在实现上并无具体难度，只是单纯地将第一维改为 `dp[i & 1]` 就行

不过这么做的前提是 `dp[i][j]` 的值只依赖其相邻位置，不过也正是因为这一点才允许空间压缩，所有这里也没什么好说的

我们来看另一种空间压缩，这里以 [LC 516.最长回文子序列](https://nishikichisato.github.io/2022/09/04/Algorithm Solution/LC-516.最长回文子序列/) 为例，我们将代码拿过来：

```cpp
int longestPalindromeSubseq(string s)
{
    int n = s.length();
    vector<vector<int>>dp(n, vector<int>(n, 0));
    for (int i = 0, j = 0; i < n && j < n; i++, j++)
        dp[i][j] = 1;
    for (int i = n - 1; i >= 0; i--)
    {
        for (int j = i + 1; j < n; j++)
        {
            if (s[i] == s[j])
                dp[i][j] = 2 + dp[i + 1][j - 1];
            else dp[i][j] = max(dp[i + 1][j], dp[i][j - 1]);
        }
    }
    return dp[0][n - 1];
}
```



我们来考虑一个问题：我们真的需要 $n^2$ 的空间来存储 `dp` 数组吗？

事实上，对于 `dp[i][j]` 的值，我们真正需要用到的数据**只有两行**：`dp[i]` 行与 `dp[i - 1]` 行

也就是说，前面从 0 到 `i - 2` 行的数据都可以被压缩

接着再考虑一个问题：我们对 `dp` 数组的推导主要是通过赋值来实现，如果只用一维 `dp` 的话，那么赋值前的 `dp` 数组里面的数值有什么含义？

显然，我们当前是需要对 `dp` 数组进行更新的，也就是求出 `dp[i]` 的所有值，那么没赋值前的值就是上一次循环结束后遗留下来的值，也就是 `dp[i - 1]` 

到此为止，我们问题已经解决一大半了

接着考虑状态转移，我们说 `dp[i][j]` 的值是由 `dp[i + 1][j - 1]` 、`dp[i][j - 1]` 和 `dp[i + 1][j]` 

我们**从行的视角来考虑**，`dp[i + 1][j]` 是 `dp[i][j]` 未赋值前的值，我们已知

`dp[i - 1][j]` 是已经先于 `dp[i][j]` 赋值的，就在它前面，我们也已知

现在有问题的只有 `dp[i + 1][j - 1]` ，这个值是在对 `dp[i - 1][j]` 赋值的时候掩盖掉的，也就是说我们需要将这个值保留下来才行

在**内层循环里面但还没开始**前，我们需要用临时变量 `tmp` 把 `dp[j]` 的值记录下来，并在此次**内层循环结束**之后赋值给 `pre` （仍在内层循环内）

`pre` 会在**每次进入内层循环之前**就初始化成 0 ，然后让 `pre` 替代 `dp[i + 1][j - 1]` 即可

需要注意的是，我们的**循环是不需要改变的**！！！这一点很重要

完整代码如下：

```cpp
class Solution {
public:
	int longestPalindromeSubseq(string s)
	{
		int n = s.length();
		vector<int>dp(n, 1);
		for (int i = n - 1; i >= 0; i--)
		{
			int pre = 0;
			for (int j = i + 1; j < n; j++)
			{
				int tmp = dp[j];
				if (s[i] == s[j])
					dp[j] = 2 + pre;
				else dp[j] = max(dp[j], dp[j - 1]);
				pre = tmp;
			}
		}
		return dp[n - 1];
	}
};
```

时间复杂度：$O(n^2)$

空间复杂度：$O(n)$ 



最后我们来讨论一下这种一维压缩之后的初始化问题

我们看到，从 `dp[i][j]` 压缩到 `dp[j]` 之后，我们的循环依旧是两层

**只不过是用 `dp[j]` 去表示 `dp[i][j]` 的每一层而已**

也就是说， **`dp[j]` 的初始化情况就等同于 `dp[i][j]` 当中第 0 层的情况**

其实把上面理解了，这里也就不难了
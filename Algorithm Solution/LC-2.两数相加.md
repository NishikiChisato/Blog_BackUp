---
title: LC 2.两数相加
tags: [链表]
categories: Algorithm Solution
---

#### [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

给你两个 **非空** 的链表，表示两个非负的整数。它们每位数字都是按照 **逆序** 的方式存储的，并且每个节点只能存储 **一位** 数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

​	 

#### 模拟

一般来说，链表这种问题，我们会先在前面建立一个前置节点（其节点值为 0 ），这样处理起来更方便

模拟没什么好说的，直接看代码就行

记得最高位的进位如果为 1 的话，需要另开一个节点

```cpp
class Solution {
public:
	ListNode* addTwoNumbers(ListNode* l1, ListNode* l2)
	{
		ListNode* pre = new ListNode(0), * cur = pre;
		int carry = 0;
		while (l1 != nullptr || l2 != nullptr)
		{
			int x = l1 ? l1->val : 0;
			int y = l2 ? l2->val : 0;
			int sum = x + y + carry;
			carry = sum / 10;
			sum %= 10;
			cur->next = new ListNode(sum);
			cur = cur->next;
			if (l1)
				l1 = l1->next;
			if (l2)
				l2 = l2->next;
		}
		if (carry == 1)
			cur->next = new ListNode(carry);
		return pre->next;
	}
};
```



#### 递归

```cpp
class Solution {
public:
	ListNode* add(ListNode* l1, ListNode* l2, int n)
	{
		if (l1 == nullptr && l2 == nullptr && n == 0)
			return nullptr;
		ListNode* cur = new ListNode(((l1 ? l1->val : 0) + (l2 ? l2->val : 0) + n) % 10);
		cur->next = add(l1 ? l1->next : nullptr, l2 ? l2->next : nullptr, ((l1 ? l1->val : 0) + (l2 ? l2->val : 0) + n) / 10);
        return cur;
	}

	ListNode* addTwoNumbers(ListNode* l1, ListNode* l2)
	{
		return add(l1, l2, 0);
	}
};
```


---
title: Leetcode记录-2
date: 2021-06-18 09:01:58
tags: 学习笔记
categories: Leetcode
description: Leetcode刷题记录
---
# 两数相加

## 题目

[链接](https://leetcode-cn.com/problems/add-two-numbers/)

给你两个**非空**的链表，表示两个非负的整数。它们每位数字都是按照**逆序**的方式存储的，并且每个节点只能存储**一位**数字。

请你将两个数相加，并以相同形式返回一个表示和的链表。

你可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例1：**

```
输入：l1 = [2,4,3], l2 = [5,6,4]
输出：[7,0,8]
解释：342 + 465 = 807.
```

**示例2：**

```
输入：l1 = [0], l2 = [0]
输出：[0]
```

**示例3：**

```
输入：l1 = [9,9,9,9,9,9,9], l2 = [9,9,9,9]
输出：[8,9,9,9,0,0,0,1]
```

**提示：**

1. 每个链表中的节点数在范围 `[1, 100]` 内

2. `0 <= Node.val <= 9`
   
3. 题目数据保证列表表示的数字不含前导零

## 解答

```c
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        int i=0;
        ListNode* l3 = new ListNode(0);
        ListNode* p = l3;
        while(l1 != nullptr || l2 != nullptr){
            int l1val;
            int l2val;
            if (l1 == nullptr)
            {
                l1val = 0;
            }
            else
            {
                l1val = l1->val;
            }
            if (l2 == nullptr)
            {
                l2val = 0;
            }
            else
            {
                l2val = l2->val;
            }
            int tmp = l1val+l2val;
            if(i == 1)
            {
                tmp += 1;
            }
            if(tmp>9)
            {
                i = 1;
                tmp = tmp%10;
            }
            else
            {
                i = 0;
            }
            ListNode* q = new ListNode(tmp);
            p->next = q;
            p = p->next;
            if (l1 != nullptr)
            {
                l1 = l1->next;
            }
            if (l2 != nullptr)
            {
                l2 = l2->next;
            }
        }
        if(i == 1)
        {
            p->next = new ListNode(1);
            p = p->next;
        }
        return l3->next;
    }
};
```

## 记录

1. 要注意空指针，无论是取值还是往下一个节点移动都要判断是否是空指针；
2. 这里的`if else`语句其实可以用三目运算符简写成一行；
3. 这个题并不能把链表转数字加起来求和再转链表，因为输入的参数会超出数字长度；
4. 链表本质上是指针+结构体的组合，现在我觉得我又会写链表了！

## 其他解法

[参考链接](https://leetcode-cn.com/problems/add-two-numbers/solution/liang-shu-xiang-jia-by-leetcode-solution/)

1. 相同思路但写法简单一些

   ```python
   class Solution {
       public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
           ListNode root = new ListNode(0);
           ListNode cursor = root;
           int carry = 0;
           while(l1 != null || l2 != null || carry != 0) {
               int l1Val = l1 != null ? l1.val : 0;
               int l2Val = l2 != null ? l2.val : 0;
               int sumVal = l1Val + l2Val + carry;
               carry = sumVal / 10;
               
               ListNode sumNode = new ListNode(sumVal % 10);
               cursor.next = sumNode;
               cursor = sumNode;
               
               if(l1 != null) l1 = l1.next;
               if(l2 != null) l2 = l2.next;
           }
           
           return root.next;
       }
   }
   ```

2. 更短的写法，这个写法很巧妙

   ```python
   class Solution {
       public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
           ListNode dummyHead = new ListNode(-1), pre = dummyHead;
           int t = 0;
           while (l1 != null || l2 != null || t != 0) {
               if (l1 != null) {
                   t += l1.val;
                   l1 = l1.next;
               }
               if (l2 != null) {
                   t += l2.val;
                   l2 = l2.next;
               }
               pre.next = new ListNode(t % 10);
               pre = pre.next;
               t /= 10;
           }
   
           return dummyHead.next;
       }
   }
   ```
   
   


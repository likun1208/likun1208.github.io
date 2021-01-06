---
title: Leetcode记录-1
date: 2020-12-21 14:38:58
tags: 学习笔记
cagegories: Leetcode
description: Leetcode刷题记录
---

# 两数之和

## 题目

[链接](https://leetcode-cn.com/problems/two-sum)

给定一个整数数组`nums`和一个整数目标值`target`，请你在该数组中找出和为目标值的那两个整数，并返回它们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

你可以按任意顺序返回答案。

**示例1：**

```
输入：nums = [2,7,11,15], target = 9
输出：[0,1]
解释：因为 nums[0] + nums[1] == 9 ，返回 [0, 1] 。
```

**示例2：**

```
输入：nums = [3,2,4], target = 6
输出：[1,2]
```

**示例3：**

```
输入：nums = [3,3], target = 6
输出：[0,1]
```

**提示：**

1. 2 <= `nums.length` <= 103
2. -109 <= `nums[i]` <= 109
3. -109 <= `target` <= 109
4. 只会存在一个有效答案

## 解答

```c
int* twoSum(int* nums, int numsSize, int target, int* returnSize){
    * returnSize=2;
    static int result[2];
    for (int i=0; i<numsSize-1; i++){
        for (int j=i+1; j<numsSize; j++){
            if (nums[i]+nums[j]==target){
                result[0]=i;
                result[1]=j;
                return result;
            }
        }
    }
    return result;
}
```

## 记录

1. 指针没学好，所以其实还是没懂为什么一开始要指定`* returnSize=2;`
2. 是最普通的遍历判断是否正确的过程，没有什么复杂算法
3. 第3行如果不用静态数组，则最后返回的时候数组生命周期就结束了，会返回一个空数组
4. 还有一些同样思路的代码写法，但是输出的时候不对，总是输出`]`，猜测和OJ系统的gcc有关，暂且不管

## 其他解法

[参考链接](https://leetcode-cn.com/problems/two-sum/solution/liang-shu-zhi-he-by-leetcode-solution/)

1. 哈希表（C语言的哈希表代码略长，所以改用python）

   ```python
   class Solution:
       def twoSum(self, nums: List[int], target: int) -> List[int]:
           hashtable = dict()
           for i, num in enumerate(nums):
               if target - num in hashtable:
                   return [hashtable[target - num], i]
               hashtable[nums[i]] = i
           return []
   ```

2. 首尾递进查找

   ```python
   class Solution:
       def twoSum(self, nums, target):
           """
           :type nums: List[int]
           :type target: int
           :rtype: List[int]
           """
           sorted_id = sorted(range(len(nums)), key=lambda k: nums[k])
           head = 0
           tail = len(nums) - 1
           sum_result = nums[sorted_id[head]] + nums[sorted_id[tail]]
           while sum_result != target:
               if sum_result > target:
                   tail -= 1
               elif sum_result < target:
                   head += 1
               sum_result = nums[sorted_id[head]] + nums[sorted_id[tail]]
           return [sorted_id[head], sorted_id[tail]]
   ```

   


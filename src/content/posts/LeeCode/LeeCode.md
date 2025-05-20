---
title: LeeCode刷题
image: "assets/LeeCode.jpg"
published: 2025-05-20
description: LeeCode刷题记录
tags: [编译原理, HUST]
category: 课程笔记
draft: true
---

## [1. 两数之和](https://leetcode.cn/problems/two-sum/)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        vector<int> res;
        for(int i=0;i<nums.size();++i){
            for(int j=i+1;j<nums.size();++j){
                if(nums[i]+nums[j]==target){
                    res.push_back(i);
                    res.push_back(j);
                    return res;
                }
            }
        }
        return res;
    }
};
```



## [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)


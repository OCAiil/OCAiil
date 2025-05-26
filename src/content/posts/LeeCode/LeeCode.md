---
title: LeeCode刷题
image: "assets/LeeCode.jpg"
published: 2025-05-20
description: LeeCode刷题记录
tags: [编译原理, HUST]
category: 课程笔记
draft: false
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

```c++
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
        ListNode* head = nullptr;
        ListNode* p = head;
        int flag = 0;
        while(l1||l2){
            int num1 = (l1 == nullptr? 0:l1->val);
            int num2 = (l2 == nullptr? 0:l2->val);
            int res = num1 + num2 + flag;
            flag = 0;
            if(res > 9){
                flag = 1;
                res -= 10;
            }
            if(head == nullptr){
                head = p = new ListNode(res);
            } else {
                p->next = new ListNode(res);
                p = p->next;
            }
            if(l1) l1 = l1->next;
            if(l2) l2 = l2->next;
        }
        if(flag) p->next = new ListNode(1);
        return head;
    }
};
```



## [3. 无重复字符的最长子串](https://leetcode.cn/problems/longest-substring-without-repeating-characters/)

```c++
class Solution {
public:
    int lengthOfLongestSubstring(string s) {
        if(s.length() == 0) return 0;
        unordered_map<char, int> mp;
        int l = 0, r = 0;
        mp[s[0]] = 0;
        int ans = 1;
        while(r < s.length() - 1){
            r++;
            if(mp.find(s[r]) != mp.end() && mp[s[r]] >= l){
                l = mp[s[r]] + 1;
            }
            mp[s[r]] = r;
            ans = max(ans, r-l+1);
        }
        return ans;
    }
};
```



## [4. 寻找两个正序数组的中位数](https://leetcode.cn/problems/median-of-two-sorted-arrays/)

```c++
class Solution {
public:
    double findMedianSortedArrays(vector<int>& nums1, vector<int>& nums2) {
        int len1 = nums1.size(), len2 = nums2.size();
        nums1.push_back(1000005), nums2.push_back(1000005);
        int p1 = 0, p2 = 0;
        int cnt = 0;
        int num[2] = {0, 0};
        while(p1!=len1||p2!=len2){
            cnt++;
            if(nums1[p1] < nums2[p2]){
                num[cnt&1] = nums1[p1];
                p1++;
            } else{
                num[cnt&1] = nums2[p2];
                p2++;
            }
            if(cnt == (len1+len2+2)/2){
                if((len1+len2)&1) return (double)num[cnt&1];
                else return (double)(1.0 * (num[0] + num[1]) / 2);
            }
        }
        return 0;
    }
};
```



## [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.length();
        vector<vector<bool> >dp(n, vector<bool>(n, false));
        for(int i=0;i<n;++i)
            dp[i][i]=true;
        int res = 1;
        int start = 0;
        for(int i=1;i<n;++i){
            if(s[i]==s[i-1]) {
                dp[i-1][i] = true;
                start = i - 1;
                res = 2;
            }
        }
        for(int len=3;len<=n;++len){
            for(int j=0;j+len-1<n;++j){
                if(dp[j+1][j+len-2] && s[j]==s[j+len-1]){
                    dp[j][j+len-1]=true;
                    start = j;
                    res = len;
                }
            }
        }
        return s.substr(start, res);
    }
};
```



## [6. Z 字形变换](https://leetcode.cn/problems/zigzag-conversion/)

```c++
class Solution {
public:
    string convert(string s, int numRows) {
        if(numRows == 1) return s;
        int tmp = ((s.length()+2*numRows-3)/(2*numRows-2))*(numRows-1);
        vector<vector<char> >zzz(numRows, vector<char>(tmp, 0));
        for(int i=0;i<(s.length()+2*numRows-3)/(2*numRows-2);++i){
            for(int j=0;j<2*numRows-2;++j){
                if((i*(2*numRows-2)+j)>=s.length()) break;
                if (j < numRows)
                    zzz[j][i * (numRows - 1)] = s[i * (2 * numRows - 2) + j];
                else
                    zzz[numRows - (j - numRows + 1) - 1][i * (numRows - 1) + j - numRows + 1] = s[i * (2 * numRows - 2) + j];
            }
        }
        string res = "";
        for(auto i:zzz){
            for(auto j:i){
                if(j) res+=j;
            }
        }
        return res;
    }
};
```



## [7. 整数反转](https://leetcode.cn/problems/reverse-integer/)

```c++
class Solution {
public:
    int reverse(int x) {
        // 2147483647 -2147483648
        int maxx = 2147483647, minn = -2147483648;
        int ans = 0;
        while(x){
            if(ans > maxx/10 || (ans == maxx/10 && (x % 10) > 7))
                return 0;
            if(ans < minn/10 || (ans == minn/10 && (x % 10) < -8))
                return 0;
            ans = ans * 10 + x % 10;
            x /= 10;
        }
        return ans;
    }
};
```



## [8. 字符串转换整数 (atoi)](https://leetcode.cn/problems/string-to-integer-atoi/)

```c++
class Solution {
public:
    int myAtoi(string s) {
        int maxx = 2147483647, minn = -2147483648;
        int ans = 0;
        int flag = 1;
        int start = 0;
        int fu = 0;
        for(auto i:s){
            if(start){
                if(i >= 48 && i <= 57) {
                    if(fu){
                        ans = flag * (i - 48);
                        fu = 0;
                    } else {
                        if(ans > maxx/10 || (ans == maxx/10 && (i - 48) > 7)) return maxx;
                        if(ans < minn/10 || (ans == minn/10 && (i - 48) > 8)) return minn;
                        ans = ans * 10 + flag * (i - 48);
                    }
                } else break;
            }
            else if(i == ' ') continue;
            else if(i == '+' || i == '-'){
                start = 1;
                if(i == '-') flag = -1;
            }
            else if(i >= 48 && i <= 57) {
                start = 1;
                if(i == '0') fu = 1;
                else ans = flag * (i - 48);
            }
            else break;
        }
        return ans;
    }
};
```



## [9. 回文数](https://leetcode.cn/problems/palindrome-number/)

```c++
class Solution {
public:
    bool isPalindrome(int x) {
        string s = to_string(x);
        for(int i=0;i<s.length()/2;++i)
            if(s[i]!=s[s.length()-i-1]) return false;
        return true;
    }
};
```



## [10. 正则表达式匹配](https://leetcode.cn/problems/regular-expression-matching/)

```c++
class Solution {
public:
    bool isMatch(string s, string p) {
        if(p.empty()) return s.empty();
        bool d = !s.empty() && (s[0]==p[0]||p[0]=='.');
        if(p[1]=='*')
            return isMatch(s, p.substr(2)) || (d && isMatch(s.substr(1), p)); 
        else
            return d && isMatch(s.substr(1), p.substr(1));
    }
};
```



## [11. 盛最多水的容器](https://leetcode.cn/problems/container-with-most-water/)

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        int l = 0, r = height.size() - 1;
        int ans = 0;
        while(l != r){
            ans = max(ans, (r - l) * min(height[l], height[r]));
            if(height[l] < height[r]) ++l;
            else --r;
        }
        return ans;
    }
};
```



## [12. 整数转罗马数字](https://leetcode.cn/problems/integer-to-roman/)

```c++
class Solution {
public:
    string intToRoman(int num) {
        string romans[13] = {"M", "CM", "D", "CD", "C", "XC", "L", "XL", "X", "IX", "V", "IV", "I"};
        int values[13] = {1000, 900, 500, 400, 100, 90, 50, 40, 10, 9, 5, 4, 1};
        string res = "";
        for(int i = 0; i < 13; ++i){
            while(num - values[i] >= 0){
                res += romans[i];
                num -= values[i];
            }
        }
        return res;
    }
};
```


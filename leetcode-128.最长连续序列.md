## leetcode-128.最长连续序列

给定一个未排序的整数数组，找出最长连续序列的长度。

要求算法的时间复杂度为 O(n)。

示例:

>  输入: [100, 4, 200, 1, 3, 2]
> 输出: 4
> 解释: 最长连续序列是 [1, 2, 3, 4]。它的长度为 4。

解题思路:

1. 给定列表进行排序 sort()
2. 遍历排序后的列表 
3. ==1进入下一层循环 
4. 保存cur - pre !=1, 保存到列表并比较列表保存的下标与上一个的距离 大于max则复制给max 

```python
# 代码实现
class Solution:
    def longestConsecutive(self, nums: List[int]) -> int:
      	if len(nums) <= 1: return len(nums)
        ans = 1
        nums = list(set(nums))
        nums.sort()
        nums.append('q')
        tmp = [0]
        for i in range(1,len(nums)): 
            if nums[i] != nums[i-1]+1: 
                if i-tmp[-1] > ans:
                    ans =i-tmp[-1]
                tmp.append(i)
        return ans
                
```


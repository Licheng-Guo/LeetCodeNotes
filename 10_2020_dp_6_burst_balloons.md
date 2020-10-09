# 312. Burst Balloons

Given n balloons, indexed from 0 to n-1. Each balloon is painted with a number on it represented by array nums. You are asked to burst all the balloons. If the you burst balloon i you will get `nums[left] * nums[i] * nums[right]` coins. Here left and right are adjacent indices of i. After the burst, the left and right then becomes adjacent.

Find the maximum coins you can collect by bursting the balloons wisely.

## Reference Solution

What I did not realize is that the two companions for the last-burst balloon is determined from the very beginning, irrelavent to the two sub-problems.

```c++
class Solution {
public:
    int maxCoins(vector<int>& nums) {
        int n = nums.size();
        nums.insert(nums.begin(), 1);
        nums.insert(nums.end(), 1);
        vector<vector<int> > cache(n+2, vector<int>(n+2, -1));
        return maxCoins(1, n, nums, cache); // n instead of n-1
    }
    
private:
    int maxCoins(int left, int right, vector<int>& nums, vector<vector<int> >& cache) {
        if (cache[left][right] != -1) return cache[left][right];
        
        int local_max = 0;
        for (int i = left; i <= right; i++) {
            int curr = nums[left-1] * nums[i] * nums[right+1] + 
                maxCoins(left, i-1, nums, cache) +
                maxCoins(i+1, right, nums, cache);
            local_max = max(local_max, curr);
        }
        cache[left][right] = local_max;
        return local_max;
    }
};
```

Pythonic implementation:
```python
from functools import lru_cache

class Solution:
    def maxCoins(self, nums: List[int]) -> int:

        nums = [1] + nums + [1]

        # awesome!
        @lru_cache(None)
        def dp(left, right):

            if left + 1 == right: return 0

            return max(nums[left] * nums[i] * nums[right] + dp(left, i) + dp(i, right) for i in range(left+1, right))

        return dp(0, len(nums)-1)
```
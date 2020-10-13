# 416. Partition Equal Subset Sum

Given a non-empty array containing only positive integers, find if the array can be partitioned into two subsets such that the sum of elements in both subsets is equal.

## Reference Solution

The brute-force way is to iterate through all possibilities, i.e. for each number, there are two choices whether taking it or not. To implement this one good way is to use recursion. Thus to compute whether the first `n` numbers can sum up to the target `t`, we first consider whether the n-th number is used. If the n-th number is used, then the problem becomes whether the first `n-1` numbers can sum up to `t - num[n]`; else the problem becomes if the first `n-1` numbers can sum up to `t`.

To implement this in a bottom-up fashion, we first compute for each target between `0` and `t`, if the first `i` numbers can sum up to each target. Then we compute whether by adding the `i+1`-th number we can sum up to each of the target.

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for (auto num : nums) sum += num;
        if (sum % 2 == 1) return false;
        int target = sum / 2;
        int size = nums.size();
        bool can_parti[size+1][target+1];
        
        can_parti[0][0] = true;
        for (int i = 1; i <= size; i++) can_parti[i][0] = true;
        for (int jj = 1; jj <= target; jj++) can_parti[0][jj] = false;
        
        for (int i = 1; i <= size; i++) {
            for (int jj = 0; jj <= target; jj++) {
                // whether the first i number can sum to jj
                int curr = nums[i-1];
                if (curr > jj) {
                    can_parti[i][jj] = can_parti[i-1][jj];
                }
                else {
                    can_parti[i][jj] = can_parti[i-1][jj] | can_parti[i-1][jj-curr];
                }
            }
        }
        
        return can_parti[size][target];
    }
};
```
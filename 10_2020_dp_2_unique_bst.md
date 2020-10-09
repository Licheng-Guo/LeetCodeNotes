# 96. Unique Binary Search Trees

Given n, how many structurally unique BST's (binary search trees) that store values 1 ... n?

## Reference Solution

For each of 1 to n, choose the digit as the root.

```c++
class Solution {
public:
    int numTrees(int n) {
        vector<int> nums(n+1, 0);
        nums[1] = 1;
        nums[0] = 1;
        
        for (int len = 2; len <= n; len++) {
            int sum = 0;
            for (int root = 1; root <= len; root++) {
                sum += nums[root-1] * nums[len-root]; 
            }
            nums[len] = sum;
        }
        
        return nums[n];
    }
};
```
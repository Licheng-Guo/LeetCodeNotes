# 238. Product of Array Except Self

Given an array nums of n integers where n > 1,  return an array output such that output[i] is equal to the product of all the elements of nums except nums[i].

## Reference solution

The lesson is that we may consider not obtaining the results in one step.

```c++
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int size = nums.size();
        vector<int> res(size, 1);
        
        int fromBeg = 1;
        for (int i = 0; i < size; i++) {
            res[i] *= fromBeg;
            fromBeg *= nums[i];
        }
        
        int fromEnd = 1;
        for (int i = size-1; i >= 0; i--) {
            res[i] *= fromEnd;
            fromEnd *= nums[i];
        }
        
        return res;
    }
};
```

## Attempt 2
```c++
class Solution {
public:
    vector<int> productExceptSelf(vector<int>& nums) {
        vector<int> res(nums.size()); // forgot to initialize
        
        int fromBeg = 1;
        for (int i = 0; i < nums.size(); i++) {
            res[i] = fromBeg;
            fromBeg *= nums[i];
        }
        
        int fromEnd = 1;
        for (int i = nums.size()-1; i >= 0; i--) {
            res[i] *= fromEnd;
            fromEnd *= nums[i];
        }
        
        return res;
    }
};
```
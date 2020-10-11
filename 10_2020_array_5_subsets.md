# 78. Subsets

Given a set of distinct integers, nums, return all possible subsets (the power set).

## First Attempt

```c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        if (nums.empty()) return {};
        return subsets(nums, 0);
    }

private:
    vector<vector<int> > subsets(vector<int>& nums, int start) {
        // note the initialization here !
        if (start == nums.size()) return vector<vector<int>>(1, vector<int>());
        
        vector<vector<int> > sub1 = subsets(nums, start+1);
        vector<vector<int> > sub2 = sub1;
        for (int i = 0; i < sub2.size(); i++) {
            sub2[i].push_back(nums[start]);
        }            

        sub1.insert(sub1.end(), sub2.begin(), sub2.end());
        
        return sub1;
    }
};
```

Reference python implementation:
```python
class Solution:
    def subsets(self, nums: List[int]) -> List[List[int]]:
        n = len(nums)
        output = [[]]
        
        for num in nums:
            output += [curr + [num] for curr in output]
        
        return output
```
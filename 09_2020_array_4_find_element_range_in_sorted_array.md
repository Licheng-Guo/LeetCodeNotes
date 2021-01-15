# 34. Find First and Last Position of Element in Sorted Array

Given an array of integers nums sorted in ascending order, find the starting and ending position of a given target value.

Your algorithm's runtime complexity must be in the order of O(log n).

If the target is not found in the array, return [-1, -1].

## Reference Solution

Attempt 1

forgot the corner case where the range includes the end of the array.
```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if (nums.empty()) return {-1, -1};
        
        int lo = 0; int hi = nums.size()-1;
        vector<int> res;
        
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] == target) {
                hi = mid;
            }
            else if (nums[mid] < target) {
                lo = mid+1;
            }
            else {
                hi = mid-1;
            }
        }
        if (nums[lo] == target) res.push_back(lo);
        else return {-1, -1}; 
            
        hi = nums.size()-1;
        while (lo < hi) {
            int mid = (lo + hi) / 2 ;
            if (nums[mid] == target) {
                if (lo == mid) break;
                lo = mid;
            }
            else if (nums[mid] < target) {
                lo = mid + 1;
            }
            else {
                hi = mid - 1;
            }
        }
        
        // forgot the corner case. e.g. [2, 2]
        if (lo+1 < nums.size() && nums[lo+1] == target) res.push_back(lo+1);
        else res.push_back(lo);
        
        return res;
    }
};
```

Could improve the second binary search as follows:
```c++
hi = nums.size()-1;
while (lo < hi) {
    int mid = (lo + hi) / 2 + 1; // make mid biased to the right
    if (nums[mid] == target) {
        lo = mid;
    }
    else if (nums[mid] < target) {
        lo = mid + 1;
    }
    else {
        hi = mid - 1;
    }
}
res.push_back(lo);
```

Use library functions:
```c++
vector<int> searchRange(vector<int>& nums, int target) {
    int lo = lower_bound(nums.begin(), nums.end(), target) - nums.begin();
    if (lo == nums.size() || nums[lo] != target)
        return {-1, -1};
    int hi = upper_bound(nums.begin(), nums.end(), target) - nums.begin() - 1;
    return {lo, hi};
}
```
Or
```c++
vector<int> searchRange(vector<int>& nums, int target) {
    auto bounds = equal_range(nums.begin(), nums.end(), target);
    if (bounds.first == bounds.second)
        return {-1, -1};
    return {bounds.first - nums.begin(), bounds.second - nums.begin() - 1};
}
```

## Attempt 2

```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if (nums.empty()) return {-1, -1}; // forgot this
        
        int lo = 0, hi = nums.size()-1; // forgot -1
        vector<int> res;
        
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] == target) {
                hi = mid;
            }
            else if (nums[mid] < target) {
                lo = mid + 1;
            }
            else {
                hi = mid - 1;
            }
        }
        if (nums[lo] != target ) return {-1, -1};
        
        res.push_back(lo);
        
        hi = nums.size()-1;
        while (lo < hi) {
            int mid = (lo + hi) / 2 + 1;
            if (nums[mid] == target) {
                lo = mid;
            }
            else if (nums[mid] < target) {
                lo = mid + 1;
            }
            else {
                hi = mid - 1;
            }            
        }
        
        res.push_back(lo);
        return res;
    }
};
```

## Attempt 3
```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        if (nums.size() == 0) return {-1, -1};
        int lower_bound = -1;
        int upper_bound = -1;
        
        int lo = 0, hi = nums.size()-1;
        
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] == target) {
                hi = mid;
            }
            else if (nums[mid] > target) {
                hi = mid - 1;
            }
            else {
                lo = mid + 1;
            }
        }

        // assert(lo == hi); // wrongly put the assert before checkpoint the corner case
        if (nums[lo] != target) return {-1, -1};
        assert(lo == hi);

        lower_bound = lo;
        
        //lo = 0;
        hi = nums.size()-1;
        
        while (lo < hi) {
            int mid = (lo + hi) / 2 + 1;
            if (nums[mid] == target) {
                lo = mid;
            }
            else if (nums[mid] > target) {
                hi = mid - 1;
            }
            else {
                lo = mid + 1;
            }
        }
        
        assert(lo == hi);
        assert(nums[lo] == target);
        upper_bound = hi;
        
        return {lower_bound, upper_bound};
    }
};
```
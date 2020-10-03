# 33. Search in Rotated Sorted Array

You are given an integer array `nums` sorted in ascending order, and an integer `target`.

Suppose that nums is rotated at some pivot unknown to you beforehand (i.e., `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`).

If `target` is found in the array return its index, otherwise, return `-1`.

## Reference Solution

Note that the first binary search has subtle difference from the second.
```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int size = nums.size();
        
        int lo = 0, hi = size-1;
        while (lo < hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] > nums[hi]) {
                lo = mid+1;
            }
            else {
                hi = mid;
            }
        }
        int pivot = lo; // very tricky
        
        lo = 0; hi = size - 1;
        while (lo <= hi) { // why <= ?
            int mid = (lo + hi) / 2;
            if (nums[(mid+pivot)%size] == target) {
                return (mid+pivot)%size;
            }
            else if (nums[(mid+pivot)%size] > target) {
                hi = mid-1;
            }
            else {
                lo = mid+1;
            }
        }
        
        return -1;
    }
};
```

## Attempt 2

In a binary search, if only one side will move in each iteration, we should let the `mid` biase to the side the will move.

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        // first use binary search to find the minimal element
        int lo = 0, hi = nums.size()-1;

        while (lo < hi) { // fixme: double-check whether < or <=
            int mid = (lo + hi) / 2; // fixme: check whether biased to the right
            if (nums[mid] < nums[hi]) { 
                hi = mid;
            }
            else {
                lo = mid + 1;
            }
        }
        int pivot = lo;
        
        // then use modular expression to "rotate back"
        lo = 0; hi = nums.size()-1;
        
        while (lo <= hi) {
            int mid = (lo + hi) / 2;
                
            int mid_rotate = (mid + pivot) % nums.size(); // confuse pivot and lo

            if (nums[mid_rotate] == target) {
                return mid_rotate;
            }
            else if (nums[mid_rotate] < target) {
                lo = mid + 1;
            }
            else {
                hi = mid - 1;
            }
        }
        return -1;
        
        
        // corner case: what if the array is not rotated? 
        // corner case: what if the number does not exist in the array?
        // corner case: what if nums is empty or only has 1 element?
    }
};
```

A bad implementation for the first binary search. The corner case is that if the array only contains 2 element, then from the beginning `lo == mid`, making the implementation wrong.
```c++
while (lo < hi) { // fixme: double-check whether < or <=
    int mid = (lo + hi) / 2; // fixme: check whether biased to the right
    if (nums[mid] < nums[hi]) { 
        hi = mid;
    }
    else {
        if (lo == mid) break;
        lo = mid;
    }
}
int pivot = lo;
```
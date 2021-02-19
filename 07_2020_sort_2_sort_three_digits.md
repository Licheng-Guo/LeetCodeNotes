# 75. Sort Color

Given an array with n objects colored red, white or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white and blue.

Here, we will use the integers 0, 1, and 2 to represent the color red, white, and blue respectively.

## Initial Attempt

This is a very chaotic first attempt. The lesson is that we should first be clear about the specific definition of the boundaries. Here `head` points to the first element right to the 0 region. The `tail` points to the first element left to the 2 region. We should realize that the region `[head, i)`should all be 1. As a result, when swapping with the `head`, we are guarantee that we will receive `1` in return, thus we should advance both `head` and `i`. However, when swapping with `tail`, we don't know what will be swapped in, thus we must not advance `i`. 

When `i` is equal to `tail`, this node is unprocessed yet, so we must still process it. However, after `i` is bigger than `tail` we must stop, because `tail` points to the last node in the 1 region, thus if we do the swap, we will swap the 1 into the 2 region.

```c++
    void sortColors(vector<int>& nums) {
        if (nums.size() == 0 or nums.size() == 1) return;
        
        int head = 0;
        int tail = nums.size()-1;
        
        for (int i = 0; i < nums.size() && i <= tail; i++) { // <= is very tricky
            if (nums[i] == 0) {
                swap(nums[i], nums[head]);
                head++;
            }
            else if (nums[i] == 2) {
                swap(nums[i], nums[tail]);
                tail--;
                i--; // this is very tricky
            }
        }
        return;
    }
```

## Reference Solution

The combination of for loop + while loop is very clever.
*** Note that the order of the two while loops cannot be flipped!!!
```c++
class Solution {
    public:
        void sortColors(int A[], int n) {
            int second=n-1, zero=0;
            for (int i=0; i<=second; i++) { // note the equal condition
                while (A[i]==2 && i<second) swap(A[i], A[second--]);
                while (A[i]==0 && i>zero) swap(A[i], A[zero++]);
            }
        }
    };
```

Bucket sort.
```c++
void sortColors(int A[], int n) {
    int num0 = 0, num1 = 0, num2 = 0;
    
    for(int i = 0; i < n; i++) {
        if (A[i] == 0) ++num0;
        else if (A[i] == 1) ++num1;
        else if (A[i] == 2) ++num2;
    }
    
    for(int i = 0; i < num0; ++i) A[i] = 0;
    for(int i = 0; i < num1; ++i) A[num0+i] = 1;
    for(int i = 0; i < num2; ++i) A[num0+num1+i] = 2;
}
```


## Attempt 2

First partition the array based on "whether the number is greater than 0"; then partition the right part based on "whether the number is greater than 1".

```c++
class Solution {
public:
    void sortColors(vector<int>& nums) {
        if (nums.size() == 0) return;
        
        int left = 0;
        int right = nums.size() - 1;
        
        left = partition(nums, left, right, 0);
        
        partition(nums, left, right, 1);
    }
    
private:
    int partition(vector<int> &nums, int left, int right, int pivot) {
        while (left <= right) {
            if (nums[left] > pivot && nums[right] == pivot) {
                swap(nums[left++], nums[right--]);
            }
            else if (nums[left] == pivot) {
                left++;
            }
            else if (nums[right] > pivot) {
                right--;
            }
        }
        
        return left;
    }
};
```
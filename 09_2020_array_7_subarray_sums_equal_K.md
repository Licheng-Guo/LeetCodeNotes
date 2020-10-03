# 560. Subarray Sum Equals K

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        
        unordered_map<int, int> freq;
        
        int sum = 0;
        int ans = 0;

        freq[k]++;
        
        for (int i = 0; i < nums.size(); i++) { // mistaken for .size()-1
            sum += nums[i];
                        
            if (freq.count(sum)) {
                ans += freq[sum];
            }
            freq[sum+k]++; // the position is tricky, it helps handle the corner case if an element is 0
        }
        
        return ans;
    }
};
```

# 523. Continuous Subarray Sum

What if the sum is an integer multiply of K?

## First Attempt

```c++
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        if (k < 0) k = -k;
        if (k == 0) {
            for (int i = 0; i < nums.size()-1; i++) {
                if (nums[i] == 0 && nums[i+1] == 0) return true;
            }
            return false;
        }
        if (k == 1) {
            return nums.size() > 1;
        }
        
        for (int i = 0; i < nums.size()-1; i++) {
            if (nums[i] == 0 && nums[i+1] == 0) return true;
        }
        
        int sum = accumulate(nums.begin(), nums.end(), 0);
        int n_max = sum / k;
        
        int prefix_sum = 0;
        unordered_map<int, int> targets;
        for( int j = 1; j <= n_max; j++) {
            targets[k * j + prefix_sum] = -1;  
        }
        
        for (int i = 0; i < nums.size(); i++) {
            prefix_sum += nums[i];
            
            if (targets.find(prefix_sum) != targets.end()) {
                if ( targets[prefix_sum] < i-1 ) {
                    return true;
                }
            }
            
            for( int j = 1; j <= n_max; j++) {
                if (targets.find(k * j + prefix_sum) == targets.end())
                    targets[k * j + prefix_sum] = i;  
            }
        }
        
        return false;
    }
};
```

## Reference Solution

If two prefix sum has the same remaining after taking the modulus of K, then there is a valid solution.

```c++
class Solution {
public:
    bool checkSubarraySum(vector<int>& nums, int k) {
        
        int prefix_sum = 0;
        unordered_map<int, int> targets;

        targets[0] = -1; // forgot this
        
        for (int i = 0; i < nums.size(); i++) {
                        
            prefix_sum += nums[i];
            if(k != 0) { // very tricky
                prefix_sum %= k;
            }        
            
            if (targets.find(prefix_sum) != targets.end()) {
                if ( targets[prefix_sum] < i-1 ) { // forgot this
                    return true;
                }
            }
            
            // note that we should not update the index of last seen position
            if (targets.find(prefix_sum) == targets.end()) {
                targets[prefix_sum] = i;                  
            }
        }
        
        return false;
    }
};
```
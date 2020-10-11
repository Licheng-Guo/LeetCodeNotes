# 528. Random Pick with Weight


You are given an array of positive integers w where w[i] describes the weight of ith index (0-indexed).

We need to call the function pickIndex() which randomly returns an integer in the range [0, w.length - 1]. pickIndex() should return the integer proportional to its weight in the w array. For example, for w = [1, 3], the probability of picking the index 0 is 1 / (1 + 3) = 0.25 (i.e 25%) while the probability of picking the index 1 is 3 / (1 + 3) = 0.75 (i.e 75%).

More formally, the probability of picking index i is w[i] / sum(w).

## First Attempt

```c++
class Solution {
public:
    // [1, 3]
    // sum: [1, 4]
    // 0 -> 0
    // 1, 2, 3 -> 1
    // corner case: 4 -> 1
    
    // initially did not include 0 as a boundary.
    Solution(vector<int>& w) : w(w), sum(w.size()+1, 0) {
        if (w.size()) {
            sum[0] = 0;
            for (int i = 1; i <= w.size(); i++) {
                sum[i] = sum[i-1] + w[i-1];
            }

            total = sum[w.size()];   
        }
    }
    
    int pickIndex() {
        if (w.size() == 0) return 0;
        // pre-compute the partial sum from w[0] to w[i]
        // if a random number between 0 and sum[n-1] falls between sum[i] and sum[i+1], then pick i 
        int r = rand() % total; 
        
        // do a binary search to find the target interval
        int left = 0; int right = w.size()-1;
        while (left <= right) { // forgot the equal condition
            int mid = (left + right) / 2;
            
            // check if r falls in range (sum[mid], sum[mid+1])
            if (sum[mid] <= r && r < sum[mid+1]) {
                return mid;
            }
            else if (r < sum[mid]) {
                right = mid-1;
            }
            else if (sum[mid+1] <= r) { // fix deadlock
                left = mid+1;
            }
            else {
                assert(0);
            }
        }
        
        assert(0);
        return 0;
        
    }
    
private:
    vector<int>& w;
    vector<int> sum;
    int total;
};
```
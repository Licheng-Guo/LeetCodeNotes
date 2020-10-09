# 347. Top K Frequent Elements

Given a non-empty array of integers, return the k most frequent elements.

## First Attempt

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        
        unordered_map<int, int> counter;
        for (int i = 0; i < nums.size(); i++) {
            counter[nums[i]]++;
        }
        
        vector<pair<int, int> > nums_freq;
        for (auto &kv : counter) {
            nums_freq.push_back(pair<int, int>(kv.second, kv.first) );
        }
                
        // left ------ right
        // sort from largest to the smallest
        int left = 0;
        int right = nums_freq.size()-1;
        while (left <= right) {  // should not use while(1) here
            int p = partition(nums_freq, left, right);
            if (p == k) {
                break;
            }
            else if (p < k) {
                left = p + 1;
            }
            else {
                right = p - 1;
            }
        }
        
        // return the answer
        vector<int> ans;
        for (int i = 0; i < k; i++) {
            ans.push_back(nums_freq[i].second);
        }
        return ans;
    }
    
private:
    // sort in reverse order
    // there may be repetition 
    int partition(vector<pair<int, int> >& nums_freq, int left, int right) {
        // choose nums_freq[left].first as the pivot
        int pivot = nums_freq[left].first;
        for (int i = left; i <= right; i++) {
            while (nums_freq[i].first < pivot && i < right) swap(nums_freq[i], nums_freq[right--]); // forgot the ++ and --
            while (nums_freq[i].first > pivot && i > left) swap(nums_freq[i], nums_freq[left++]);
        }
        
        return left;
    }
};
```

## Reference Solution

Use heap.

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        // use a counter: number -> freq
        
        // maintain a heap
        // push elements from the counter one-by-one into the heap
        // when the heap size is larger than K
        // with each push, pop the top element into the result vector
        
        unordered_map<int, int> counter;
        for (auto &n : nums) {
            counter[n]++;
        }
        
        // By default a max heap is created ordered by first element of pair.
        priority_queue<pair<int, int> > pq;
        vector<int> res;
        for (auto &kv : counter) {
            pq.push(make_pair(kv.second, kv.first));
            
            // got confused about the heap size
            // should not use nums.size()
            if (pq.size() > counter.size() - k) { 
                res.push_back(pq.top().second);
                pq.pop();
            }
        }
        
        return res;
    }
};
```

To create a min-heap:

```c++
typedef pair<int, int> pi; 
priority_queue<pi, vector<pi>, greater<pi> > pq;
```

If you have an array of size n and you want to build a heap from all items at once, Floyd's algorithm can do it with O(n) complexity. This corresponds to the std::priority_queue constructors that accept a container parameter.

```c++
vector<int> vec={3, 1, 4, 1, 5};
priority_queue<int> c3(std::less<int>(), vec);
```

If you have an empty priority queue to which you want to add n items, one at a time, then the complexity is `O(n * log(n))`.

So if you have all of the items that will go into your queue before you build it, then the first method will be more efficient. You use the second method--adding items individually--when you need to maintain a queue: adding and removing elements over some time period.

Removing n items from the priority queue also is `O(n * log(n))`.
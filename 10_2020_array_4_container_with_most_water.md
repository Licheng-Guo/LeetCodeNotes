# 11. Container with most water.

Given n non-negative integers a1, a2, ..., an , where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of the line i is at (i, ai) and (i, 0). Find two lines, which, together with the x-axis forms a container, such that the container contains the most water.

## First Attempt

```c++
class Solution {
public:
    int maxArea(vector<int>& height) {
        // find a, b such that
        // (b - a) * min(height(a), height(b) ) is maximized
        // for each pivot, only considers the bars that are higher
        // create an invert index mapping from y value to x value
        
        if (height.size() <= 1) return 0;
        
        vector<pair<int, int> > index;
        for (int i = 0; i < height.size(); i++) {
            index.push_back(pair<int, int>(height[i], i));
        }
        sort(index.begin(), index.end(), [](pair<int, int>& a, pair<int, int>& b) {
            return a.first > b.first;
        });
        
        int ans = 0;
        for (int i = 0; i < index.size(); i++) {
            for (int j = 0; j < i; j++) {
                int candidate = abs(index[i].second - index[j].second) * index[i].first; // got confused about index and value again
                if (candidate > ans) {
                    ans = candidate;
                }
            }
        }
        return ans;
    }
};
```

## Reference Solution

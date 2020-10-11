# 763. Partition Labels

A string `S` of lowercase English letters is given. We want to partition this string into as many parts as possible so that each letter appears in at most one part, and return a list of integers representing the size of these parts.

## First Attempt

Actually the sorting step is unnecessary. We simply iterate through the string, for each character we find its range and decide if we have a split or expand the current range.

```c++
class Solution {
public:
    vector<int> partitionLabels(string S) {
        if (S.length() == 0) return {0};

        vector<pair<int, int> > intervals(26, pair<int,int>(-1, -1));
        for (int i = 0; i < S.length(); i++) {
            if (intervals[S[i]-'a'].first == -1) {
                intervals[S[i]-'a'].first = i;
                intervals[S[i]-'a'].second = i; // forgot this!
            }
            else {
                intervals[S[i]-'a'].second = i;
            }
        }
        
        intervals.erase(remove(intervals.begin(), intervals.end(), pair<int,int>(-1,-1)), intervals.end());
        
        sort(intervals.begin(), intervals.end(), 
             [](pair<int, int>& a, pair<int, int>& b) {
                 return a.first < b.first;
             });
                
        pair<int, int> merge;
        merge = intervals[0];
        
        vector<int> ans;
        
        for (int i = 1; i < intervals.size(); i++) {
            if (intervals[i].first < merge.second) {
                // almost forgot this
                merge.second = max(intervals[i].second, merge.second);
            } 
            else {
                ans.push_back(merge.second - merge.first + 1);
                merge = intervals[i];
            }
        }
        ans.push_back(merge.second - merge.first + 1);
        
        return ans;
    }
};
```

## Reference Solution

This problem can be solved by greedy methods. First we record the last position for each character. Then we start from the beginning, for each letter we encounter, we set its last shown position as the temporary split position. If we encounter another letter that has a farther last position, we update the temporary split position. When we actually reach the temporary split position, that position becomes the actual split position.

```python
class Solution(object):
    def partitionLabels(self, S):

        # very tricky way to deal with repetition
        last = {c: i for i, c in enumerate(S)}
        j = anchor = 0
        ans = []
        for i, c in enumerate(S):
            j = max(j, last[c])
            if i == j:
                ans.append(i - anchor + 1)
                anchor = i + 1
            
        return ans
```
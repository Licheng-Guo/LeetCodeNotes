# 56. Merge Intervals

Given a collection of intervals, merge all overlapping intervals.

```
Example 1:

Input: [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlaps, merge them into [1,6].
```

## Use sorting

```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        sort(intervals.begin(), intervals.end(), 
             [](vector<int>& a, vector<int>& b ){ // use reference makes it significantly faster
            return a[0] < b[0];   
        });
                    
        vector<vector<int>> res;
        
        if (intervals.size() == 0) return {};
        
        // when the operation involves the comparison of 2 objects, be careful about if the total number of objects is only 1
        else if (intervals.size() == 1) return {intervals[0]}; // corner case
        
        int curr = 0;
        for (curr = 0; curr < intervals.size() - 1; curr++) {
            next = curr+1;
            if (intervals[curr][1] < intervals[next][0]) { // case 1
                res.push_back(intervals[curr]);
            }
            else if (intervals[curr][1] < intervals[next][1]) { // case 2
                intervals[next][0] = intervals[curr][0];
            }
            else { // case 3, previously miss this one
                intervals[next][0] = intervals[curr][0];
                intervals[next][1] = intervals[curr][1]; // can be merged with case 2 by using max
            }
        }
        
        res.push_back(intervals[curr]);
        
        return res;
    }
};
```

Sample solution 
```c++
class Solution {
public:
    static bool comp(const vector<int>& a, const vector<int>& b) {
        return a[0] < b[0];
    }
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int>> res;
        if (intervals.empty()) { // empty method of vector
            return res;
        }
        sort(intervals.begin(), intervals.end(), comp);
        res.push_back(intervals[0]);
        for (int i = 1; i < intervals.size(); i++) {
            if (res.back()[1] < intervals[i][0]) { // usage of back()
                res.push_back(intervals[i]);
            } else {
                res.back()[1] = max(res.back()[1], intervals[i][1]); // clever use of max
            }
        }
        return res;
    }
};
```

My attempt
```c++
class Solution {
public:
    vector<vector<int>> merge(vector<vector<int>>& intervals) {
        vector<vector<int> > res;
        if (intervals.size() == 0) {
            return res;
        }
        
        // forgot to sort
        sort(intervals.begin(), intervals.end(), comp);
        
        res.push_back(intervals[0]);
        for (int i = 0; i < intervals.size(); i++ ) {
            if (res.back()[1] < intervals[i][0]) {
                res.push_back(intervals[i]);
            }
            else {
                res.back()[1] = max(res.back()[1], intervals[i][1]);
            }
        }
        
        return res;
    }
    
private:
    static bool comp(vector<int>& a, vector<int>& b) {
        return a[0] < b[0];
    }
};
```

By declaring a function member as static, you make it independent of any particular object of the class. A static member function can be called even if no objects of the class exist and the static functions are accessed using only the class name and the scope resolution operator ::. A static member function can only access static data member, other static member functions and any other functions from outside the class. Static member functions have a class scope and they do not have access to the this pointer of the class. You could use a static member function to determine whether some objects of the class have been created or not.



We can also construct a graph to connect overlapping intervals and do a graph traversal.

# 57 Interval Insert

Given a set of non-overlapping intervals, insert a new interval into the intervals (merge if necessary).

You may assume that the intervals were initially sorted according to their start times.

Reference solution:
```c++
class Solution {
public:
    vector<vector<int>> insert(vector<vector<int>>& intervals, vector<int>& newInterval) {
        int index = 0;
        vector<vector<int>> res;
        
        while (index < intervals.size() && intervals[index][1] < newInterval[0]) {
            res.push_back(intervals[index]);
            index++; // forgot index++
        }
        
        // very clever!
        // standard approach to compute intersection, should memorize
        while (index < intervals.size() && intervals[index][0] <= newInterval[1]) { // forgot the equal case
            newInterval[0] = min(intervals[index][0], newInterval[0]);
            newInterval[1] = max(intervals[index][1], newInterval[1]);
            index++;
        }
        res.push_back(newInterval);
        
        while (index < intervals.size()) {
            res.push_back(intervals[index]);
            index++;
        }
        
        return res;
    }
};
```
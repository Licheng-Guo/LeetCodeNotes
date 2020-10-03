# 986. Interval List Intersections


Given two lists of closed intervals, each list of intervals is pairwise disjoint and in sorted order.

Return the intersection of these two interval lists.

## First Attempt

```c++
class Solution {
public:
    vector<vector<int>> intervalIntersection(vector<vector<int>>& A, vector<vector<int>>& B) {
        int ia= 0; int ib = 0;
        vector<vector<int> > ans;
        
        while (ia < A.size() && ib < B.size()) {
            vector<int>& curr_a = A[ia];
            vector<int>& curr_b = B[ib];
            
            if (curr_a[1] < curr_b[0]) {
                ia++;
            }
            else if (curr_a[0] > curr_b[1]) {
                ib++;
            }
            else {
                ans.push_back(getIntersect(curr_a, curr_b));
                if (curr_a[1] == curr_b[1]) {
                    ia++; ib++;
                }
                else if (curr_a[1] > curr_b[1]) {
                    ib++;
                }
                else {
                    ia++;
                }
            }
        }
        
        return ans;
    }
    
private:

    // min() & max() are very handy in computing intersects
    vector<int> getIntersect(vector<int>& a, vector<int>& b) {
        return {max(a[0], b[0]), min(a[1], b[1])};
    }
};
```
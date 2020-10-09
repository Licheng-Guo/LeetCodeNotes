# 221. Maximal Square

Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.

## First Attempt

```c++
class Solution {
public:
    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.empty()) return 0;
        int len_v = matrix.size();
        int len_h = matrix[0].size();
        
        // matrix[v][h]
        
        int max_v[len_v][len_h];
        int max_h[len_v][len_h];
        int max_sqr[len_v][len_h];
        
        max_v[0][0] = matrix[0][0] == '1' ? 1:0;
        max_h[0][0] = matrix[0][0] == '1' ? 1:0;
        max_sqr[0][0] = matrix[0][0] == '1' ? 1:0;
        
        for (int h = 1; h < len_h; h++) {
            max_h[0][h] = matrix[0][h] == '1' ? max_h[0][h] + 1 : 0;
            max_v[0][h] = matrix[0][h] == '1' ? 1 : 0;
            max_sqr[0][h] = max_v[0][h];
        }
        
        for (int v = 1; v < len_v; v++) {
            max_v[v][0] = matrix[v][0] == '1' ? max_v[v][0] + 1 : 0;
            max_h[v][0] = matrix[v][0] == '1' ? 1 : 0;
            max_sqr[v][0] = max_h[v][0];
        }
        
        for (int v = 1; v < len_v; v++) {
            for (int h = 1; h < len_h; h++) {
                max_v[v][h] = matrix[v][h] == '1' ? max_v[v-1][h] + 1 : 0;
                max_h[v][h] = matrix[v][h] == '1' ? max_h[v][h-1] + 1 : 0;
                if (matrix[v][h] == '1') {
                    max_sqr[v][h] = min(max_sqr[v-1][h-1], min(max_v[v-1][h], max_h[v][h-1])) + 1;
                }
                else {
                    max_sqr[v][h] = 0;
                }
            }
        }
        
        int ans = 0;
        for (int v = 0; v < len_v; v++) {
            for (int h = 0; h < len_h; h++) {
                if (max_sqr[v][h] > ans) {
                    ans = max_sqr[v][h];
                }
            }
        }
        
        return ans*ans;
        
    }
};
```
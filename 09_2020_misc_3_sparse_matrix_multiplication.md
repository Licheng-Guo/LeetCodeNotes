# 311. Sparse Matrix Multiplication

## Reference Idea

```c++
class Elem {
public:
    int row;
    int col;
    int val;
    
    Elem(int i, int j, int val) : row(i), col(j), val(val) {}
};

class Solution {
public:
    vector<vector<int>> multiply(vector<vector<int>>& A, vector<vector<int>>& B) {
        vector<Elem> sparse_A = getSparse(A);
        vector<Elem> sparse_B = getSparse(B);
        
        vector<vector<int> > res(A.size(), vector<int>(B[0].size(), 0)); //fixme corner case
        for (Elem a : sparse_A) {
            for (Elem b : sparse_B) {
                if (a.col == b.row) {
                    res[a.row][b.col] += a.val * b.val;
                }
            }
        }
        
        return res;
    }
    
private:
    vector<Elem> getSparse(vector<vector<int> >& A) {
        vector<Elem> res;
        
        for (int i = 0; i < A.size(); i++) {
            for (int j = 0; j < A[0].size(); j++) {
                if (A[i][j]) {
                    res.push_back(Elem(i, j, A[i][j]));
                }
            }
        }
        
        return res;
    }
};
```
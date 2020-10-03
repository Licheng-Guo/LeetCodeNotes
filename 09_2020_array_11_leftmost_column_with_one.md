# 1428. Leftmost Column with at Least a One

A binary matrix means that all elements are 0 or 1. For each individual row of the matrix, this row is sorted in non-decreasing order.

Given a row-sorted binary matrix binaryMatrix, return leftmost column index(0-indexed) with at least a 1 in it. If such index doesn't exist, return -1.

## First Attempt

```c++
/**
 * // This is the BinaryMatrix's API interface.
 * // You should not implement it, or speculate about its implementation
 * class BinaryMatrix {
 *   public:
 *     int get(int row, int col);
 *     vector<int> dimensions();
 * };
 */

class Solution {
public:
    int leftMostColumnWithOne(BinaryMatrix &binaryMatrix) {
        // record the current leftmost position
        // for each row, first check if the current leftmost position is 0
        // if so, skip this row
        // else use the current leftmost position as the left side start position and do binary search
        // corner case: all 0 matrix and all 1 matrix
        
        vector<int> dim = binaryMatrix.dimensions();
        int row = dim[0];
        int col = dim[1];
        
        int curr_col = col-1;
        bool is_all_0 = true;
        
        for (int i = 0; i < row; i++) {
            if (binaryMatrix.get(i, curr_col) == 0) {
                continue;
            }
            is_all_0 = false;
            
            int left = 0, right = curr_col;
            
            curr_col = getLeftMostOne(i, left, right, binaryMatrix); // bug redefine curr_col
        }
        
        if (is_all_0) {
            return -1;
        }
        else {
            return curr_col;
        }
    }
    
private:
    int getLeftMostOne(int row, int left, int right, BinaryMatrix &binaryMatrix) {
        while (left < right) { // double check if <=
            int mid = (left + right) / 2;
            if (binaryMatrix.get(row, mid) == 1) {
                right = mid;
            }
            else {
                left = mid+1;
            }
        }
        
        return right;
    }
};
```

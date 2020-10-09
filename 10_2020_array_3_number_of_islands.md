# 200. Number of Islands

Given a 2d grid map of '1's (land) and '0's (water), count the number of islands. An island is surrounded by water and is formed by connecting adjacent lands horizontally or vertically. You may assume all four edges of the grid are all surrounded by water.

## First Attempt

```c++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        if (grid.empty()) return 0;
        
        int row = grid.size();
        int col = grid[0].size();
        
        vector<vector<bool> > visited(row, vector<bool>(col, false));
        
        int count = 0;
        for (int i = 0; i < row; i++) {
            for (int jj = 0; jj < col; jj++) {
                if (!visited[i][jj] && grid[i][jj] == '1') {
                    DFS(grid, visited, i, jj);
                    count++;
                }
            } 
        }
        
        return count;
    }
    
private:
    void DFS(vector<vector<char>>& grid, vector<vector<bool> >& visited, int row, int col) {
        if (row < 0 || row >= grid.size()) return;
        if (col < 0 || col >= grid[0].size()) return;
        if (visited[row][col] == true) return; // this should be after the index check
        if (grid[row][col] == '0') return;
        
        
        visited[row][col] = true;
        DFS(grid, visited, row+1, col);
        DFS(grid, visited, row-1, col);
        DFS(grid, visited, row, col-1);
        DFS(grid, visited, row, col+1);
    }
};
```
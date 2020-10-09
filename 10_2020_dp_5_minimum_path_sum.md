# 64. Minimum Path Sum

Given a m x n grid filled with non-negative numbers, find a path from top left to bottom right which minimizes the sum of all numbers along its path.

## First Attempt

Note that in this example we could not use A* because the path cost may be zero! One potential fix is add 1 to all costs then we can apply a heuristic estimation function.

Since the problem already specify that only right or down movements are allowed, A* search is not necessary.

```c++
class Pos {
public:
    int row;
    int col;
    int path_cost;
    int esti_cost;
    
    Pos(int row, int col, int path_cost, int esti_cost): 
        row(row), col(col), path_cost(path_cost), esti_cost(esti_cost) {}
};

class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        // use a heap to store the wave front
        // store current path cost and estimated cost to target
        
        if (grid.empty()) return 0;
        int row_len = grid.size();
        int col_len = grid[0].size();
        
        auto comp = [](Pos a, Pos b) {return a.path_cost + a.esti_cost > b.path_cost + b.esti_cost;};
        priority_queue<Pos, vector<Pos>, decltype(comp)> pq(comp);
        pq.push(Pos(0, 0, grid[0][0]+1, row_len + col_len - 1));
        
        // initially forgot to set visited
        vector<vector<bool> > visited(row_len, vector<bool>(col_len, false));
        visited[0][0] = true;
        
        while (!pq.empty()) {
            Pos curr = pq.top(); pq.pop();
            // printf("%d %d %d %d\n", curr.row, curr.col, curr.path_cost, curr.esti_cost);
            
            if (curr.row == row_len-1 && curr.col == col_len-1) {
                return curr.path_cost - row_len - col_len + 1;
            }
            
            if (curr.row+1 < row_len && visited[curr.row+1][curr.col] == false) {
                pq.push(Pos(curr.row+1, curr.col, curr.path_cost+grid[curr.row+1][curr.col]+1, curr.esti_cost-1));
                visited[curr.row+1][curr.col] = true;
            }
                
            if (curr.col+1 < col_len && visited[curr.row][curr.col+1] == false) {
                pq.push(Pos(curr.row, curr.col+1, curr.path_cost+grid[curr.row][curr.col+1]+1, curr.esti_cost-1));
                visited[curr.row][curr.col+1] = true;
            }
                
        }
        
        return 0;
    }
};
```

## Second Attempt

```c++
class Solution {
public:
    int minPathSum(vector<vector<int>>& grid) {
        if (grid.empty()) return 0;
        
        int row = grid.size();
        int col = grid[0].size();
        
        for (int i = 1; i < row; i++) {
            grid[i][0] = grid[i-1][0] + grid[i][0];
        }
        
        for (int j =1; j < col; j++) {
            grid[0][j] += grid[0][j-1];
        }
        
        // should start from 1 not 0
        for (int i = 1; i < row; i++) {
            for (int jj = 1; jj < col; jj++) {
                // forgot +=
                grid[i][jj] += min(grid[i-1][jj], grid[i][jj-1]);
            }
        }
        
        return grid[row-1][col-1];
    
    }
};
```
# 797. All Paths From Source to Target

Given a directed acyclic graph of N nodes. Find all possible paths from node 0 to node N-1, and return them in any order.

The graph is given as follows:  the nodes are 0, 1, ..., graph.length - 1.  graph[i] is a list of all nodes j for which the edge (i, j) exists.

## First Attempt

DFS based solution:
```c++
class Solution {
public:
    vector<vector<int>> allPathsSourceTarget(vector<vector<int>>& graph) {
        int num = graph.size();
        vector<int> curr_path;
        vector<vector<int> > all_paths;
        vector<bool> visited(num, false);
        DFS(0, num-1, curr_path, all_paths, visited, graph);
        
        return all_paths;
    }

private:
    void DFS(int curr, int dst, 
             vector<int>& curr_path, 
             vector<vector<int> >& all_paths, 
             vector<bool>& visited,
             vector<vector<int>>& graph) {

        // note that since the graph is acyclic, and our goal is finding all paths instead of visiting each node once, we don't need to check if the node has been visited
        // if (visited[curr]) {
        //     return;
        // }
        
        // visited[curr] = true;
        curr_path.push_back(curr);
        
        if (curr == dst) {
            all_paths.push_back(curr_path);
            // this is not the end. Should backtrack and explore other paths
        }
        else {
            for (auto &n : graph[curr]) {
                DFS(n, dst, curr_path, all_paths, visited, graph);
            }
        }
        
        // *** this is very tricky ***
        // visited[curr] = false;
        curr_path.pop_back();
        return;
    }
};
```
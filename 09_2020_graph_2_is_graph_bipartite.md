# 785. Is Graph Bipartite?

One common mistake is that in DFS we should not manually visit the root node outside the DFS routine; while in BFS we should separate marking as visted from really visiting it. In BFS we should initially push the root into the queue and mark it as visited, but should not really visit it.

```c++
class Solution {
public:
    bool isBipartite(vector<vector<int>>& graph) {
        assert (!graph.empty());
        
        vector<int> loc(graph.size(), -1);
        
        // must not visit the root first in DFS!
        // should differentiate from BFS where we must first enqueue the root element 
        
        for (int i = 0; i < graph.size(); i++) {
            // only DFS on nodes that have not been visited before
            if (loc[i] == -1 && !DFS(graph, loc, i, 1)) return false;
        }
        return true;
    }

private:
    bool DFS(vector<vector<int>>& graph, vector<int>& loc, int curr, int prev) {
        // the checking process happens when visiting an already-visited node.
        if (loc[curr] != -1) {
            if (loc[curr] == prev) {
                return false;
            }
            else {
                return true; // forgot this
            }
        }

        loc[curr] = 1-prev;
        
        for (int next : graph[curr]) {
            if (!DFS(graph, loc, next, loc[curr])) {
                return false;
            }
        }
        
        return true;
    }
};
```
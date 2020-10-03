# 310. Minimum Height Trees

For an undirected graph with tree characteristics, we can choose any node as the root. The result graph is then a rooted tree. Among all possible rooted trees, those with minimum height are called minimum height trees (MHTs). Given such a graph, write a function to find all the MHTs and return a list of their root labels.

The basic idea is easy to think of. We can layer by layer peel off the leaves until the roots. However, the implementation is not so easy.

First, we should choose the right data structure for graph problems. In our situation we need to remove neighbor nodes, thus `unordered_set` is suitable

## Initial Attempt That Exceeds Timing Limit

```c++
class Solution {
public:

  vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
    if (edges.size() == 0) {
      return vector<int>(1, 0);
    }
    
    vector<int> edge_cnt(n, 0);
    for (auto e:edges) {
      edge_cnt[e[0]]++;
      edge_cnt[e[1]]++;
    }
    
    vector<bool> removed(edges.size(), false);
    int remove_cnt = 0;
    int total_edge = edges.size();
    vector<int> results;
    while(remove_cnt < total_edge) {
      results.clear();
      vector<int> to_reduce;
      for (int i = 0; i < edges.size(); i++) {
        if (removed[i]) {
          continue;
        }
        
        auto e = edges[i];
        if (edge_cnt[e[0]] == 1) {
          to_reduce.push_back(e[0]);
          to_reduce.push_back(e[1]);
          removed[i] = true;
          remove_cnt++;
          results.push_back(e[1]);

        }
        if (edge_cnt[e[1]] == 1) {
          to_reduce.push_back(e[0]);
          to_reduce.push_back(e[1]);
          removed[i] = true;
          remove_cnt++;
          results.push_back(e[0]);
        }
      }
        
      // pay attention to when to update in layered processing
      for (auto t:to_reduce) {
        edge_cnt[t]--;
      }
    }
    
    set<int> temp(results.begin(), results.end());
    return vector<int>(temp.begin(), temp.end());
  }
}    
```

One lesson from my initial attempt is the updating order in layered processing. When we try to peel off the outmost layer, we must not start updating the inner nodes until all nodes in the outmost layer have been processed. otherwise the inner nodes may be treated as if they are also in the outmost layer.

One paticular clumsy factor about this implementation is how we obtain the current leaf layer. In this code it is done quite inefficiently --- every time we iterate through all the nodes and check the neighbor count. Instead, we can notice that the second outmost layer are directly connected to the current outmost layer, thus we can obtain the next outmost layer while iterate through the current outmost layer.

```c++
class Solution {
public:

    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        if (n == 1) {
            return vector<int>({0});
        }
        
        vector<unordered_set<int>> graph(n);
        for (auto& e:edges) {
            graph[e[0]].insert(e[1]);
            graph[e[1]].insert(e[0]);
        }
        
        unordered_set<int> curr;
        unordered_set<int> next;
        
        for (int i = 0; i < n; i++) {
            if (graph[i].size() == 1) {
                curr.insert(i);
            }
        }
        
        int remaining_node = n;
        
        while(remaining_node > 2) {
            remaining_node -= curr.size();
            for (int leaf : curr) {
                int neighbor = *(graph[leaf].begin());
                graph[neighbor].erase(leaf);
                if (graph[neighbor].size() == 1) {
                    next.insert(neighbor);
                }
            }
            
            curr = std::move(next);
            next.clear();
        }
        
        vector<int> result;
        for (auto n:curr) {
            result.push_back(n);
        }
        return result;
    }
};
```

Another potentially efficient implementation is to keep the edge-based representation of the graph. Meanwhile, we also keep the number of edges for each node. In each iteration, we go through all the current valid edges, get rid of the leaf edge, and push the remaining edges into the vector for the processing of the next round.

BFS based implementation.
```c++
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        vector<unordered_set<int>> graph(n, unordered_set<int>());
        for (auto &e : edges) {
            graph[e[0]].insert(e[1]);
            graph[e[1]].insert(e[0]);
        }
        
        queue<int> q;
        int remain = n;
        
        for (int i = 0; i < n; i++) {
            if (graph[i].size() <= 1) {
                q.push(i);
            }
        }
        
        while (!q.empty()) {
            int size = q.size();
            if (remain <= 2) { // cannot use queue size to determine if finished
                break;
            }

            unordered_set<int> candidates;
            
            for (int i = 0; i < size; i++) {
                int curr = q.front(); q.pop();
                
                for (auto &neighbor : graph[curr]) {
                    graph[neighbor].erase(curr); // unordered_set -> erase / insert
                    remain--;      
                    candidates.insert(neighbor);
                }    
            }
            
            // cannot check among all nodes, otherwise the already erased nodes will be recounted.
            for (auto &c : candidates) {
                if (graph[c].size() <= 1){ 
                    q.push(c);
                }
            }   
        }
        
        vector<int> result;
        while (!q.empty()) {
            result.push_back(q.front());
            q.pop();
        }
        
        return result;
        
    }
};
```

Comment: don't know why previously I used the `candidates` set. Also confused why I used `<= 1`. The following code also works:

```c++
class Solution {
public:
    vector<int> findMinHeightTrees(int n, vector<vector<int>>& edges) {
        vector<unordered_set<int>> graph(n, unordered_set<int>());
        for (auto &e : edges) {
            graph[e[0]].insert(e[1]);
            graph[e[1]].insert(e[0]);
        }
        
        queue<int> q;
        int remain = n;
        
        if (n == 2) {return edges[0];}
        if (n == 1) {return {0}; }
        
        for (int i = 0; i < n; i++) {
            if (graph[i].size() == 1) {
                q.push(i);
            }
        }
        
        while (!q.empty()) {
            int size = q.size();
            if (remain <= 2) { // cannot use queue size to determine if finished
                break;
            }

            unordered_set<int> candidates;
            
            for (int i = 0; i < size; i++) {
                int curr = q.front(); q.pop();
                
                for (auto &neighbor : graph[curr]) {
                    graph[neighbor].erase(curr); // unordered_set -> erase / insert
                    remain--;      
                    if (graph[neighbor].size() == 1) {
                        q.push(neighbor);
                    }
                }    
            }
        }
        
        vector<int> result;
        while (!q.empty()) {
            result.push_back(q.front());
            q.pop();
        }
        
        return result;
        
    }
};
```
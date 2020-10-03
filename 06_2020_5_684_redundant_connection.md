# 684 Redundant Connection 

In this problem, a tree is an undirected graph that is connected and has no cycles.

The given input is a graph that started as a tree with N nodes (with distinct values 1, 2, ..., N), with one additional edge added. The added edge has two different vertices chosen from 1 to N, and was not an edge that already existed.

The resulting graph is given as a 2D-array of edges. Each element of edges is a pair [u, v] with u < v, that represents an undirected edge connecting nodes u and v.

Return an edge that can be removed so that the resulting graph is a tree of N nodes. If there are multiple answers, return the answer that occurs last in the given 2D-array. The answer edge [u, v] should be in the same format, with u < v.

## Initial Attempt

```c++
class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        int n = 0;
        for (auto & e : edges) {
            if (e[0] > n) n = e[0];
            if (e[1] > n) n = e[1];
        }
        
        vector<int> degree(n+1, 0); // forgot to allocate n+1 elements
        vector<vector<int> > neighbors(n+1, vector<int>()); // initialize vector of vector
        for (auto & e : edges) {
            degree[e[0]]++;
            degree[e[1]]++;
            neighbors[e[0]].push_back(e[1]);
            neighbors[e[1]].push_back(e[0]);
        }
        
        queue<int> q;
        for (int i = 1; i <= n; i++) {
            if (degree[i] == 1) {
                q.push(i);
            }
        }
        
        while (!q.empty()) {
            int curr = q.front(); q.pop();
            int next = neighbors[curr][0];
            
            degree[curr]--;
            degree[next]--;
            neighbors[next].erase(find(neighbors[next].begin(), neighbors[next].end(), curr));
            neighbors[curr].erase(find(neighbors[curr].begin(), neighbors[curr].end(), next));
            
            if (degree[next] == 1) {
                q.push(next);
            }
        }
        
        for (auto itr = edges.rbegin(); itr != edges.rend(); itr++) {
            // note the bracket around the pointer dereference
            if (degree[(*itr)[0]] && degree[(*itr)[1]]) {
                return *itr;
            }
        }
        return {0, 0};
    }
};
```

## Second Attempt Based on BFS

Always avoid modifying the container while iterating over it.

```c++
class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        
        assert(edges.size() <= 1000);
        vector<unordered_set<int> > graph(2000, unordered_set<int>()); // fixme
        for (auto &e : edges) {
            graph[e[0]].insert(e[1]);
            graph[e[1]].insert(e[0]);
        }
        
        queue<int> q;
        for (int i = 0; i < graph.size(); i++) {
            if (graph[i].size() == 1) {
                q.push(i);
            }
        }
                        
        while (!q.empty()) {
            int size = q.size();
                        
            vector<int> candidates;
            vector<int> gabage;
            for (int i = 0; i < size; i++) {
                int curr = q.front(); q.pop();
                
                assert(graph[curr].size() == 1);
                
                for (auto &n : graph[curr]) {
                    graph[n].erase(curr);
                    // graph[curr].erase(n); // *** dangerous ***
                    gabage.push_back(curr);
                    candidates.push_back(n);                    
                }
            }
            
            for (auto &c : candidates) {
                if (graph[c].size() == 1) {
                    q.push(c);
                }
            }
            
            for (auto &g : gabage) {
                graph[g].clear();
            }
        }
        
        // wrong method to get edges in the loop
//         // get the vertices in the loop
//         vector<int> loop;
//         for (int i = 0; i < graph.size(); i++) {
//             if (graph[i].size() > 1) {
//                 loop.push_back(i);
//                 printf("%d\n", i);
//             }
//         }
        
//         // get the edges in the loop
//         sort(loop.begin(), loop.end());
//         vector<vector<int>> loop_edges;
//         for (int i = 0; i < loop.size()-1; i++) {
//             loop_edges.push_back({loop[i], loop[i+1]});
//         }
//         loop_edges.push_back({loop[0], loop.back()});
  
        // get the vertices in the loop
        unordered_set<int> loop;
        for (int i = 0; i < graph.size(); i++) {
            if (graph[i].size() > 1) {
                loop.insert(i);
            }
        }
        
        // redundant. Please see the first attempt
        for (auto it = edges.rbegin(); it != edges.rend(); it++) {
            int s = (*it)[0];
            int t = (*it)[1];
            if (loop.find(s) != loop.end() && loop.find(t) != loop.end()) {
                return *it;
            }
        }
        assert(false);
        return {0, 0};
        
    }
};
```

## Third Attempt Based on DFS

Note that in undirected graph we don't need to differentiate cycle from reconvergent paths, thus we only need 1 bit for visitness.

```c++
class Solution {
public:
    vector<int> findRedundantConnection(vector<vector<int>>& edges) {
        
        assert(edges.size() <= 1000);
        vector<unordered_set<int> > graph(2000, unordered_set<int>()); // fixme
        for (auto &e : edges) {
            
            // corner case: loop of 2 nodes
            if (find(graph[e[0]].begin(), graph[e[0]].end(), e[1]) != graph[e[0]].end()) {
                return e;
            }
            graph[e[0]].insert(e[1]);
            graph[e[1]].insert(e[0]);
        }
        
        vector<int> loop;
        vector<int> visited(2000, 0);
        // visited[1] = 1; //this is wrong! have not visit it yet
        
        DFS(loop, 1, graph, visited, -1);
        
        // *** remove the part beyond the loop ***
        // the recorded path will contain nodes outside of the loop
        for (int i = 1; i < loop.size(); i++) {
            if (loop[i] == loop[0]) {
                loop.erase(loop.begin()+i, loop.end());
                break;
            }
        }
        
        for (auto it = edges.rbegin(); it != edges.rend(); it++) {
            if (find(loop.begin(), loop.end(), (*it)[0]) != loop.end() &&
                find(loop.begin(), loop.end(), (*it)[1]) != loop.end() ) {
                return *it;
            }
        }
        
        assert(false);
        return { 0, 0};
    }
    
private:
    void DFS(vector<int>& loop, int curr, vector<unordered_set<int> >& graph, vector<int>& visited, int prev) {
        printf("%d\n", curr);
        
        if (visited[curr] == 1) {
            merge_point = curr;
            loop.push_back(curr);
            return;
        }
        // *** this is not necessary in undirected graph ***
        // else if (visited[curr] == -1) {
        //     return;
        // }
    
        visited[curr] = 1;
        
        for(auto &child : graph[curr]) {
            if (child == prev) {
                continue;
            }
            
            DFS(loop, child, graph, visited, curr);   
            
            // note the position of collect path is after the recursive call
            if (merge_point) {
                loop.push_back(curr);
                return;
            }
        }
        
        visited[curr] = -1;
    }
    
    int merge_point = 0;
};
```

# 685 Redundant Connection 2

What if the graph is directed?

```c++
class Solution {
public:
    vector<int> findRedundantDirectedConnection(vector<vector<int>>& edges) {
        // if there is one node with indgree 0
        // then there must be a node with indgree 2
        // remove one of the incoming edges
        // if there is no node with indegree 0
        // then there must be a loop in the graph
        // find the loop, removing anyone will leave a valid solution
        
        int n = 0;
        for (auto &e : edges) {
            if (e[0] > n) {
                n = e[0];
            }
            if (e[1] > n) {
                n = e[1];
            }
        }
        vector<int> indegree(n + 1, 0);
        vector<int> outdegree(n + 1, 0);
        vector<vector<int>> in_edges(n + 1, vector<int>());
        for (auto &e : edges) {
            indegree[e[1]]++;
            outdegree[e[0]]++;
            in_edges[e[1]].push_back(e[0]);
        }
        
        //auto root = find(indegree.begin(), indegree.end(), 0); 
        int root = -1;
        for (int i = 1;  i<= n; i++) {
            if (indegree[i] == 0) {
                root = i;
                break;
            }
        }
        if (root != -1) {
            // printf("node %d has no input edge\n", root);
            for (int i = 1; i <= n; i++) {
                if (indegree[i] == 2) {
                    int p1 = in_edges[i][0];
                    int p2 = in_edges[i][1];
                    
                    bool find_root = false;
                    int next = p1;
                    while (next != i) {
                        if (next == root) {
                            find_root = true;
                            break;
                        }
                        else {
                            next = in_edges[next][0];
                        }
                    }
                    if (find_root) {
                        return {p2, i};
                    }
                    else {
                        return {p1, i};
                    }
                    
                    // for (auto itr = edges.rbegin(); itr != edges.rend(); itr++) {
                    //     if ((*itr)[0] == p1 && (*itr)[1] == i) {
                    //         return {p1, i};
                    //     }
                    //     else if ((*itr)[0] == p2 && (*itr)[1] == i) {
                    //     return {p2, i};
                    //     }
                    // }
                    // assert(0);
                }
            }
            assert(0);
        }
        else {
            // find the loop
            queue<int> q;
            for (int i = 1; i <= n; i++) {
                if (0 == outdegree[i]) {
                    q.push(i);
                }
            }
            
            while (!q.empty()) {
                int curr = q.front(); q.pop();
                // printf("remove node %d\n", curr);
                int parent = in_edges[curr][0];
                outdegree[parent]--;
                indegree[curr]--;
                if (outdegree[parent] == 0) {
                    q.push(parent);
                }
            }
            
            vector<vector<int>> loop;
            for (int i = 1; i <= n; i++) {
                if (indegree[i] == 1) {
                    assert(outdegree[i] == 1);
                    loop.push_back({in_edges[i][0], i});
                    // printf("edge %d -> %d in a loop\n", in_edges[i][0], i);
                }
            }
            
            for (auto i = edges.rbegin(); i != edges.rend(); i++) {
                if (find(loop.begin(), loop.end(), *i) != loop.end()) {
                    return *i;
                }
            }
            assert(0);
        }
        
        return {0, 0};
    }
};
```

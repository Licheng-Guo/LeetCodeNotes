# 332 Reconstruct Itinerary

Given a list of airline tickets represented by pairs of departure and arrival airports [from, to], reconstruct the itinerary in order. All of the tickets belong to a man who departs from JFK. Thus, the itinerary must begin with JFK.

Note:

1. If there are multiple valid itineraries, you should return the itinerary that has the smallest lexical order when read as a single string. For example, the itinerary ["JFK", "LGA"] has a smaller lexical order than ["JFK", "LGB"].
2. All airports are represented by three capital letters (IATA code).
3. You may assume all tickets form at least one valid itinerary.
4. One must use all the tickets once and only once.

## Analysis

The key question that has trapped me is whether there are "dead ends" in the graph. If so, we need to implement the backtrack facility to reverse back from the deadend. However, in this case there are no dead ends. Indeed we have branches, but as long as we follow the lexical order at branching points we are good. We may first follow one branch to reach one end, then follow another branch to reach another end.



```c++
class Solution {
public:
    vector<string> findItinerary(vector<vector<string>>& tickets) {
        ticket_num =  tickets.size();
        visited_num = 0;

        // unordered_map cannot take vector as key
        unordered_map<string, vector<string> > graph;
        unordered_map<string, vector<bool> > visited;
        for (auto t : tickets) {
            if (graph.find(t[0]) == graph.end()) {
                graph.insert(pair<string, vector<string> >(t[0], vector<string>()) );
                visited.insert(pair<string, vector<bool> >(t[0], vector<bool>()) );                
            }
                
            graph[t[0]].push_back(t[1]);
            visited[t[0]].push_back(false);
        }
        
        vector<string> route;
        route.push_back("JFK");
        DFS("JFK", route, visited, graph);
        
        return route;
    }

    
    bool DFS(
            string curr, 
            vector<string>& route,
            unordered_map<string, vector<bool> >& visited,
            unordered_map<string, vector<string> >& graph) {
        vector<string> next_steps = graph[curr];

        // *** pay attention to the reference here ***
        vector<bool>& curr_visited = visited[curr];
        
        std::sort(next_steps.begin(), next_steps.end());
        
        if (visited_num == ticket_num) {
            return true;
        }
        
        bool succeed = false;
        for (int i = 0; i < next_steps.size(); i++) {
            string t = next_steps[i];
            if (curr_visited[i]) {
                continue;
            }
            
            succeed = true;
            curr_visited[i] = true;
            visited_num++;
            route.push_back(t);
            
            bool status = DFS(t, route, visited, graph);
            
            if (status == false) {
                // backtrack
                succeed = false;
                curr_visited[i] =false;
                visited_num--;
                route.pop_back();   
            }
        }
        
        return succeed;
    }
    
    int visited_num;
    int ticket_num;
    
    
    
};
```

## No Need for Backtracking

First keep going forward until you get stuck. That's a good main path already. Remaining tickets form cycles which are found on the way back and get merged into that main path. By writing down the path backwards when retreating from recursion, merging the cycles into the main path is easy - the end part of the path has already been written, the start part of the path hasn't been written yet, so just write down the cycle now and then keep backwards-writing the path.

```c++
class Solution {
    
    void
    dfs(unordered_map<string, multiset<string>>& vertices, string node, vector<string>& sol) {
            
        auto it = vertices.find(node);
        
        if (it != vertices.end()) {
            while (!it->second.empty()) {
                auto neighbour = it->second.begin();
                string n = *neighbour;

                // this is very clever, which avoides dead loops.
                it->second.erase(neighbour);
                dfs(vertices, n, sol);
            }
        }
        
        sol.push_back(node);
    }
        
public:
    vector<string> findItinerary(vector<vector<string>>& tickets) {
        vector<string> sol;
        
        unordered_map<string, multiset<string>> vertices;
        
        for (int i = 0; i < tickets.size(); ++i) {
            vertices[tickets[i][0]].insert(tickets[i][1]);
        }
                
        dfs(vertices, "JFK", sol);
        
        reverse(sol.begin(), sol.end());
            
        return sol;
    }
};
```

My attempt. It is very tricky and dangerous to change the container while iterating over it. The reference solution use the while loop which is the correct way. We should not use a for loop if we are going to change the container in the loop body. Seems that we don't need to explicitly initilize the multiset, unlike vectors.

```c++
class Solution {
public:
    vector<string> findItinerary(vector<vector<string>>& tickets) {
        unordered_map<string, multiset<string> > graph;
        for (auto &t : tickets) {
            // no need. the container will create a new element and return the reference
            //
            // if (graph.find(t[0]) == graph.end()) {
            //     graph[t[0]] = multiset<string>();
            // }
            graph[t[0]].insert(t[1]);
        }
        
        vector<string> sol;
        
        DFS(sol, "JFK", graph);
        
        reverse(sol.begin(), sol.end());
        
        return sol;
    }
    
    void DFS(vector<string>& sol, string curr, unordered_map<string, multiset<string> >& graph) {
        // printf("visit %s\n", curr.c_str());
        if (graph.find(curr) != graph.end()) {
            // dangerous operation: change the target while looping over it
            // for(auto &t : graph[curr]) { 
            //     graph[curr].erase(t);
            //     DFS(sol, t, graph);
            // }
            
            while (!graph[curr].empty()) {
                auto next = graph[curr].begin();
                string next_str = *next;
                graph[curr].erase(next);
                DFS(sol, next_str, graph);
            }
        }
        
        sol.push_back(curr);
    }
};
```

The lesson is that we should remember the possibility of `post-order DFS`.

Note that in this question we are allowed to visit the same vertex multiple times but each edge should only be visited once. We may also use a two-level map to track whether an edge has been visited before.
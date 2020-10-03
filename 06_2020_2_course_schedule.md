# 207 Course Schedule

There are a total of `numCourses` courses you have to take, labeled from 0 to `numCourses-1`.

Some courses may have prerequisites, for example to take course 0 you have to first take course 1, which is expressed as a pair: `[0,1]`

Given the total number of courses and a list of prerequisite pairs, is it possible for you to finish all courses?

## Initial Attempt
If we use DFS to detect loops in a graph, the implementation of `visited` is a little different from usual. When we backtrack, we must remove the current node from the `visited` set. This is because the "visited" set we need to know must be the ones that are in the same searching path as the current visiting node. In other words, we must distinguish cycles from reconvergent paths.

```c++
class Course {
public:
    int id;
    std::set<Course*> pre; // no need to "new"
    
    Course(int id) {
        this->id = id;
    }
    
    void add_dependency(Course* dep_course) {
        pre.insert(dep_course); // "set" should use "insert"
    }
}; // ";" after class definition

class Solution {
public:
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<Course*> all_course;
        for (int i = 0; i < numCourses; i++) {
            all_course.push_back(new Course(i));
        }
        
        for (vector<int> req : prerequisites) {
            Course* junior = all_course[req[0]];
            Course* senior = all_course[req[1]];
            senior->add_dependency(junior);
        }
        
        for (Course* curr_course : all_course) {
            // to avoid redundant check
            if (checked.find(curr_course) != checked.end()) {
                continue;
            }
            
            visited.clear();
            if (hasCycle(curr_course)) {
                return false;
            }
        }
        return true;
    }
    

    bool hasCycle(Course* curr) {
        if (visited.find(curr) != visited.end()) {
            return true;
        }
        
        visited.insert(curr);
        
        for (Course* next : curr->pre) {
            if (hasCycle(next)) {
                return true;
            }
        }
        
        // when backtrack, must delete the trace of current visit
        visited.erase(visited.find(curr));
        
        checked.insert(curr);
        return false;
    }
    
    std::set<Course*> visited;
    std::set<Course*> checked;
};
```

Or we can use different marks to represent different visiting status, which is more efficient than using a set. This approach also reduceds redundant work. In my initial approach this clever feature is not included.

```python
def canFinish(self, numCourses, prerequisites):
    graph = [[] for _ in xrange(numCourses)]
    visit = [0 for _ in xrange(numCourses)]
    for x, y in prerequisites:
        graph[x].append(y)
    def dfs(i):
        if visit[i] == -1:
            return False
        if visit[i] == 1:
            return True
        visit[i] = -1
        for j in graph[i]:
            if not dfs(j):
                return False
        visit[i] = 1
        return True
    for i in xrange(numCourses):
        if not dfs(i):
            return False
    return True
```

- if node v has not been visited, then mark it as 0.
- if node v is being visited, then mark it as -1. If we find a vertex marked as -1 in DFS, then their is a ring.
- if node v has been visited, then mark it as 1. If a vertex was marked as 1, then no ring contains v or its successors.

My version:
First, must remember to use reference when passing array. Second, in my initial attempt for optimization, I only start at those nodes that have zero indegrees. However, this is not enough because it is possible that the loop has no inbound connection fron the other part of the graph. The solution is to utilize the visiting status of the node.

```c++
class Solution {
public:
    bool DFS(int num, vector<int>& visited, unordered_map<int, vector<int>>& edges) { // forgot to add reference
        if (visited[num] == VISITED_CURR) {
            return false;
        }
        else if (visited[num] == VISITED_PREV) {
            return true;
        }
        
        visited[num] = VISITED_CURR;
        for (auto &child : edges[num]) {
            if (!DFS(child, visited, edges)) {
                return false;
            }
        }
        visited[num] = VISITED_PREV;
        return true;
    }
    
    
    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> visited(numCourses, 0);
        unordered_map<int, vector<int>> edges;
        for (auto &e : prerequisites) {
            edges[e[1]].push_back(e[0]);
        }
        
        for (int i = 0; i < numCourses; i++) {
            if (visited[i] != 0) {
                continue;
            }
            
            if(!DFS(i, visited, edges)) {
                return false;
            }
        }
        return true;
    }
private:

    const int VISITED_CURR = -1;
    const int VISITED_PREV = 1;
};
```

## Utilize Indegree

- For a cycle of nodes, we cannot find a node with an indegree of 0. 
- If we remove a node and the associated edges, we will not create new cycles.
- Thus we can repeatedly check if there is at least one node with an indegree of 0, then remove that node.
- To logically remove the node, we can set a mark on it, then decrease the indegree of all its neighbors. In the next round, we will skip the marked nodes.

My implementation for the follow-up question (210. Course Schedule II), which requires us to find an ordering of taking the course.

Comment: this is a bad implementation, should use BFS.

```c++
class Solution {
public:
    vector<int> findOrder(int numCourses, vector<vector<int>>& prerequisites) {
        vector< vector<int> > graph(numCourses, vector<int>()); // user vector<int> test(10) to initialize the vector to size 10.
        vector<int> indegree(numCourses, 0);
        for(vector<int> dep : prerequisites) {
            int junior = dep[1];
            int senior = dep[0];

            // be careful about the direction of the edges
            graph[junior].push_back(senior);
            indegree[senior]++;
        }
        
        vector<int> order;
        
        for (int i = 0; i < numCourses; i++) {
            bool success = findNextCourse(numCourses, order, graph, indegree);
            if (!success) {
                return vector<int>();
            }
        }

        return order;
    }
    
    bool findNextCourse(int numCourses, vector<int>& order, vector< vector<int> >& graph, vector<int>& indegree) {
        for (int i = 0; i < numCourses; i++) {
            if (indegree[i] == 0) {
                order.push_back(i);        
                indegree[i] = -1;
                for (int neighbor : graph[i]) {
                    indegree[neighbor]--;
                }
                return true;
            }
        }
        return false;
    }
};
```

One optimization to the code is to use BFS. In each iteration, we enqueue all the nodes with an indegree of 0.

The lesson of this solution is trying to look for equivalent properties of the problem. We should also think about `iteratively checking the condition and simplifying the system`.
- Originally we want to detect the existence of cycles in a graph
- If a graph does not have a cycle, it will still not after removing one node of the graph
- We can first check if the original graph is by itself a large cycle
- If not, we remove a node and check if the residual graph is by itself a cycle
- We must make sure that the node we remove is impossible to be in any cycle
- The property we rely on is that if a node has an indegree of zero, it is impossible to be in a loop.

## Another Sample Implementation

BFS can also be utilized for topological sort

```c++
bool canFinish(int n, vector<pair<int, int>>& pre) {
    vector<vector<int>> adj(n, vector<int>());
    vector<int> degree(n, 0);
    for (auto &p: pre) {
        adj[p.second].push_back(p.first);
        degree[p.first]++;
    }
    queue<int> q;
    for (int i = 0; i < n; i++)
        if (degree[i] == 0) q.push(i);
    while (!q.empty()) {
        int curr = q.front(); q.pop(); n--; // should use "front" with queue
        for (auto next: adj[curr])
            if (--degree[next] == 0) q.push(next);
    }
    return n == 0;
}
```
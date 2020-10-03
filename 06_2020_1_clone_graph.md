# 133. Clone Graph

Given a reference of a node in a connected undirected graph.

Return a deep copy (clone) of the graph.

```c++
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> neighbors;
    
    Node() {
        val = 0;
        neighbors = vector<Node*>();
    }
    
    Node(int _val) {
        val = _val;
        neighbors = vector<Node*>();
    }
    
    Node(int _val, vector<Node*> _neighbors) {
        val = _val;
        neighbors = _neighbors;
    }
};
*/
```

## Analysis

One tricky point about cloning is to avoid cloning the same vertex multiple times. Usually in DFS we will check if the ***current vertex*** has been visited before. However, the usual DFS pattern does not check if the ***neighbors*** have been cloned before. Thinking of the case where both A and B are connected to C, where it is possible that C might be cloned twice when visiting A and visiting B respectively.
It is also not enough if we track whether the neighbor has been visited before, because if so, we still need a way to get the pointer to that neighbor.

Thus the solution is to store the mapping from the original vertices to the cloned vertices. Each time we try to clone the neighbor of the current vertex, we first check if the neighbor has already been in our hash map.

Another thing is whether we need to record if a vertex has been visited before if we use DFS? Actually not. We analyze based on two implmentation of the DFS routine.

- First, we can pass one original vertex and one cloned vertex to the routine, where we cloned each of the neighbor (if not cloned before), then apply the routine to every pair of the neighbor vertex with the cloned counterpart. In this implementation, we can choose to only recurse on those newly cloned nodes. In this way, we guarantee that each node will only be called once.

- We may have another implmentation of the DFS routine, where the routine body takes care of cloning the current node only, but not the neighbors of the current node.

## Solution

First attempt:
```c++
class Solution {
public:  
    void DFS(Node* orig, Node* orig_clone) {     
        printf("visit %d\n", orig->val);
        
        for (auto const& next : orig->neighbors ) {
            Node* next_clone = NULL;
            if (m.find(next) == m.end()) {
                next_clone = new Node(next->val);
                m[next] = next_clone;
                DFS(next, next_clone );        
            }

            orig_clone->neighbors.push_back(m[next]);
        }
    }
    
    Node* cloneGraph(Node* node) {
        if (!node) {
            return NULL;
        }
        
        Node* node_clone = new Node(node->val);
        m[node] = node_clone; // initially forgot this line
        
        DFS(node, node_clone);

        return node_clone;
    }
private:
    std::unordered_map<Node*, Node*> m;
};
```

Attempt 2: If in the routing we do the copy of the current node, instead of the neighbors of the current node. In this case we cannot pass the pointer of the clone (if we really want to do that way, we have to pass the reference of the pointer to the copy). 

One tricky factor is the position of the line `cloned[orig] = copy`. My initial attempt is to put it after the for loop, which will result in infinite recursion. The lesson is to always check if the recursion condition has been taken care of prior to invoking the recursion routine. Another point is that here we blend two functions into the same code, thus increasing the possibility of error.

```c++
class Solution {
public:
    
    Node* DFS(Node *orig) {
        if (cloned.find(orig) == cloned.end()) {
            Node *copy = new Node(orig->val);
            cloned[orig] = copy; // note the position of this line
            for (Node *n : orig->neighbors) {
                copy->neighbors.push_back(DFS(n)); // forgot to add "->neighbors"
            }
        }
        return cloned[orig];
    }
    
    Node* cloneGraph(Node* node) {
        if (!node) {
            return NULL;
        }
        
        return DFS(node);
    }
private:
    std::unordered_map<Node*, Node*> cloned;
};
```

## Shoddy Attemp 3

An ugly BFS implementation. There are many problems with this implementation.



```c++
0   class Solution {
1   public:
2       
3       Node* cloneGraph(Node* node) {
4           if (!node) {
5               return NULL;
6           }
7           
8           std::queue<Node*> q;
9           q.push(node);
10           
11           while (!q.empty()) {
12               Node *top = q.front(); q.pop();
13               if (cloned.find(top) == cloned.end()) {
14                   cloned[top] = new Node(top->val);
15               }
16               Node *copy = cloned[top];
17               visited.insert(top);
18               
19               for (Node* n : top->neighbors) {
20                   
21                   if (cloned.find(n) == cloned.end()) {
22                       Node *n_copy = new Node(n->val);
23                       cloned[n] = n_copy;                    
24                   }
25                   copy->neighbors.push_back(cloned[n]);
26                   
27                   if (visited.find(n) == visited.end()) {
28                       q.push(n);
29                       visited.insert(n); // previously forgot this
30                   }
31               }
32           }
33   
34           return cloned[node];
35       }
36   private:
37       std::unordered_map<Node*, Node*> cloned;
38       std::set<Node*> visited;
39   };
```


First, line 29 is previously forgotten. If you only update the `visited` at line 17, it is possible that one node be added to the queue twice. For example, in the following figure both b and c will add d to the queue. Again in this scenario we must avoid duplicated cloning, thus the tracking of visitness is different than usual. **The lesson is that for BFS, visitness checking happens when you add a node to the queue, not when you process a node popped from the queue.**
```
a --- b
|     |
|     |
c --- d
```

The most severe problem about this implementation is that there are two places for cloning a new node. This is not correct as we are doing an iterative clone, thus theoretically only one clone should exist in your loop body. 

Fix the problem as follows. Note that it is very tricky here which nodes to add to the queue. The reference implementation only adds a node to the queue if it is newly cloned. `This boils down to the question of when to set the visited bit of a node in BFS. In my current understanding, this should happens when enqueuing the node rather than dequeueing the node.`

In some scenarios, it is OK to check and update visitedness when dequeueing, but not this case.

```c++
class Solution {
public:
    
    Node* cloneGraph(Node* node) {
        if (!node) {
            return NULL;
        }
        
        std::queue<Node*> q;
        q.push(node);
        
        Node *top_copy = new Node(node->val);
        cloned[node] = top_copy;
        
        while (!q.empty()) {
            Node *top = q.front(); q.pop();
            Node *copy = cloned[top];
            
            for (Node* n : top->neighbors) {
                
                if (cloned.find(n) == cloned.end()) {
                    Node *n_copy = new Node(n->val);
                    cloned[n] = n_copy;     
                    q.push(n);
                }
                copy->neighbors.push_back(cloned[n]);
            }
        }

        return cloned[node];
    }
private:
    std::unordered_map<Node*, Node*> cloned;
    std::set<Node*> visited;
};
```
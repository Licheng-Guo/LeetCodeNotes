# 987. Vertical Order Traversal of a Binary Tree

If two nodes have the same position, then the value of the node that is reported first is the value that is smaller.

## Reference Solution
```c++
class Solution {
public:
    vector<vector<int>> verticalTraversal(TreeNode* root) {
        // clever use of nested maps
        map<int, map<int, set<int> > > nodes;
        
        traverse(root, 0, 0, nodes);
        
        vector<vector<int> > res;
        
        for (auto &x: nodes) {
            vector<int> col;
            for (auto &y: x.second) {
                col.insert(col.end(), y.second.begin(), y.second.end());
            }
            res.push_back(col);
        }
        
        return res;
    }
    
private:
    void traverse(TreeNode *curr, int x, int y, map<int, map<int, set<int> > >& nodes) {
        if (!curr) return;
        
        nodes[x][y].insert(curr->val);
        
        traverse(curr->left, x-1, y+1, nodes); // very tricky here
        traverse(curr->right, x+1, y+1, nodes);
    }
};
```

# 314. Binary Tree Vertical Order Traversal
Given a binary tree, return the vertical order traversal of its nodes' values. (ie, from top to bottom, column by column).

If two nodes are in the same row and column, the order should be from left to right.

## First attempt:

```c++
class Solution {
public:
    vector<vector<int>> verticalOrder(TreeNode* root) {
        constructTable(root, 0, 0);
        
        vector<vector<int> > ans;
        for (auto &kv : table) {
            vector<int> layer;
            for (auto &col : kv.second) {
                for (TreeNode* n : col.second) {
                    layer.push_back(n->val); // got confused between node and value
                }
            }
            ans.push_back(layer);
        }
        
        return ans;
    }
    
private:
    map<int, map<int, vector<TreeNode*> > > table;
    
    void constructTable(TreeNode *curr, int x, int y) {
        if (!curr) return;
        
        table[x][y].push_back(curr);
        
        constructTable(curr->left, x-1, y+1);
        constructTable(curr->right, x+1, y+1);
    }
};
```
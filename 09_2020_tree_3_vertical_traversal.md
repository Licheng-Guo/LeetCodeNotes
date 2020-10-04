# 987. Vertical Order Traversal of a Binary Tree

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
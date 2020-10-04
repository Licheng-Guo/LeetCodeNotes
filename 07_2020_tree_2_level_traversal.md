# 102. Binary Tree Level Order Traversal


## Initial Attempt

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        using data_t = pair<TreeNode*, int>;
        queue<data_t> q;
        vector<vector<int>> res;
        
        if (root) { // don't forgot this
            q.push(data_t(root, 0));
        }
        
        while (!q.empty()) {
            data_t curr = q.front(); q.pop(); // queue uses front()
            if (res.size() <= curr.second) {
                res.push_back(vector<int>());
            }    
            res[curr.second].push_back(curr.first->val);
            
            if (curr.first->left) {
                q.push(data_t(curr.first->left, curr.second+1)); // queue uses push()
            }
            if (curr.first->right) {
                q.push(data_t(curr.first->right, curr.second+1));
            }
        }
        
        return res;
    }
};

```

Actually there is no need for the marker.

```c++
    vector<vector<int>> levelOrder(TreeNode* root) {
        queue<TreeNode*> q;
        vector<vector<int>> res;
        
        if (root) {
            q.push(root);
        }
        
        while (!q.empty()) {
            int n = q.size();
            vector<int> curr_level;
            curr_level.reserve(n);
            
            for (int i = 0; i < n; i++) {
                TreeNode* node = q.front(); q.pop();
                curr_level.push_back(node->val);
                
                if (node->left) q.push(node->left);
                if (node->right) q.push(node->right);
            }
            
            res.push_back(curr_level);
        }
        
        return res;
        
    }
```

We can also insert markers into the queue to denote the boundaries of layers.
```c++
    vector<vector<int>> levelOrder(TreeNode* root) {
        if (root == nullptr) return {};
        queue<TreeNode*> q;
        q.push(root);
        q.push(nullptr);
        int level = 0;
        vector<vector<int>> res;
        vector<int> tmp;
        while (!q.empty()) {
            auto t = q.front();
            q.pop();
            if (t) {
                tmp.push_back(t->val);
                if (t->left) q.push(t->left);
                if (t->right) q.push(t->right);
            } else {
                res.push_back(tmp);
                tmp.clear();
                if (!q.empty()) q.push(nullptr);
            }
        }
        return res;
    }
```

Pythonic way. This is the same idea as the second solution. At the beginning of one layer, we know that all items in the queue belong to the current layer, thus we can use a for loop to execute the current layer, no more no less.

```python
def levelOrder(self, root):
    ans, level = [], [root]
    while root and level:
        ans.append([node.val for node in level])            
        level = [kid for n in level for kid in (n.left, n.right) if kid]
    return ans
```

# 107. Binary Tree Level Order Traversal II

Given a binary tree, return the bottom-up level order traversal of its nodes' values. (ie, from left to right, level by level from leaf to root).

- insert at the front is costly
- it's ok to return a locally defined vector, as long as the return type is not referenced. The contents will be moved
- if we are using the default constructor, don't put `()` after the variable

```c++
class Solution {
public:
    vector<vector<int>> levelOrderBottom(TreeNode* root) {
        // dont use vector<vector<int>> result();
        vector<vector<int>> result;
        
        if (!root) {
            return result;
        }
        
        queue<TreeNode*> q;
        q.push(root);
        
        while (!q.empty()) {
            int n = q.size();
            vector<int> layer;
            for (int i = 0; i < n; i++) {
                TreeNode* curr = q.front(); q.pop();
                layer.push_back(curr->val);
                if (curr->left) q.push(curr->left);
                if (curr->right) q.push(curr->right);
            }
            
            result.push_back(move(layer)); // insert at front is costly
        }
        
        reverse(result.begin(), result.end());
        return result;
        
    }
};
```
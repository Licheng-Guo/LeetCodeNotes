# 94. Binary Tree Inorder Traversal

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    vector<int> inorderTraversal(TreeNode* root) {
        vector<int> result;
        
        visitStack(root, result);
        
        return result;
    }
    
    void visitRecursive(TreeNode *curr, vector<int>& results) {
        if (!curr) {
            return;
        }
        
        visitRecursive(curr->left, results);
        
        results.push_back(curr->val); // be careful whether to push in the node or the val
        
        visitRecursive(curr->right, results);
    }
    
/*

while (curr) {

  *** something here to prevent deadloop ***

  if we can go left:
    go left
  else:
    go right
}

*/    
    void visitStack(TreeNode *root, vector<int>& result) {
        TreeNode *curr = root;
        stack<TreeNode*> s;
        
        // use both curr and s.empty in the predicate
        // curr is related to the process of going down
        // s.empty is related to the process of back up
        while (curr || !s.empty()) {
            if (!curr) {
                // we need to make sure when we return from a left branch, we do not go left again
                curr = s.top(); s.pop();
                result.push_back(curr->val);
                curr = curr->right;
            }
            else {
                if (curr->left) {
                    s.push(curr);
                    curr = curr->left;
                }
                else {
                    result.push_back(curr->val);
                    curr = curr->right; // be careful about the potential dead loop
                }                
            }
        }
    }
    
    //*** if use morris, make sure that the whole process finish.
    // for problems that you have the answer in the middle, still finish the process
    // otherwise the system may crash because you have spoil the tree
    void visitMorris(TreeNode *root, vector<int>& result) {
        TreeNode *curr = root;
        
        while (curr) {
            if (curr->left) {
                TreeNode *pre = curr->left;
                while (pre->right && pre->right != curr) {
                    pre = pre->right;
                }
                
                // by checking whether the returning link has already been setup, we know if we are visiting it for the first time or the second time
                if (!pre->right) { // be careful about the not here
                    pre->right = curr;
                    curr = curr->left;                    
                }
                else {
                    pre->right = NULL;
                    result.push_back(curr->val);
                    curr = curr->right;
                }

            }
            else {
                result.push_back(curr->val);
                // this will return to the upper level parent
                curr = curr->right;
            }
        }
    }
};
```

## Second Attempt

The stack based approach can be optimized.
```c++
    void visitStack(TreeNode *root, vector<int>& result) {
        TreeNode *curr = root;
        stack<TreeNode*> s;
        
        // use both curr and s.empty in the predicate
        // curr is related to the process of going down
        // s.empty is related to the process of back up
        while (curr || !s.empty()) {
            if (!curr) {
                // we need to make sure when we return from a left branch, we do not go left again
                curr = s.top(); s.pop();
                result.push_back(curr->val);
                curr = curr->right;
            }
            else {
                s.push(curr);
                curr = curr->left;          
            }
        }
    }
```

# Preorder Traversal

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
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        stack<TreeNode*> s;
        TreeNode *curr = root;

        while (curr || !s.empty()) {
            if (curr) {
                res.push_back(curr->val);
                if (curr->left) {
                    s.push(curr); // must push before move to left
                    curr = curr->left;
                } 
                else {
                    curr = curr->right;
                } 
            }
            else {
                curr = s.top(); s.pop();
                curr = curr->right;
            }
        }
        
        return res;
    }
    
    vector<int> preorderTraversal(TreeNode* root) {
        vector<int> res;
        TreeNode *curr = root;

        while (curr) {
            res.push_back(curr->val);

            if (curr->left) {
                TreeNode *pre = curr->left;
                while (pre->right && pre->right != curr) {
                    pre = pre->right;
                }
                
                if (!pre->right) {
                    pre->right = curr;
                    curr = curr->left;
                }
                else {
                    pre->right = nullptr;
                    curr = curr->right;
                    res.pop_back(); // how to make this more concise?
                }
            }
            else {
                curr = curr->right;
            }
        }
        
        return res;
    }    
};
```
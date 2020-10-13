# 270. Closest Binary Search Tree Value

Given a non-empty binary search tree and a target value, find the value in the BST that is closest to the target.

Note:

- Given target value is a floating point.
- You are guaranteed to have only one unique value in the BST that is closest to the target.

## First Attempt

Note that here we are not doing tree traversal but BST look-up.

```c++
class Solution {
public:
    int closestValue(TreeNode* root, double target) {
        // need to remember the previous visited node
        // corner case: bigger than the largest node or smaller than the smallest node
        
        TreeNode *prev = root;
        double prev_dist = abs(root->val - target);
        
        TreeNode *curr = root;
        
        while (curr) {
            double dist = abs(curr->val - target);
            if (dist < prev_dist) {
                prev_dist = dist;
                prev = curr;
            }
            
            if (curr->val < target) {
                curr = curr->right; // confused about direction again
            }
            else {
                curr = curr->left;
            }
        }
        
        return prev->val;
        
    }
};
```
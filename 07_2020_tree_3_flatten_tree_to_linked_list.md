# 114. Flatten Binary Tree to Linked List

Given a binary tree, flatten it to a linked list in-place.

For example, given the following tree:

```
    1
   / \
  2   5
 / \   \
3   4   6
```

The flattened tree should look like:

```
1
 \
  2
   \
    3
     \
      4
       \
        5
         \
          6
```

The tricky thing about this problem is that the order of flattening does not matter. You can first merge the left subtree into the right subtree, then step by step resolve the sub-subtrees.

```c++
void flatten(TreeNode* root) {        
    TreeNode *curr = root;
    
    while (curr) {
        if (curr->left) {
            TreeNode *pre = curr->left;

            while (pre->right) {
                pre = pre->right;
            }

            pre->right = curr->right;
            curr->right = curr->left;
            curr->left = nullptr;               
        }
        curr = curr->right;
    }
    
}
```

Another solution is to construct the result from bottom up. If we traverse in the order of (visit, left, right), we cannot do the transform on the fly as that will corrupt the structure of the parts not visited yet. However, we could do this in the reverse way. If we traverse in the order of (right, left, visit), we can make sure that the part we have destroyed will not be visited again.

```c++
TreeNode* prev = nullptr;

void flatten(TreeNode* root) {        
    if (!root) {
        return;
    }
    
    flatten(root->right);
    flatten(root->left);
    
    root->right = prev;
    root->left = nullptr;
    prev = root;
    
}
```
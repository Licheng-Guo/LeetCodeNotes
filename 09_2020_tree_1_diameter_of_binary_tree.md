# 543. Diameter of Binary Tree

Given a binary tree, you need to compute the length of the diameter of the tree. The diameter of a binary tree is the length of the longest path between any two nodes in a tree. This path may or may not pass through the root.

## First attempt:
```c++
class Solution {
public:
    int diameterOfBinaryTree(TreeNode* root) {
        if (!root) return 0;
        
        unordered_map<TreeNode*, int> max_len_from_root;
        unordered_map<TreeNode*, int> max_len;
        
        max_len_from_root[nullptr] = 0;
        max_len[nullptr] = 0;
        
        visit(root, max_len_from_root, max_len);
        
        return max(max_len_from_root[root], max_len[root])-1;
    }
    
private:
    // forgot the reference again
    void visit( TreeNode* node, 
                unordered_map<TreeNode*, int>& max_len_from_root,
                unordered_map<TreeNode*, int>& max_len) {
        if (!node) {
            return;
        }
        
        visit(node->left, max_len_from_root, max_len);
        visit(node->right, max_len_from_root, max_len);
        
        max_len_from_root[node] = 
            max(max_len_from_root[node->left],
                max_len_from_root[node->right]) + 1;
        
        max_len[node] = max(
                max(max_len_from_root[node->left] + max_len_from_root[node->right] + 1,
                    max_len[node->left]),
                max_len[node->right]);
    }
};
```

## Optimization

So although the longest path doesn't have to go through the root node, it has to pass the root node of some subtree of the tree (because it has to be from one leaf node to another leaf node, otherwise we can extend it for free). The longest path that passes a given node as the ROOT node is T = left_height+right_height. So you just calculate T for all nodes and output the max T.

Reference implementation:
```c++
int diameterOfBinaryTree(TreeNode* root) {
    int d=0;
    rec(root, d);
    return d;
}

int rec(TreeNode* root, int &d) {
    if(root == NULL) return 0;
    int ld = rec(root->left, d);
    int rd = rec(root->right, d);
    d=max(d,ld+rd);
    return max(ld,rd)+1;
}
```

# 1026. Maximum Difference Between Node and Ancestor

Given the root of a binary tree, find the maximum value V for which there exists different nodes A and B where `V = |A.val - B.val|` and A is an ancestor of B.

(A node A is an ancestor of B if either: any child of A is equal to B, or any child of A is an ancestor of B.)

Note that it is incorrect to find the global maximum node and the global minimum node, because they may not be in the same path.

## First Attempt

```c++
class Solution {
public:
    int maxAncestorDiff(TreeNode* root) {
        // when visiting a node, record val - min(left) and max(right) - val
        // track the largest difference
        int min_val = root->val;
        int max_val = root->val;
        int dist = -1;
        
        traverse(root, min_val, max_val, dist);
        
        return dist;
    }
    
private:
    
    void traverse(TreeNode* curr, int& min_val, int& max_val, int& max_dist) {
        if (!curr) {
            return; //double check on the return values
        }
        
        int local_min_left = curr->val;
        int local_max_left = curr->val;
        int local_min_right = curr->val;
        int local_max_right = curr->val;
        int local_dist_left = -1;
        int local_dist_right = -1;
        
        traverse(curr->left, local_min_left, local_max_left, local_dist_left);
        local_dist_left = max(local_dist_left, abs(curr->val - local_min_left));
        local_dist_left = max(local_dist_left, abs(curr->val - local_max_left));

        
        traverse(curr->right, local_min_right, local_max_right, local_dist_right);
        local_dist_right = max(local_dist_right, abs(curr->val - local_min_right));    
        local_dist_right = max(local_dist_right, abs(curr->val - local_max_right));    
        
        max_dist = max(max_dist, max(local_dist_left, local_dist_right)); // got this wrong
        min_val = min(min(local_min_left, local_min_right), curr->val);
        max_val = max(max(local_max_left, local_max_right), curr->val);
        
        // printf("visit %d, min_val = %d, max_val = %d, dist = %d\n", curr->val, min_val, max_val, max_dist);
        
        return;
    }
};
```

## Reference Implementation

I think this is the best implementation. We just pass the current min/max to subproblems, and simply update the min/max when visiting each node and recomupte the maximum distance. Note that this relies on pre-order traversal. In contrary, to find the diameter of a tree, we use post-order traversal as we need to calculate the maximum length in the bottom-up way.

Compared to the diameter problem, another difference is that the solution of the sub-problems rely on the current min/max value. On the other hand, the diameter of sub-trees is independent of the outside structures or node value.

I think this implemention works on any DAG.

Note that it is very clever to pass by value instead of pass by reference. If we choose to pass by reference, we must restore to the previous mi/mx when backtracking. However, this is automatically achieved if we pass by value.

```c++
class Solution {
public:
    
    int diff = INT_MIN;
    
    void getMax(TreeNode *root, int mx, int mi){
        
        if(root == NULL) return;
       
        mx = max(mx, root->val);
        mi = min(mi, root->val);
        
        diff = max(diff, mx - mi);
        
        getMax(root->left,mx,mi);
        getMax(root->right,mx,mi);
    }
    
    int maxAncestorDiff(TreeNode* root) {
        
        if(!root) return 0;
        
        int mx = INT_MIN, mi = INT_MAX;

        getMax(root,mx,mi);
        
        return diff;
    }
};
```


In this implementation, we do not track how the min/max change when stepping into the sub problems

```c++
public int maxAncestorDiff(TreeNode root) {
    return dfs(root, root.val, root.val); // initialize both max and min with root.val.
}
private int dfs(TreeNode n, int max, int min) {
    if (n == null) { return 0; } // base case.
    max = Math.max(n.val, max); // update max.
    min = Math.min(n.val, min); // update min.
    int l = dfs(n.left, max, min); // recurse down.
    int r = dfs(n.right, max, min); // recurse down.
    return Math.max(max - min, Math.max(l, r)); // compare all super/sub differences to get result.
}
```
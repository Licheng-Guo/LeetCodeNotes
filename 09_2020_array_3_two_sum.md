# 1. Two Sum

Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

## Reference Solution
Note that unordered_map is based on hash table, so looking up takes O(1) time.

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> table;
        for (int i = 0; i < nums.size(); i++) {
            if (table.count(target-nums[i]) != 0) {
                vector<int> res = {table[target-nums[i]], i};
                return res;
            }
            else {
                table[nums[i]] = i;
            }
        }
        assert(0);
        return {0, 0};
    }
};
```

# 167. Two Sum II - Input array is sorted

Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2.

## First Attempt

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int n = numbers.size();
        
        for (int i = 0; i < n-1; i++) {
            int res = findIndex(numbers, i+1, n-1, target-numbers[i]);
            if (res != -1) {
                return {i+1, res+1};
            }
        }
        
        assert(0);
        return {0, 0};
    }
    
    int findIndex(vector<int>& nums, int lo_bound, int hi_bound, int target) {
        assert(lo_bound <= hi_bound);
        
        int lo = lo_bound, hi = hi_bound;
        
        while (lo <= hi) {
            int mid = (lo + hi) / 2;
            if (nums[mid] == target) {
                return mid;
            }
            else if (nums[mid] < target) {
                lo = mid + 1;
            }
            else {
                hi = mid - 1;
            }
        }
        
        return -1;
    }
};
```

## Reference Solution

As long as we only move one pointer at a time we will not miss the answer.

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int n = numbers.size();
        
        int lo = 0; 
        int hi = n-1;
        
        while (lo < hi) {
            int sum = numbers[lo] + numbers[hi];
            if (sum == target) {
                return {lo + 1, hi + 1};
            }
            else if (sum < target) {
                lo++;
            }
            else {
                hi--;
            }
        }
        
        assert(0);
        return {0, 0};
    }
    
};
```

# 653. Two Sum IV - Input is a BST

Given the root of a Binary Search Tree and a target number k, return true if there exist two elements in the BST such that their sum is equal to the given target.

## First Attempt

```c++
class Solution {
public:
    bool findTarget(TreeNode* root, int k) {
        vector<int> nums;
        inorder(root, nums);
        
        int lo = 0; int hi = nums.size()-1;
        while (lo < hi) {
            int sum = nums[lo] + nums[hi];
            if (sum == k) {
                return true;
            }
            else if (sum < k) {
                lo++;
            }
            else {
                hi--;
            }
        }
        
        return false;
    }
    
    void inorder(TreeNode* node, vector<int>& nums) {
        if (!node) return;
        
        inorder(node->left, nums);
        nums.push_back(node->val);
        inorder(node->right, nums);
    }
};
```

## Reference Solution

Perform two tree traversal at the same time. Made a mistake about when to pop from the stacks. Note that when moving to the next node, we have already visited the top() of the stack, thus we directly go to `top()->right` if iterating from the left end.

```c++
class Solution {
public:
    bool findTarget(TreeNode* root, int k) {
        stack<TreeNode*> left_side;
        stack<TreeNode*> right_side;
        
        TreeNode *l = root;
        TreeNode *r = root;
        while (l) {
            left_side.push(l);
            l = l->left;
        }
        
        while (r) {
            right_side.push(r);
            r = r->right;
        }
        
        // while (!left_side.empty() && !right_side.empty()) {
        while (1) {
            if (left_side.top() == right_side.top()) {
                break;
            }
            
            int sum = left_side.top()->val + right_side.top()->val;
            
            if (sum == k) {
                return true;
            }
            else if (sum < k) {
                // left_side.pop();
                TreeNode *curr = left_side.top()->right; left_side.pop();
                while (curr) {
                    left_side.push(curr);
                    curr = curr->left;
                }
            }
            else {
                // right_side.pop();
                TreeNode *curr = right_side.top()->left; right_side.pop();
                while (curr) {
                    right_side.push(curr);
                    curr = curr->right;
                }
            }
        }
        
        return false;
    }
};
```

Implement a BST iterator. *** This is potentially wrong ***
Refer to 09_2020_tree_2.

```c++
class BSTIterator {
private:
    stack<TreeNode*> s;
    TreeNode *curr;
    bool direction;

public:
    static const bool INCREMENTAL = true;
    static const bool DECREMENTAL = false;
    
    BSTIterator(TreeNode *root, bool direction) : curr(root), direction(direction) {
        while (curr) {
            s.push(curr);

            // note that we want curr to point to the current node instead of null
            TreeNode *next = direction ? curr->left : curr->right;
            if (next) {
                curr = next;
            }
            else {
                break;
            }
        }
    }
    
    ~BSTIterator() {
        while(!s.empty()) {
            s.pop();
        }
    }
    
    int getVal() {
        return curr->val;
    }
    
    void next() {
        curr = direction ? curr->right : curr->left; // *** error prone *** 
        if (curr) {
            while (curr) {
                s.push(curr);
                TreeNode *next = direction ? curr->left : curr->right;
                if (next) {
                    curr = next;
                }
                else {
                    break;
                }
            }
        }
        else {
            curr = s.top();
            s.pop();
        }
    }
};

class Solution {
public:
    bool findTarget(TreeNode* root, int k) {
        BSTIterator iter_inc(root, BSTIterator::INCREMENTAL);
        BSTIterator iter_dec(root, BSTIterator::DECREMENTAL);
        
        while (1) {
            int l = iter_inc.getVal();
            int r = iter_dec.getVal();
            if (l >= r) break;
            
            if (l + r == k) {
                return true;
            }
            else if (l + r < k) {
                iter_inc.next();
            }
            else {
                iter_dec.next();
            }
        }
        
        return false;
    }
};
```
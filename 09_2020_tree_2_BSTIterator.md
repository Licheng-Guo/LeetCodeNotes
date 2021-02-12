
# 173. Binary Search Tree Iterator

## First Attempt

Very broken implentation. Pitfalls everywhere.
```c++
class BSTIterator {
public:
    BSTIterator(TreeNode* root) {
        curr = root;
        while (curr) {
            if (curr->left) {
                s.push(curr);
                curr = curr->left;
            }
            else {
                break; // forgot this
            }
        }
    }
    
    ~BSTIterator() {
        while (!s.empty()) {
            s.pop();
        }
    }
    
    /** @return the next smallest number */
    int next() {
        assert(curr);
        int val = curr->val;
        
        curr = curr->right;
        
        if (curr) {
            while (curr) {
                if (curr->left) {
                    s.push(curr);
                    curr = curr->left;
                }
                else {
                    break;
                }
            }
        }
        else {
            if (s.empty()) { // forgot this
                curr = nullptr;
            }
            else {
                curr = s.top();
                s.pop();                
            }
        }
        
        return val;
    }
    
    /** @return whether we have a next smallest number */
    bool hasNext() {
        // quite tricky
        return curr || !s.empty();
    }
private:
    stack<TreeNode*> s;
    TreeNode *curr;
};
```

Reference implementation

```c++
class BSTIterator {
    stack<TreeNode *> myStack;
public:
    BSTIterator(TreeNode *root) {
        pushAll(root);
    }

    /** @return whether we have a next smallest number */
    bool hasNext() {
        return !myStack.empty();
    }

    /** @return the next smallest number */
    int next() {
        TreeNode *tmpNode = myStack.top();
        myStack.pop();
        pushAll(tmpNode->right);
        return tmpNode->val;
    }

private:
    void pushAll(TreeNode *node) {
        for (; node != NULL; myStack.push(node), node = node->left);
    }
};
```

## Second Attempt

```c++

class BSTIterator {
public:
    BSTIterator(TreeNode* root) {
        curr = root;
        while (curr) {
            s.push(curr);
            curr = curr->left;
        }
    }
    
    ~BSTIterator() {
        while (!s.empty()) {
            s.pop();
        }
    }
    
    /** @return the next smallest number */
    int next() {
        TreeNode *curr = s.top(); s.pop();
        int val = curr->val;
        
        curr = curr->right;
        while (curr) {
            s.push(curr);
            curr = curr->left;
        }
        
        return val;
    }
    
    /** @return whether we have a next smallest number */
    bool hasNext() {
        return !s.empty();
    }
private:
    stack<TreeNode*> s;

};
```
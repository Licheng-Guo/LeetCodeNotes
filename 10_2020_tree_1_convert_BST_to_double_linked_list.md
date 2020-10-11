# 426. Convert Binary Search Tree to Sorted Doubly Linked List

Note that the tail and the head should be connected to each other.

## First Attempt

```c++

class Solution {
public:
    // left -------- right
    Node* treeToDoublyList(Node* root) {
        if (!root) return nullptr;
        
        stack<Node*> s;
        pushAll(s, root);
        Node *ans = s.top();
        
        Node *prev = nullptr;
        while (!s.empty()) {
            Node *curr = s.top(); s.pop();         
            pushAll(s, curr->right);
            
            // note that the modification should be after the pushAll
            // No! Observe that the construction of the linked list only affects the left part of the current node, while for the traversal we will only touch the right part from now on. Thus it is safe to move the following part before the pushAll()
            curr->left = prev;
            if (prev) prev->right = curr;
            prev = curr;            
        }
        prev->right = ans;
        ans->left = prev;

        
        return ans;
    }
    
private:
    void pushAll(stack<Node*>& s, Node* curr) {
        while (curr) {
            s.push(curr);
            curr = curr->left;
        }
    }
};
```
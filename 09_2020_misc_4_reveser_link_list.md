# 206. Reverse Linked List

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head) return nullptr;
        
        stack<ListNode*> s;
        
        while (head) {
            s.push(head);
            head = head->next;
        }
        
        ListNode *new_head = nullptr;
        ListNode *curr = nullptr;
        if (!s.empty()) {
            new_head = s.top(); s.pop();
            curr = new_head;
        }
        while (!s.empty()) {
            curr->next = s.top(); s.pop();
            curr = curr->next; // forgot this
        }
        curr->next = nullptr;
        
        return new_head;
    }
};
```

Use a dummy header to make the code simpler. Note that for the last node (the original first node), we need to explicitly nullify its `next` field, which still points to the second node in the original linked list.

```c++
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        if (!head) return nullptr;
        
        stack<ListNode*> s;
        
        while (head) {
            s.push(head);
            head = head->next;
        }
        
        ListNode *new_head = new ListNode();
        ListNode *curr = new_head;
        
        while (!s.empty()) {
            curr->next = s.top(); s.pop();
            curr = curr->next; // forgot this
        }
        curr->next = nullptr; // this cannot be forgotten!
        
        return new_head->next;
    }
};
```

Attempt 2

```c++
ListNode* reverseList(ListNode* head) {        
    if (head == nullptr) return nullptr;
    
    std::stack<ListNode*> s;
    ListNode *curr = head;
    while (curr) {
        s.push(curr);
        curr = curr->next;
    }
    
    ListNode dummy;
    curr = &dummy;
    while (!s.empty()) {
        curr->next = s.top(); s.pop();
        curr = curr->next;
    }
    curr->next = nullptr; // forgot this
    
    return dummy.next;
}
```

## Reference Solution 1

```c++
ListNode* reverseList(ListNode* head) {
    ListNode* prev = nullptr;
    ListNode* curr = head;
    
    while (curr) {
        ListNode *next = curr->next; // forgot this line
        curr->next = prev;
        prev = curr;
        curr = next;
    }
    
    return prev;
}
```

## Reference Solution 2

```c++
ListNode* reverseList(ListNode* head) {        
    if (head == nullptr) return nullptr;
    else if (head->next == nullptr) return head;
    else {
        ListNode *new_head_of_sub = reverseList(head->next);
        ListNode *new_tail_of_sub = head->next;
        
        head->next->next = head; 
        head->next = nullptr; // forgot this one
        return new_head_of_sub;
    }
}
```
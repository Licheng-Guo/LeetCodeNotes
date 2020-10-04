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
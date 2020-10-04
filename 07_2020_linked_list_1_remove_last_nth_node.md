# 19. Remove Nth Node From End of List

Given a linked list, remove the n-th node from the end of list and return its head.

```
Example:

Given linked list: 1->2->3->4->5, and n = 2.

After removing the second node from the end, the linked list becomes 1->2->3->5.
```

The trick: use two pointers n-node apart. The corner case is that if the array only has n nodes.
```c++
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *first = head;
        ListNode *second = head;
        
        for (int i = 0; i < n; i++) {
            second = second->next;
        }
        
        // corner case
        if (!second) {
            return head->next;
        }
        
        while (second->next) {
            first = first->next;
            second = second->next;
        }
        
        ListNode *target = first->next;
        first->next = target->next;
        delete target;
        
        
        return head;
    }
};
```
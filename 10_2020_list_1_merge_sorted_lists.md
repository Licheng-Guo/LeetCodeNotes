# 21. Merge Two Sorted Lists

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *ans = new ListNode(-1);
        ListNode *curr = ans;
        
        while (l1 && l2) {
            if (l1->val < l2->val) {
                curr->next = new ListNode(l1->val);
                l1 = l1->next;
            }    
            else {
                curr->next = new ListNode(l2->val);
                l2 = l2->next;
            }
            curr = curr->next;
        }
   
        // this is better
        if (l1) {
            curr->next = l1;
        }
        else {
            curr->next = l2;
        }
//         while (l1) {
//             curr->next = new ListNode(l1->val);
//             l1 = l1->next;
//             curr = curr->next;
//         }
        
//         while (l2) {
//             curr->next = new ListNode(l2->val);
//             l2 = l2->next;
//             curr = curr->next;
//         }
        
        return ans->next;
    }
};
```

## Another Attempt

```c++
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *dummy = new ListNode(-1);
        ListNode *curr = dummy;
        
        while (l1 && l2) { // confused between && and &
            if (l1->val < l2->val) {
                curr->next = new ListNode(l1->val);
                l1 = l1->next;
                curr = curr->next;
            }
            else {
                curr->next = new ListNode(l2->val);
                l2 = l2->next;
                curr = curr->next;
            }
        }
        
        if (l1 == nullptr) {
            curr->next = l2;
        }
        else if (l2 == nullptr) {
            curr->next = l1;
        }
        else {
            assert(false);
        }
    
        return dummy->next;
    }  
};
```

# 23. Merge k Sorted Lists

## First Attempt

Should be careful whether we are goint for the `middle point` or the `length between pointers`.

```c++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return mergeKLists(lists, 0, lists.size()-1);
    }
    
private:
    ListNode* mergeKLists(vector<ListNode*>& lists, int start, int end) {   
        
        if (end - start < 0) return nullptr;
        else if (end - start == 0) return lists[start];
        else if (end - start == 1) return mergeTwoLists(lists[start], lists[end]);
        else {
            int mid = (end + start) / 2; // confused with -
            ListNode *l1 = mergeKLists(lists, start, mid);
            ListNode *l2 = mergeKLists(lists, mid+1, end);
            return mergeTwoLists(l1, l2);
        }
    }
        
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode *ans = new ListNode(-1);
        ListNode *curr = ans;
        
        while (l1 && l2) {
            if (l1->val < l2->val) {
                curr->next = new ListNode(l1->val);
                l1 = l1->next;
            }    
            else {
                curr->next = new ListNode(l2->val);
                l2 = l2->next;
            }
            curr = curr->next;
        }
   
        if (l1) {
            curr->next = l1;
        }
        else {
            curr->next = l2;
        }
        
        return ans->next;
    }
};
```

## Reference Solution

Store the lists in a heap sorted by the first element. Each time we pick the list with the lowest head, pop the head node and re-insert the list back into the heap.

```c++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        // note that we need > to construct a min-heap
        auto compare = [](ListNode* a, ListNode *b) {
           return a->val > b->val;
        };
        priority_queue<ListNode*, vector<ListNode*>, decltype(compare)> pq(compare);

        for (auto l : lists) {
            if (l) {
                q.push(l);
            }
        }
        
        ListNode pre(0);
        ListNode *node = &pre;
        while (q.size()) {
            ListNode *top = q.top();
            q.pop();
            node->next = top;
            node = node->next;
            if (top->next) {
                q.push(top->next);
            }
        }
        return pre.next;
    }
};
```

My attempt. It is very easy to get confused between containers.

```c++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        auto compare = [](ListNode* a, ListNode *b) {
            assert(a);
            assert(b);
            return a->val > b->val;
        };
        priority_queue<ListNode*, vector<ListNode*>, decltype(compare)> pq(compare);
        
        for (auto l : lists) {
            if (l) {
                pq.push(l);
            }
        }
        
        ListNode *ans = new ListNode(0);
        ListNode *curr = ans;
        while (!pq.empty()) { // bug here
            ListNode *top_list = pq.top(); pq.pop();
            
            curr->next = top_list;
            top_list = top_list->next;
            curr = curr->next;
            
            if (top_list) {
                pq.push(top_list);
            }
        }
        
        return ans->next;
    }
};
```

Try again the divide-and-conquer method.
```c++
class Solution {
public:
    ListNode* mergeKLists(vector<ListNode*>& lists) {
        return _mergeKLists(lists, 0, lists.size()-1);
    }

private:
    ListNode* _mergeKLists(vector<ListNode*>& lists, int start, int end) {
        if (start > end) {
            return nullptr;
        }
        else if (start == end) return lists[start];
        else if (start + 1 == end) return(mergeTwoLists(lists[start], lists[end]));
        else {
            int mid = (start + end) / 2;
            ListNode* sub1 = _mergeKLists(lists, start, mid);
            ListNode* sub2 = _mergeKLists(lists, mid+1, end);
            return mergeTwoLists(sub1, sub2);
        }
    }
    
    ListNode* mergeTwoLists(ListNode* l1, ListNode* l2) {
        ListNode dummy(-1); // if we "new" a dummy, we need to free the memory before leaving
        ListNode *curr = &dummy;
        
        while (l1 && l2) { // confused between && and &
            if (l1->val < l2->val) {
                curr->next = new ListNode(l1->val);
                l1 = l1->next;
                curr = curr->next;
            }
            else {
                curr->next = new ListNode(l2->val);
                l2 = l2->next;
                curr = curr->next;
            }
        }
        
        if (l1 == nullptr) {
            curr->next = l2;
        }
        else if (l2 == nullptr) {
            curr->next = l1;
        }
        else {
            assert(false);
        }
    
        return dummy->next;
    }
};
```
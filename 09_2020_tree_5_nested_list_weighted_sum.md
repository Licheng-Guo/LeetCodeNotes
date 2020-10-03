# 339. Nested List Weight Sum

Given a nested list of integers, return the sum of all integers in the list weighted by their depth.

Each element is either an integer, or a list -- whose elements may also be integers or other lists.

Example 1:

```
Input: [[1,1],2,[1,1]]
Output: 10 
Explanation: Four 1's at depth 2, one 2 at depth 1.
```

Example 2:

```
Input: [1,[4,[6]]]
Output: 27 
Explanation: One 1 at depth 1, one 4 at depth 2, and one 6 at depth 3; 1 + 4*2 + 6*3 = 27.
```

## First Attempt

```c++
/**
 * // This is the interface that allows for creating nested lists.
 * // You should not implement it, or speculate about its implementation
 * class NestedInteger {
 *   public:
 *     // Constructor initializes an empty nested list.
 *     NestedInteger();
 *
 *     // Constructor initializes a single integer.
 *     NestedInteger(int value);
 *
 *     // Return true if this NestedInteger holds a single integer, rather than a nested list.
 *     bool isInteger() const;
 *
 *     // Return the single integer that this NestedInteger holds, if it holds a single integer
 *     // The result is undefined if this NestedInteger holds a nested list
 *     int getInteger() const;
 *
 *     // Set this NestedInteger to hold a single integer.
 *     void setInteger(int value);
 *
 *     // Set this NestedInteger to hold a nested list and adds a nested integer to it.
 *     void add(const NestedInteger &ni);
 *
 *     // Return the nested list that this NestedInteger holds, if it holds a nested list
 *     // The result is undefined if this NestedInteger holds a single integer
 *     const vector<NestedInteger> &getList() const;
 * };
 */
class Solution {
public:
    int depthSum(vector<NestedInteger>& nestedList) {
        // use BFS
        // in each layer, if the element is a number, then add to the total sum
        // if the element is a vector, then add to the queue
        if (nestedList.empty()) return 0;
        
        queue<NestedInteger> q;
        NestedInteger root;
        for (auto& n : nestedList) root.add(n);
        
        q.push(root);
        
        int sum = 0;
        int layer = 0; // made a mistake here. Note that we add an artificial layer
        while (!q.empty()) {
            int size = q.size();
            
            for (int i = 0; i < size; i++) {
                NestedInteger curr = q.front(); q.pop();
                if (curr.isInteger()) {
                    sum += layer * curr.getInteger();
                }
                else {
                    // be careful here. Almost made a mistake
                    for (auto &n : curr.getList()) {
                        q.push(n);
                    }
                }
            }
            
            layer++;
        }
        
        return sum;
    }
};
```
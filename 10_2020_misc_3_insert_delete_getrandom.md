# 380. Insert Delete GetRandom O(1)

Implement the `RandomizedSet` class:

`bool insert(int val)` Inserts an item val into the set if not present. Returns true if the item was not present, false otherwise.

`bool remove(int val)` Removes an item val from the set if present. Returns true if the item was present, false otherwise.

`int getRandom()` Returns a random element from the current set of elements (it's guaranteed that at least one element exists when this method is called). Each element must have the same probability of being returned.

Follow up: Could you implement the functions of the class with each function works in average O(1) time?

## First Attempt

Similar to the LRU cache problem, here we have two data structures tightly coupled together. Thus when operating on one data structure, we must be reminded that the other one should be modified accordingly.

```c++
class RandomizedSet {
public:
    /** Initialize your data structure here. */
    RandomizedSet() {
        
    }
    
    /** Inserts a value to the set. Returns true if the set did not already contain the specified element. */
    bool insert(int val) {
        if (num_to_id.count(val)) {
            return false;
        }
        else {
            num_to_id[val] = num_to_id.size();
            id_to_num[id_to_num.size()] = val;
            
            return true;
        }
    }
    
    /** Removes a value from the set. Returns true if the set contained the specified element. */
    bool remove(int val) {
        if (num_to_id.count(val)) {
            int id = num_to_id[val];
            int victim = id_to_num[id_to_num.size()-1];
            id_to_num[id] = victim; // forgot this one!
            num_to_id[victim] = id;
            
            id_to_num.erase(id_to_num.size()-1);
            num_to_id.erase(val);
            
            return true;
        }
        else {
            return false;
        }
    }
    
    /** Get a random element from the set. */
    int getRandom() {
        int rd = rand() % id_to_num.size();
        return id_to_num[rd];
    }
    
private:
    unordered_map<int, int> id_to_num;
    unordered_map<int, int> num_to_id;
};
```
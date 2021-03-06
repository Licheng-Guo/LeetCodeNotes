# 146. LRU Cache

Core idea: First, we need a map to store the key-value mappings. Second, we need somethings to track which keys are not touched for a long time, thus when we need to evict a victim, we know whom to evict. 

Thus we need a queue or list storing keys, and the back of the container is the oldest item. When we touch a key, we need to move it to the earliest position in the queue/list. 

One clumsy way is to search for that node each time, which takes O(n) time. A better way is to have a mapping from key value to iterators, so that we can locate the node in O(1) time.

The good thing about lists is that iterators are never invalidated by modifiers (unless erasing the element itself). This way, we can store the iterator to the corresponding LRU queue in the values of the hash map. Since using erase on a list with an iterator takes constant time, all operations of the LRU cache run in constant time.

```c++
class LRUCache {
public:
    LRUCache(int capacity): capacity(capacity), size(0) {
        
    }
    
    int get(int key) {
        if (cache.count(key) == 0) {
            return -1;
        }
        else {
            updateLRU(key);
            return cache[key];
        }
    }
    
    void put(int key, int value) {
        if (cache.count(key) == 0) {
            if (size == capacity) {
                // lru.erase(lru.back()); 
                cache.erase(lru.back());
                key_to_lru.erase(lru.back());
                lru.pop_back(); 
                size--;
            }

            cache[key] = value;
            updateLRU(key);
            size++;

        }
        else {
            cache[key] = value;
            updateLRU(key);            
        }
    }
    
private:
    int size;
    int capacity;
    list<int> lru;
    unordered_map<int, int> cache;
    unordered_map<int, list<int>::iterator > key_to_lru;
    
    void updateLRU(int key) {
        if (key_to_lru.count(key) != 0) { // forgot this
            lru.erase(key_to_lru[key]);        
        }
        lru.push_front(key);
        key_to_lru[key] = lru.begin();
    }
};
```

## Second Attempt. 

Two major errors occur. First, to erase from the LRU list, we should erase using the iterator, which should be stored in the map from key to LRU positions.

Second, to check whether a key exist in LRU, we should check using the hash map, which only takes O(1) time.

To make the code clean, we use two helper functions `touch()` and `evict()`.

```c++
class LRUCache {
public:
    LRUCache(int capacity): capacity(capacity), size(0) {
        
    }
    
    ~LRUCache() {
        table.clear();
        key_to_pos.clear();
        lru.clear();
    }
    
    int get(int key) {
        if (table.count(key) == 0) {
            return -1;
        }
        else {
            touch(key);
            return table[key];
        }
    }
    
    void put(int key, int value) {
        if (table.size() == capacity && table.count(key) == 0) {
            evict();
        }
        table[key] = value;
        touch(key);        
    }
    
private:
    unordered_map<int, int> table;
    unordered_map<int, list<int>::iterator> key_to_pos;
    list<int> lru;
    int capacity;
    int size;
    
    void touch(int key) {
        // if (find(lru.begin(), lru.end(), key) != lru.end()) {
        if (key_to_pos.count(key) != 0) {
            // lru.remove(key);
            lru.erase(key_to_pos[key]);
        }
        lru.push_front(key);

        // when updating LRU, the mapping from key to LRU pos also needs updating
        key_to_pos[key] = lru.begin();
    }
    
    void evict() {
        int victim = lru.back();
        lru.pop_back();
        key_to_pos.erase(victim);
        table.erase(victim); // forgot this
    }
};
```
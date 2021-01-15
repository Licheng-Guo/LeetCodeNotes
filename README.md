# Typo Check list

- Should pass by reference
- Empty input or 0 elements
- What if only contains 1 element
- Initialize vector length
- Whether .empty() has be reversed
- Check if there are predicate branches not handled
- Check if `pop()`, `front()`, etc are always on non-empty containers.
- Be careful whether it is binary tree or binary search tree
- Easy to forgot changing the iterator in while loops
- Easy to get confused which container to use. E.g. while (!c.empty())
- Don't forgot to advance pointer in linked list questions.
- Check `=` vs `==`
- Check loop conditions
- Check variable by variable to see if anything is forgotten.
- When multiple data structures are interacting with each other, check for every operation if any one of them is overlooked.
- Many times the predicate of while loops contain boundary check when iterating through a container.
- When using python, be careful if deep copy is needed when returning an attribute of a class. 

# Solution Check List

- Can we use hash map to create a “reverse” mapping
- For tree manipulation, consider tree traversal in unusual order
- Can sorting help?
- DFS, BFS
- Two pointers
- Do one step, then recurse
- min(), max() may be handy

# Corner Case
- If the graph is connected?
- Does the answer always exist?
- Data type?
- 0?
- INT_MIN, INT_MAX?
- Repetition?

# STL Cheatsheet

## Vector
- Insert
```c++
it = myvector.begin();
// returns an iterator that points to the first of the newly inserted elements.
it = myvector.insert ( it , 200 ); // insert once
myvector.insert (it,2,300); // insert 300 times
myvector.insert (it+2,anothervector.begin(),anothervector.end());
int myarray [] = { 501,502,503 };
myvector.insert (myvector.begin(), myarray, myarray+3);
```

- Erase
```c++
// erase the 6th element
myvector.erase (myvector.begin()+5);

// erase the first 3 elements:
myvector.erase (myvector.begin(),myvector.begin()+3);
```


- Obtain a subset of vector.
```c++
vector<vector<int> > res(points.begin(), points.begin()+K);
```

# String

- Insert
```c++
// Inserts n consecutive copies of character c.
string& insert (size_t pos,   size_t n, char c);

// Inserts a copy of a substring of str. The substring is the portion of str that begins at the character position subpos and spans sublen characters (or until the end of str, if either str is too short or if sublen is npos).
string& insert (size_t pos, const string& str, size_t subpos, size_t sublen);

string& erase (size_t pos = 0, size_t len = npos);
```

# Heap

To create a min-heap:

```c++
typedef pair<int, int> pi; 
priority_queue<pi, vector<pi>, greater<pi> > pq;

vector<int> vec={3, 1, 4, 1, 5};
priority_queue<int> c3(std::less<int>(), vec);
```


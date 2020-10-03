# 278. First Bad Version

```c++
// The API isBadVersion is defined for you.
// bool isBadVersion(int version);

class Solution {
public:
    int firstBadVersion(int n) {
        //use binary search
        // if bad version (true), go left
        // if good version (false), go right
        // corner case: all versions bad or all versions good
        // normally, left -> good, right -> bad
        // check middle, if middle is good, left = middle
        // else right = middle
        // when there are only two versions in consideration, return the right one
        
        // double check: index starts from 1
        
        if (isBadVersion(1) == true) {
            return 1;
        }
        assert(isBadVersion(n) == true);
        
        unsigned int left = 1; unsigned int right = n;
        while (right - left + 1 > 2) {
            unsigned int mid = (left + right) / 2;
            if (isBadVersion(mid)) {
                right = mid; // again get confused about right and left. Don't use these two names next time
            }
            else {
                left = mid;
            }
        }
        
        assert(right == left + 1);
        return right;
    }
};
```

Another way to do the binary search. As long as the mid is biased to the direction that will always move, we don't need to worry about dead loop.
```c++
while (left < right) {
    unsigned int mid = (left + right) / 2;
    if (isBadVersion(mid)) {
        right = mid; // again get confused about right and left. Don't use these two names next time
    }
    else {
        left = mid+1;
    }
}
```
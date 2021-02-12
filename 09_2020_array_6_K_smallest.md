# 973. K Closest Points to Origin

We have a list of points on the plane.  Find the K closest points to the origin `(0, 0)`.

(Here, the distance between two points on a plane is the Euclidean distance.)

You may return the answer in any order.  The answer is guaranteed to be unique (except for the order that it is in.)

## Attempt 1

```c++
class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
        sort(points.begin(), points.end(), [](vector<int>& a, vector<int>& b) {
            return (pow(a[0],2) + pow(a[1],2)) < (pow(b[0],2) + pow(b[1],2));
        });
            
        vector<vector<int>> res(points.begin(), points.begin()+K); // note that it is not K-1
        return res;
    }
};
```

## Quick Select

If pivot is the K-th element in the array, and since all elements on the left are smaller than the pivot, we have reached our goal of selecting the K smallest elements but no order requirement.

```c++
vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
    int l = 0, r = points.size()-1;
    while (true) {
        int p = partition(points, l, r);
        if (p == K-1) {
            break;
        }
        else if (p < K-1) {
            l = p + 1; // be careful
        }
        else {
            r = p - 1;
        }
    }

    // how to obtain a subset of a vector
    vector<vector<int> > res(points.begin(), points.begin()+K);
    return res;
}
```

Partition version 1:

Note that we must first swap towards the larger end, because it is possible that the element swapped back from the larger end is smaller then the pivot. On the other hand, we are sure the the elements swapped from the smaller end will not be larger then the pivot.

In this implementation, `l` and `r` represents the next position of the the smaller group and the larger group. In the end, `l == r` if there is no duplicates of the pivot.

```c++
int partition(vector<vector<int>>& points, int l, int r) {
    vector<int> pivot = points[l];
    int pivot_idx = l;
    
    for (int i = l; i <= r; i++) {
        while (dist(points[i]) > dist(pivot) && i < r) swap(points[i], points[r--]);
        while (dist(points[i]) < dist(pivot) && i > l) swap(points[i], points[l++]);
    }
    
    return l;
}
```

Partition version 2:

This is the classic way of performing partitioning in quick sort. Note the `<=` conditions.

```c++
int partition(vector<vector<int>>& points, int l, int r) {
    vector<int> pivot = points[l];
    int pivot_idx = l;
    
    l++;
    
    while (l <= r) {
        if (dist(points[l]) > dist(pivot) && dist(points[r]) < dist(pivot)) {
            swap(points[l++], points[r--]);
        }
        else if (dist(points[l]) <= dist(pivot)) { // forgot the equal here
            l++;
        }
        else if (dist(points[r]) >= dist(pivot)) {
            r--;
        }
    }
    
    swap(points[pivot_idx], points[r]);
    return r;
}
```

## Attempt 2

In this implementation, I use the strategy to update the right pointer if the left side is larger than pivot and vice versa. This is less concise than update the left pointer when the left side is smaller than the pivot.

```c++
    bool operator<(const vector<int>& a, const vector<int>&b ) {
        return a[0]*a[0] + a[1]*a[1] < b[0]*b[0] + b[1]*b[1];
    }
    bool operator>(const vector<int>& a, const vector<int>&b ) {
        return b < a;
    }
    bool operator>=(const vector<int>& a, const vector<int>&b ) {
        return !(a < b);
    }
    bool operator<=(const vector<int>& a, const vector<int>&b ) {
        return !(a > b); // got this wrong
    }

class Solution {
public:
    vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
        int left = 0;
        int right = points.size()-1; // forgot the -1
        
        while (1) {
            int p = _partition(points, left, right);
            if (p == K-1) { // forgot the -1
                return vector<vector<int> >(points.begin(), points.begin()+K); 
            }
            else if (p > K-1) {
                right = p - 1;
            }
            else {
                left = p + 1;
            }
        }
    }
    
private:

    int _partition(vector<vector<int> >& points, int left, int right) {
        int pivot_idx = left;
        vector<int>& pivot = points[pivot_idx];
        left++;
        
        while (left <= right) {
            if (points[left] <= pivot && points[right] >= pivot) {
                left++; right--;
            }
            else if (points[left] > pivot && points[right] < pivot) {
                swap(points[left], points[right]);
                left++; right--;
            }
            else if (points[left] > pivot) {
                // swap(points[left], points[right-1]); => segfault
                right--;
            }
            else if (points[right] < pivot) {
                // swap(points[right], points[left+1]); => segfault
                left++;
            }
        }
        
        swap(points[pivot_idx],points[right]);
        return right;
    }
};
```

An recursive implementation

```c++
    vector<vector<int>> kClosest(vector<vector<int>>& points, int K) {
        int left = 0;
        int right = points.size()-1; 
        return _kClosest(points, left, right, K);
    }
    
    vec_t _kClosest(vector<vector<int>>& points, int left, int right, int K) {
        
        if (left > right) {return {}; }
        if (left == right) {
            assert(K == 1);
            return vec_t(points.begin()+left, points.begin()+right+1);
        }
        
        int p = _partition(points, left, right);

        if (p-left == K-1) { // pay attention here! K is relative to left
            return vec_t(points.begin()+left, points.begin()+p+1);
        }
        else if (p-left < K-1) {
            vec_t part1 = vec_t(points.begin()+left, points.begin()+p+1);
            vec_t part2 = _kClosest(points, p+1, right, K - (p-left+1) );
            part1.insert(part1.end(), part2.begin(), part2.end());
            return part1;
        }
        else {
            return _kClosest(points, left, p-1, K);
        }
    }
```

Another definition of `K` that makes things more concise:
```c++
    vec_t _kClosest(vector<vector<int>>& points, int left, int right, int K) {        
        if (left > right) {return {}; }
        if (left == right) {
            assert(K-1 == left);
            return vec_t(points.begin()+left, points.begin()+right+1);
        }
        
        int p = _partition(points, left, right);

        if (p == K-1) {
            return vec_t(points.begin()+left, points.begin()+p+1);
        }
        else if (p < K-1) {
            vec_t part1 = vec_t(points.begin()+left, points.begin()+p+1);
            vec_t part2 = _kClosest(points, p+1, right, K);
            part1.insert(part1.end(), part2.begin(), part2.end());
            return part1;
        }
        else {
            return _kClosest(points, left, p-1, K);
        }
    }
```
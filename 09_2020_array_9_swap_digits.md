# 670. Maximum Swap

Given a non-negative integer, you could swap two digits at most once to get the maximum valued number. Return the maximum valued number you could get.

## First Attempt

Forgot the corner case: when there are repetitive digits, we should swap with the last one.

```c++
class Solution {
public:
    int maximumSwap(int num) {
        string digits = to_string(num); // int to string
        string digits_sort = to_string(num);
        sort(digits_sort.rbegin(), digits_sort.rend()); // confused with sorting order
        
        unordered_map<char, set<int> > order;        
        for (int i = 0; i < digits.length(); i++) {
            order[digits[i]].insert(i);
        }
                
        for (int i = 0; i < digits.length(); i++) {
            if( digits_sort[i] > digits[i]) {

                int target = *(order[digits_sort[i]].rbegin()); // confused with rend()
                swap(digits[i], digits[target]);
                
                break;
            }
        }
        
        return stoi(digits); // string to int
    }
}
```

## Reference Solution

First record the last existing position of each digit. Then starting from the MSB, for each digit larger than MSB, check if its last exsiting position is less significant than the MSB.

Note that `break` only escapes one layer of loop.

```c++
class Solution {
public:

    int maximumSwap(int num) {
        string num_str = to_string(num);
        vector<int> last(10, -1);
        
        for(int i = num_str.length()-1; i>= 0; i--) {
            if (last[num_str[i]-'0'] == -1) {
                last[num_str[i]-'0'] = i;
            }
        }
        
        for (int i = 0;  i < num_str.length(); i++) {
            for (int j = 9; j > num_str[i]-'0'; j--) { // mistaken for ++
                if (last[j] > i) {
                    swap(num_str[i], num_str[last[j]]);
                    // break; // note that there are two loop layers
                    return stoi(num_str); 
                }
            }
        }
            
        return num;
    }
};
```
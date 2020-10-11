# 5. Longest Palindromic Substring

Given a string s, return the longest palindromic substring in s.

## Reference Solution

First determine the status of all 1-char sub-strings and 2-char sub-strings, then for each of them gradually grow outwards.

```c++
class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size();
        bool table[n][n]; // much faster than vector
        string ans = "";
        
        for ( int i = 0; i < s.size(); i++) {
            table[i][i] = true;
        }
        
        // don't forget that the answer may be in the corner cases
        for (int i = 0; i < s.size()-1; i++) {
            if (s[i] == s[i+1]) {
                table[i][i+1] = true;
            }
            else {
                table[i][i+1] = false;
            }
        }
        
        
        for (int len = 3; len <= s.size(); len++) { // forgot the equal
            for (int i = 0; i < s.size()-len+1; i++) {
                table[i][i+len-1] = table[i+1][i+len-2] && s[i] == s[i+len-1];
            }
        }
        
        for (int len = s.size(); len >= 0; len--) {
            for (int i = 0; i < s.size()-len+1; i++) {
                if (table[i][i+len-1]) {
                    return s.substr(i, len); // note that the second argument is length
                }
            }
        }
        
        return "";
    }
};
```
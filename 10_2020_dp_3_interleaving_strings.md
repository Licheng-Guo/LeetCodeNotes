# 97. Interleaving String

Given s1, s2, and s3, find whether s3 is formed by the interleaving of s1 and s2.

## Reference Solution

Try to do one step and see if we arrive at the same problem, only with different inputs.

```c++
class Solution {
public:
    bool isInterleave(string s1, string s2, string s3) {
        // if s1[0] == s3[0] check (s1[1:], s2, s3[1:])
        // if s2[0] == s3[0] check (s1, s2[1:], s3[1:])
        
        // check if s1[0:i] + s2[0:j] matches s3[0:i+j+1]
        
        int len1 = s1.length();
        int len2 = s2.length();
        if (s3.length() != len1+len2) return false;
        
        bool table[len1+1][len2+1];
        
        table[0][0] = true;
        for (int i = 1; i <= len1; i++) {
            table[i][0] = table[i-1][0] && s1[i-1] == s3[i-1];
        }
        
        for (int j = 1; j <= len2; j++) { // forgot the equal here
            table[0][j] = table[0][j-1] && s2[j-1] == s3[j-1];
        }
        
        for (int i = 1; i <= len1; i++) {
            for (int j = 1; j <= len2; j++) {
                bool ans1 = s1[i-1] == s3[i+j-1] && table[i-1][j];
                bool ans2 = s2[j-1] == s3[i+j-1] && table[i][j-1];
                table[i][j] = ans1 | ans2;
            }
        }

        return table[len1][len2];
        
    }
};
```
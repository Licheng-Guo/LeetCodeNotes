# 415. Add Strings

Given two non-negative integers num1 and num2 represented as string, return the sum of num1 and num2.

## First Attempt

```c++
class Solution {
public:
    string addStrings(string num1, string num2) {
        
        int len1 = num1.length();
        int len2 = num2.length();
        if (len1 > len2) {
            num2.insert(num2.begin(), len1-len2, '0');
        }
        else if (len1 < len2) {
            num1.insert(num1.begin(), len2-len1, '0');
        }
        
        int len = max(len1, len2);
        int carry = 0;
        string ans = "";
        
        for( int i = len-1; i >= 0; i--) { // make a mistake about -1
            int a = num1[i] - '0';
            int b = num2[i] - '0';
            int sum = (a + b + carry) % 10;
            carry = (a + b + carry) / 10;
            ans += to_string(sum);
        }
        if (carry) ans += "1"; // forgot this
        
        reverse(ans.begin(), ans.end());
        return ans;
    }
};
```

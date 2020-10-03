# 125. Valid Palindrome

Given a string, determine if it is a palindrome, considering only alphanumeric characters and ignoring cases.

Note: For the purpose of this problem, we define empty string as valid palindrome.

## First Attempt

```c++
class Solution {
public:
    bool isPalindrome(string s) {
        if (s.length() == 0) {
            return true;
        }
        
        int beg = 0; int end = s.length()-1;
        
        while (beg <= end) {
            if (!isValid(s[beg]) ) {
                beg++;
            }
            else if (!isValid(s[end]) ) {
                end--;
            }
            else {
                if (isEqual(s[beg], s[end]) ) {
                    beg++; end--;
                } 
                else {
                    return false;
                }
            }
        }
        
        return true;
    }
    
private:
    bool isValid(char c) {
        return isalpha(c) || isdigit(c);
    }
    
    bool isEqual(char a, char b) {
        if (isalpha(a)) {
            a = tolower(a);
        }
        if (isalpha(b)) {
            b = tolower(b);
        }
        
        return a == b;
    }
};
```

# 680. Valid Palindrome II

Given a non-empty string s, you may delete at most one character. Judge whether you can make it a palindrome.

## First Attempt

```c++
class Solution {
public:
    bool validPalindrome(string s) {
        if (s.length() == 0) {
            return true;
        }
        
        int beg = 0; int end = s.length()-1;
        
        int hp = 1;
        
        return isPalindrome(s, beg, end, hp);
    }
    
private:
    bool isPalindrome(string s, int beg, int end, int hp) {
        while (beg <= end) {
            if (!isValid(s[beg]) ) {
                beg++;
            }
            else if (!isValid(s[end]) ) {
                end--;
            }
            else {
                if (isEqual(s[beg], s[end]) ) {
                    beg++; end--;
                } 
                else {
                    if (hp) {
                        if (isPalindrome(s, beg, end-1, 0)) return true;
                        else if (isPalindrome(s, beg+1, end, 0)) return true;
                        else return false;
                    }
                    else {
                        return false;
                    }
                }
            }
        }
        
        return true;
    }
    
    
    bool isValid(char c) {
        return isalpha(c) || isdigit(c);
    }
    
    bool isEqual(char a, char b) {
        if (isalpha(a)) {
            a = tolower(a);
        }
        if (isalpha(b)) {
            b = tolower(b);
        }
        
        return a == b;
    }
};
```
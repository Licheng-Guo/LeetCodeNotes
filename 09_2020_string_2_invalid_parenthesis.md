# 1249. Minimum Remove to Make Valid Parentheses

Given a string s of '(' , ')' and lowercase English characters. 

Your task is to remove the minimum number of parentheses ( '(' or ')', in any positions ) so that the resulting parentheses string is valid and return any one valid string.

## First Attempt

```c++
class Solution {
public:
    string minRemoveToMakeValid(string s) {
        stack<pair<int, int> > mystack;
        
        for (int i = 0; i < s.length(); i++) {
            if (s[i] == '(') {
                mystack.push(pair<int, int>(0, i));
            }
            else if (s[i] == ')') {
                if (!mystack.empty()) { // forgot this
                    if (mystack.top().first == 0) {
                        mystack.pop();
                    } 
                    else {
                        mystack.push(pair<int, int>(1, i));
                    }
                }
                else {
                    mystack.push(pair<int, int>(1, i));
                }
            }
        }
        
        while (!mystack.empty()) {

            // how to erase one char from a string str.erase(pos, len)
            s.erase(mystack.top().second, 1);
            mystack.pop();
        }
        
        return s;
    }
};
```

## Reference Implementation
Instead of tracking the indexes, we can change the candidates to special characters and remove those at the end. Also, we do not have to differentiate what's pushed onto the stack. Instead, we could only push `(` onto the stack and resolve `)` on the fly.

```c++
string minRemoveToMakeValid(string s) {
  stack<int> st;
  for (auto i = 0; i < s.size(); ++i) {
    if (s[i] == '(') st.push(i);
    if (s[i] == ')') {
      if (!st.empty()) st.pop();
      else s[i] = '*';
    }
  }
  while (!st.empty()) {
    s[st.top()] = '*';
    st.pop();
  }
  s.erase(remove(s.begin(), s.end(), '*'), s.end());
  return s;
}
```
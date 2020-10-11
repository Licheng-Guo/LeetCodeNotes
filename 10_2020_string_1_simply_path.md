# 71. Simplify Path


Given an absolute path for a file (Unix-style), simplify it. Or in other words, convert it to the canonical path.

In a UNIX-style file system, a period . refers to the current directory. Furthermore, a double period .. moves the directory up a level.

Note that the returned canonical path must always begin with a slash /, and there must be only a single slash / between two directory names. The last directory name (if it exists) must not end with a trailing /. Also, the canonical path must be the shortest string representing the absolute path.

## First Attempt

```c++
class Solution {
public:
    string simplifyPath(string path) {
        // use a stack 
        vector<string> dir_list = getDirList(path);
        stack<string> s;
        for (int i = 0; i < dir_list.size(); i++) {
            if (dir_list[i] == "") {
                continue;
            }
            else if (dir_list[i] == ".") {
                continue;
            }
            else if (dir_list[i] == "..") {
                if (s.size()) {
                    s.pop();
                }
            }
            else {
                s.push(dir_list[i]);
            }
        }
        
        string ans = "";
        while (!s.empty()) {
            ans = "/" + s.top() + ans; s.pop();
        }
        
        if (ans == "") ans = "/";
        return ans;
    }
    
private:
    // string split in c++
    vector<string> getDirList(string path) {
        vector<string> dir_list;
        string curr = "";
        stringstream ss(path);
        while (getline(ss, curr, '/' )) { // note that the 3rd arg is a char
            dir_list.push_back(curr);               
        }
        
        return dir_list;
    }
};
```
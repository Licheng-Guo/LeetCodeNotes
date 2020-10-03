# 249. Group Shifted Strings

Given a string, we can "shift" each of its letter to its successive letter, for example: `"abc" -> "bcd"`. We can keep "shifting" which forms the sequence:

```
"abc" -> "bcd" -> ... -> "xyz"
```
Given a list of non-empty strings which contains only lowercase alphabets, group all strings that belong to the same shifting sequence.

## First Attempt

```c++
class Solution {
public:
    vector<vector<string>> groupStrings(vector<string>& strings) {
        // calculate the distance vector of each input token
        // group inputs together with a hash map: dist vect -> token
        
        unordered_map<string, vector<string> > groups;
        
        for (auto &token : strings) {
            groups[getDistVec(token)].push_back(token);
        }
        
        vector<vector<string> > ans;
        for (auto &kv_pair: groups) {
            ans.push_back(std::move(kv_pair.second));
        }
        
        return ans;
    }
    
private:
    string getDistVec(string& token) {        

        string dist_vec = "";
        for (int i = 0; i < token.length()-1; i++) {
            int val = token[i+1] - token[i];
            if (val < 0) val += 26;
            // dist_vec += to_string(val);
            dist_vec += 'a' + val; // this is better
        }
        
        return dist_vec;
    }
};
```
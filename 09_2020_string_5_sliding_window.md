# 438. Find All Anagrams in a String

Given a string s and a non-empty string p, find all the start indices of p's anagrams in s.

## Reference Solution

```c++
class Solution {
public:
    vector<int> findAnagrams(string s, string p) {
        if (s.length() < p.length()) {
            return {};
        }
        
        vector<int> freq_s(26, 0);
        vector<int> freq_p(26, 0);
        int win_size = p.length();
        vector<int> ans;
        
        for (int i = 0; i < p.length(); i++) {
            freq_p[p[i]-'a']++;
        }
        
        for (int i = 0; i < win_size; i++) {
            freq_s[s[i] - 'a']++;
        }
        if (freq_s == freq_p) {
            ans.push_back(0);
        }
        
        for (int i = 0; i < s.length() - win_size; i++) {
            freq_s[s[i]-'a']--;
            freq_s[s[i+win_size]-'a']++;
            
            if (freq_s == freq_p) {  // we could directly compare two vectors or maps
                ans.push_back(i+1);
            }
        }
        
        return ans;
    }
};
```

# 187. Repeated DNA Sequences


All DNA is composed of a series of nucleotides abbreviated as A, C, G, and T, for example: "ACGAATTCCG". When studying DNA, it is sometimes useful to identify repeated sequences within the DNA.

Write a function to find all the 10-letter-long sequences (substrings) that occur more than once in a DNA molecule.


## Reference Solution 1

It is possible to manually compute a distinct "hash" value for each unique sequence.


```c++
class Solution {
public:
    vector<string> findRepeatedDnaSequences(string s) {
        if (s.length() < 10 ) return {};
        unordered_map<char, int> encode = {{'A',0}, {'C',1}, {'G',2}, {'T',3}};
        
        set<long long> counter;
        
        long long win_val = 0;
        for(int i = 0; i < 10; i++) {
            win_val *= 4;
            win_val += encode[s[i]];
        }
        counter.insert(win_val);
        
        set<string> ans;
        
        for (int i = 0; i < s.length()-10; i++) {
            win_val -= encode[s[i]] * pow(4, 9);
            win_val *= 4;
            win_val += encode[s[i+10]];
            if (counter.count(win_val) != 0) {
                ans.insert(s.substr(i+1, 10));
            }
            else {
                counter.insert(win_val);
            }
        }
        
        return vector<string>(ans.begin(), ans.end());
    }
};
```
# 953. Verifying an Alien Dictionary

In an alien language, surprisingly they also use english lowercase letters, but possibly in a different `order`. The `order` of the alphabet is some permutation of lowercase letters.

Given a sequence of `words` written in the alien language, and the `order` of the alphabet, return `true` if and only if the given `words` are sorted lexicographicaly in this alien language.

## First Attempt

```c++
class Solution {
public:
    bool isAlienSorted(vector<string>& words, string order) {
        unordered_map<char, int> value;
        for (int i = 0; i < order.length(); i++) {
            value[order[i]] = i+1;
        }
        
        int max_len = 0;
        for (auto &w : words) {
            if (w.length() > max_len) max_len = w.length();
        }
        
        double curr = 0;
        for (int i = 0; i < words.size(); i++) {
            double next = getValue(words[i], max_len, value);            
            if (next < curr) {
                return false;
            }
            curr = next;
        }
        return true;
    }

private:
    double getValue(string word, int max_len, unordered_map<char, int>& value) {
        double val = 0;
        for (int i = 0; i < word.length(); i++) {
            val *= 26;
            val += value[word[i]]; // forgot =
        }
        for (int i = 0; i < max_len - word.length(); i++) {
            val *= 26;
        }
        return val;
    }
};
```

## Reference Implementation

```c++
bool isAlienSorted(vector<string> &words, string order) {
    size_t indices[26]{};
    for (size_t i = 0; i < order.size(); ++i) {
        indices[order[i] - 'a'] = i;
    }
    return is_sorted(words.begin(), words.end(), [&indices](
            const string &w1,
            const string &w2
    ) {
        auto l1 = w1.size(), l2 = w2.size();
        for (auto i = 0; i < min(l1, l2); ++i) {
            auto c1 = w1[i], c2 = w2[i];
            if (c1 != c2) {
                return indices[c1 - 'a'] < indices[c2 - 'a'];
            }
        }
        return l1 < l2;
    });
}
```
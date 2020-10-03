# 211. Design Add and Search Words Data Structure

Design a data structure that supports adding new words and finding if a string matches any previously added string.

Implement the `WordDictionary` class:

`WordDictionary()` Initializes the object.

`void addWord(word)` Adds word to the data structure, it can be matched later.

`bool search(word)` Returns true if there is any string in the data structure that matches word or false otherwise. word may contain dots `'.'` where dots can be matched with any letter.

## First Attempt

```c++
class TrieNode {
public:
    bool is_word;
    vector<TrieNode*> children;
    
    TrieNode(): children(26, nullptr), is_word(false) {} // correct way to initialize member variables
}; // forgot the ;

class WordDictionary {
public:
    WordDictionary() {
        root = new TrieNode();
    }
    
    void addWord(string word) {
        TrieNode *curr = root;
        for(int i = 0; i < word.length(); i++) {
            if (!curr->children[word[i] - 'a']) {
                curr->children[word[i] - 'a'] = new TrieNode();
            }         
            curr = curr->children[word[i] - 'a'];
        }
        curr->is_word = true;
    }
    
    bool search(string word) {
        return search(word, root);
    }

private:
    TrieNode *root;
    
    bool search(string word, TrieNode *curr) {
        if (!curr) {
            return false;
        }
        // else if (word.length() == 0 && curr->is_word) { // unhandled branch
        //     return true;
        // }
        else if (word.length() == 0) {
            return curr->is_word;
        }
        
        if (word[0] != '.') {
            return search(word.substr(1, word.length()-1), curr->children[word[0] - 'a']);
        }
        else {
            for (int i = 0; i < 26; i++) {
                if (search(word.substr(1, word.length()-1), curr->children[i])) {
                    return true;
                }
            }
            return false;
        }
    }
};
```
# 721. Accounts Merge

Given a list accounts, each element `accounts[i]` is a list of strings, where the first element `accounts[i][0]` is a name, and the rest of the elements are emails representing emails of the account.

Now, we would like to merge these accounts. Two accounts definitely belong to the same person if there is some email that is common to both accounts. Note that even if two accounts have the same name, they may belong to different people as people could have the same name. A person can have any number of accounts initially, but all of their accounts definitely have the same name.

After merging the accounts, return the accounts in the following format: the first element of each account is the name, and the rest of the elements are emails in sorted order. The accounts themselves can be returned in any order.

## First Attempt

Note that for BFS we decouple the actual visiting of a node from marking the node as already visited. When enqueuing a node, we mark it as visited because otherwise we may enqueue the same node twice. However, we only visit the node when dequeuing it.

Be carefule when dealing with nested vectors, remember to use reference.

It is extremely easy to make typo when using `i` and `j` as iterators.

```c++
class Solution {
public:
    vector<vector<string>> accountsMerge(vector<vector<string>>& accounts) {
        
        unordered_map<string, unordered_set<string> > graph;
        unordered_map<string, string> em_to_name;
        for (int i = 0; i < accounts.size(); i++) {
            vector<string>& profile = accounts[i]; // forgot the reference here
            
            assert(profile.size() >= 2);
            em_to_name[profile[1]] = profile[0];
            string parent = profile[1];
            // for (int j = 2; i < profile.size(); j++) { // don't use i and j!
            for (int j = 2; j < profile.size(); j++) {
                string child = profile[j];
                graph[parent].insert(child);
                graph[child].insert(parent);
                
                em_to_name[child] = profile[0];
            }
        }
        
        vector<vector<string> > ans;
        unordered_set<string> visited;
        for (auto &kv : em_to_name) {
            if (visited.count(kv.first) == 0) {
                vector<string> all_em = getAllEmails(kv.first, graph, visited);
                sort(all_em.begin(), all_em.end());
                all_em.insert(all_em.begin(), em_to_name[kv.first]);
                ans.push_back(move(all_em));
            }
        }
        
        return ans;
    }
    
private:
    vector<string> getAllEmails(
            string name, 
            unordered_map<string, unordered_set<string> >& graph,
            unordered_set<string>& visited) {
        
        if (graph.find(name) == graph.end()) {
            return {name};
        }
        
        queue<string> q;
        q.push(name);
        visited.insert(name);
        vector<string> ans;
        // ans.push_back(name); // wrong!
        // in BFS, we decouple the actual visiting from the marking of already visited.
                
        while (!q.empty()) {
            string curr = q.front(); q.pop();
            ans.push_back(curr);
            
            for (string nei : graph[curr]) {
                if (visited.count(nei) == 0) {
                    visited.insert(nei);
                    q.push(nei);
                }
            }
            
        }
        
        return ans;
    }
};
```

It is also feasible to check if a node has been visited when dequeueing it, but that may lead to more work.

```c++
queue<string> q;
q.push(name);
vector<string> ans;
        
while (!q.empty()) {
    string curr = q.front(); q.pop();
    if (visited.count(curr)) {
        continue;
    }
    
    visited.insert(curr);
    ans.push_back(curr);
    
    for (string nei : graph[curr]) {
        q.push(nei);
    }     
}
```
# 621. Task Scheduler

Given a characters array tasks, representing the tasks a CPU needs to do, where each letter represents a different task. Tasks could be done in any order. Each task is done in one unit of time. For each unit of time, the CPU could complete either one task or just be idle.

However, there is a non-negative integer n that represents the cooldown period between two same tasks (the same letter in the array), that is that there must be at least n units of time between any two same tasks.

Return the least number of units of times that the CPU will take to finish all the given tasks.

## Reference Solution

First, the idle interval is a **minimal** but not a **fixed** value. It is OK to schedule more tasks in between two same tasks.

Second, our focus is on the most frequent task. Suppose that the cooling interval is large enough, we could always schedule the remaining tasks in the idle slots, because all other tasks are no more frequency than the most frequenct task. As a result, either the total cycle is determined solely by the most frequent task (when the cooling interval is large enough), or the total cycle count is exactly how many tasks there are (when the cooling interval is small).

One cornor case is that if there are multiple most-frequency tasks, we have some tail cycles to take care of.

```c++
class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        vector<int> counter(26, 0);
        for (char c : tasks) {
            counter[c - 'A']++;
        }
        
        sort(counter.begin(), counter.end());
        
        int n_max = 0;
        for (int i = 25; i >= 0; i--) {
            if (counter[i] == counter.back()) {
                n_max++;
            }
            else {
                break;
            }
        }
        
        return max ( int(tasks.size()), (n+1)*(counter.back()-1)+n_max );
    }
};
```
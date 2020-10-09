# 322. Coin Change

You are given coins of different denominations and a total amount of money amount. Write a function to compute the fewest number of coins that you need to make up that amount. If that amount of money cannot be made up by any combination of the coins, return -1.

You may assume that you have an infinite number of each kind of coin.

## First Attempt
Note that if we use memorization to implement DP problems, don't forget the initial corner cases!

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> cache(amount+1, -2);
        
        return coinChange(coins, amount, cache);
    }
    
private:
    int coinChange(vector<int>& coins, int amount, vector<int>& cache) {
        
        // forgot these two!
        if (amount == 0) return 0;
        if (amount < 0) return -1;
        
        if (cache[amount] != -2) {
            return cache[amount];
        }
        
        int local_min = INT_MAX;
        for (auto n : coins) {
            int curr = coinChange(coins, amount-n, cache);
            if (curr != -1) local_min = min(local_min, curr);
        }
        
        if (local_min != INT_MAX) cache[amount] = local_min+1;
        else cache[amount] = -1;
        
        return cache[amount];
    }
};
```

## Reference Solution

Feels similar to Djikstra's algorithm.

```python
class Solution:
    def coinChange(self, coins: List[int], amount: int) -> int:
        dp = [float('inf')] * (amount + 1)
        dp[0] = 0
        
        for coin in coins:
            for x in range(coin, amount + 1):
                dp[x] = min(dp[x], dp[x - coin] + 1)
        return dp[amount] if dp[amount] != float('inf') else -1 
```
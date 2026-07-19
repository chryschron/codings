# 1回目
実装方針自体はすぐ立った

```c++
class Solution {
public:
    int coinChange(const vector<int>& coins, int amount) {
        if (amount == 0) {
            return 0;
        }

        vector<int> minCoins(amount + 1);
        for (auto coin : coins) {
            if (coin > amount) {
                continue;
            }
            minCoins[coin] = 1;
        }

        for (int i = 1; i < amount; i++) {
            if (minCoins[i] == 0) {
                continue;
            }

            for (auto coin : coins) {
                uint64_t valueSum = (uint64_t)i + (uint64_t)coin;
                if (valueSum > (uint64_t)amount) {
                    continue;
                }
                if (minCoins[valueSum] == 0) {
                    minCoins[valueSum] = minCoins[i] + 1;
                } else {
                    minCoins[valueSum] = min(
                        minCoins[valueSum],
                        minCoins[i] + 1
                    );
                }
            }
        }
        return minCoins[amount] != 0 ? minCoins[amount] : -1;
    }
};
```

## memo
- `amount == M, coins.size() == N`として時間計算量O(MN)、空間計算量O(M)
	- `M <= 10^4, N <= 12`なので大体100μsオーダー
- `coin <= INT_MAX`なので、`valueSum`は`64bit`にする必要がある

# 2回目
初期値を`amount`(最大のコイン枚数)以上の値にしておけばもっとシンプルに書ける

```c++
class Solution {
public:
    int coinChange(const vector<int>& coins, int amount) {
        const int NO_CHANGE = amount + 1;
        vector<int> minCoins(
            amount + 1,
            NO_CHANGE
        );

        minCoins[0] = 0;
        for (auto coin : coins) {
            for (int i = coin; i < minCoins.size(); i++) {
                minCoins[i] = min(
                    minCoins[i - coin] + 1,
                    minCoins[i]
                );
            }
        }
        return minCoins[amount] != NO_CHANGE ?
               minCoins[amount] : -1;
    }
};
```

## memo
- 高々`amount`枚の1円が答えなので、`amount + 1`を便宜上`NO_CHANGE`として、そこから減らしていく

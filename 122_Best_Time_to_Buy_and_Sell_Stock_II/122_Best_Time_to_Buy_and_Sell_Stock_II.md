# 1回目
一日ごとに売買すれば良さそう

```c++
class Solution {
public:
    int maxProfit(const vector<int>& prices) {
        int max_profit = 0;
        if (prices.empty()) {
            return max_profit;
        }

        for (int i = 1; i < prices.size(); i++) {
            if (prices[i] > prices[i - 1]) {
                max_profit += prices[i] - prices[i - 1];
            }
        }
        return max_profit;
    }
};
```

## memo
- `prices`の要素数をNとして時間計算量O(N)、空間計算量O(1)
	- `N <= 3*10^4`なので10μsオーダー
- `price <= 10^4`なのでintでよい

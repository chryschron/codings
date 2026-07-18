# 1回目
普通に解く

```c++
class Solution {
public:
    int maxProfit(const vector<int>& prices) {
        int min_price = numeric_limits<int>::max();
        int max_profit = 0;
        for (int price : prices) {
            if (price < min_price) {
                min_price = price;
            } else {
                max_profit = max(max_profit, price - min_price);
            }
        }
        return max_profit;
    }
};
```

## memo
- Nを`prices`の要素数として時間計算量O(N)、メモリO(1)
	- `N <= 10^5`なので100μsオーダー
- `0 <= price <= 10^4`なのでintで十分

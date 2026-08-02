# 1回目
分からなかったので答えを見た。最大最小値を求めてから二分探索を回せば良い

```c++
class Solution {
public:
    int shipWithinDays(const vector<int>& weights, int days) {
        int min_weight = *max_element(weights.begin(), weights.end());
        int max_weight = reduce(weights.begin(), weights.end());

        while (min_weight < max_weight) {
            int mid_weight = (min_weight + max_weight) / 2;
            int days_mid = 1;
            int limit = 0;
            for (int weight : weights) {
                if (limit + weight > mid_weight) {
                    days_mid++;
                    limit = 0;
                }
                limit += weight;
            }

            if (days_mid > days) {
                min_weight = mid_weight + 1;
            } else {
                max_weight = mid_weight;
            }
        }
        return max_weight;
    }
};
```

## memo
- `weights`の要素数をN、`max_weight - min_weight`をSとして時間計算量O(NlogS)、空間計算量O(1)
	- `N <= 5*10^4`、`S <= 500 * N`なので高々1msオーダー
- `max_element()`なのに`min_weight`としているのが気持ち悪い

# 2回目
1回目のコードを書き、通した

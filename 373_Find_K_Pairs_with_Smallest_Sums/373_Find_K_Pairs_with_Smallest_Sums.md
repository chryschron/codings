# 1回目
Trivialな方法しか思い浮かばなかったので、悪いとはわかっているが一応試した(当然TLE)

```c++
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        std::priority_queue<pair<int, int>> sum_u;
        for (const int u : nums1) {
            for (const int v : nums2) {
                sum_u.push({u + v, u});
                if (sum_u.size() > k)
                    sum_u.pop();
            }
        }
        std::vector<std::vector<int>> result;
        while (!sum_u.empty()) {
            result.push_back({sum_u.top().second, sum_u.top().first - sum_u.top().second});
            sum_u.pop();
        }
        return result;
    }
};
```

## memo
- 良くないところ
	- 最初の二重forで全パターン列挙してしまっている
	- `nums1` `nums2`の要素数をそれぞれM NとすればO(MNlogK)

# 2回目
考えたがよく分からなかったのでLLMに頼った。K-way Mergeという手法を使うとのこと。

```c++
class Solution {
public:
    vector<vector<int>> kSmallestPairs(vector<int>& nums1, vector<int>& nums2, int k) {
        // sum, i, j
        using T = tuple<int, int, int>;
        priority_queue<T, vector<T>, greater<T>> pq;

        for (int i = 0; i < min((size_t)k, nums1.size()); i++)
            pq.push({nums1[i] + nums2[0], i, 0});

        vector<vector<int>> result;
        while (!pq.empty() && k--) {
            const auto [sum, i, j] = pq.top();
            pq.pop();

            result.push_back({nums1[i], nums2[j]});
            if (j + 1 < nums2.size()) {
                pq.push({nums1[i] + nums2[j + 1], i, j + 1});
            }
        }

        return result;
    }
};
```

## memo
- `nums1`縦`num2`横の二次元配列を考えると、元がソート済みなので左上から右下にかけて増加
- 故にまずnums2[0]に固定すれば各行の配列の最小値がわかる、そこから更に全体の最小値を取って、該当行を右に一個ずらすことを繰り返す
	- 最小の値だけを取り続けられる
- 時間計算量はa=min(nums1.size(), k)としてO(k log a)、メモリはO(a)

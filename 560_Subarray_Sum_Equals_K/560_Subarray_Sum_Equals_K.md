# 1回目
`nums.length <= 2 * 10^4`なので素朴な解き方(O(N^2))ではギリギリ。調べたところpartial sumを用いるとO(N)で解けると分かったため、それで実装した。

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> prefix_sum_count{{0, 1}};

        int prefix_sum = 0;
        int total_subarray = 0;
        for (const int num : nums) {
            prefix_sum += num;
            
            if (const auto it = prefix_sum_count.find(prefix_sum - k); it != prefix_sum_count.end()) {
                total_subarray += it->second;
            }
            prefix_sum_count[prefix_sum]++;
        }
        return total_subarray;
    }
};
```

## memo
- `N = nums.length`として計算量 O(N)、メモリO(N)
- `{0, 1}`は`partial_sum == k`用

# 2回目
上のコードで特に問題なさそうなので、素朴に解いてみる

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int total_subarray = 0;
        for (size_t i = 0; i < nums.size(); i++) {
            int partial_sum = 0;
            for (size_t j = i; j < nums.size(); j++) {
                partial_sum += nums[j];
                if (partial_sum == k) {
                    total_subarray++;
                }
            }
        }
        return total_subarray;
    }
};
```

## memo
- 計算量O(N^2)、メモリO(1)
	- ワースト10^4 * 10^4 = 10^8オーダーステップかかる。
	- TLEしなかったが2767ms掛かった。
- 配列最大長が十分小さいならアリ
	- 10^3オーダー程度なら10^9ステップ/sとしてO(N)、O(N^2)の差は高々1msオーダー

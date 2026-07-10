# 1回目
DPを用いて自分より前の要素から自分よりも小さく、更に一番長い部分列を持つ要素を見つけてそれに+1した値を入れる作業を全要素に対して行えば良い

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if (nums.size() == 0) {
            return 0;
        }
        vector<int> subseq_lens(nums.size(), 1);

        int maxlen = 1;
        for (int i = 1; i < nums.size(); i++) {
            for (int j = 0; j < i; j++) {
                if (nums[j] < nums[i]) {
                    int new_len = subseq_lens[j] + 1;
                    subseq_lens[i] = subseq_lens[i] < new_len ?
                                     new_len : subseq_lens[i];
                }
            }
            maxlen = maxlen < subseq_lens[i] ? subseq_lens[i] : maxlen;
        }
        return maxlen;
    }
};
```

## memo
- 要素数をNとして時間計算量O(N^2)、空間計算量O(N)
	- `N <= 2500`なので大体10^9ステップ、大体10msオーダー
- あらゆる要素は要素数1の部分列を形成すると見なせるので、`dp`は1で初期化する必要がある

# 2回目
Binary Searchと組み合わせることでO(NlogN)が達成できる

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        if (nums.size() == 0) {
            return 0;
        }

        vector<int> dp_bs(1, nums[0]);
        for (int i = 1; i < nums.size(); i++) {
            if (nums[i] > dp_bs.back()) {
                dp_bs.push_back(nums[i]);
            } else {
                int insert_idx = lower_bound(
                    dp_bs.begin(),
                    dp_bs.end(),
                    nums[i]
                ) - dp_bs.begin();
                dp_bs[insert_idx] = nums[i];
            }
        }
        return dp_bs.size();
    }
};
```

## memo
- 時間計算量O(NlogN)、空間計算量O(N)
	- `log_2 2500 ~ 11`なので高々10^5ステップ、100μsオーダーに収まる
		- 二分探索はO(logN)
- なぜこれでうまく行くのか
	- `dp_bs.back() < nums[i]`のときは部分列の長さが伸びる
	- そうでないとき、最大部分列の長さという情報は残しながらも後続でもっといい部分列を作れる可能性を残せる(=`bp_bs`内の数字が小さいほど、先述の条件が満たされやすい)
	- 配列は常にソートされているので、`lower_bound`を使ってO(logN)を達成できる
- `dp_bs`よりもいい名前が思い浮かばなかった…

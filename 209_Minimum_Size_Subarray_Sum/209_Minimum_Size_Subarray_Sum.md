# 1回目
Sliding Windowで解く

```c++
class Solution {
public:
    int minSubArrayLen(int target, const vector<int>& nums) {
        int minLength, subArraySum, l, r;

        minLength = numeric_limits<int>::max();
        subArraySum = 0;
        l = 0;
        for (r = 0; r < nums.size(); r++) {
            subArraySum += nums[r];
            if (subArraySum >= target) {
                while (subArraySum - nums[l] >= target) {
                    subArraySum -= nums[l];
                    l++;
                }
                minLength = min(minLength, r - l + 1);
            }
        }
        return minLength != numeric_limits<int>::max() ?
               minLength : 0;
    }
};
```

## memo
- 時間計算量O(N)、空間計算量O(1)
	- `N <= 10^5`ゆえ、高々100μsオーダー
- 今回は[l, r]の範囲で考えた
- `subArraySum`は最大で`10^4*10^5 = 10^9 < 2^30 < 2^31 - 1`ゆえ、`int`型で大丈夫

# 2回目
累積和と二分探索を使うと時間計算量がO(NlogN)になる

```c++
class Solution {
public:
    int minSubArrayLen(int target, const vector<int>& nums) {
        vector<uint64_t> prefixSums(nums.size() + 1);

        for (int i = 0; i < nums.size(); i++) {
            prefixSums[i + 1] = prefixSums[i] + nums[i];
        }

        int minLength = numeric_limits<int>::max();
        for (int i = 0; i < nums.size() + 1; i++) {
            int left = i;
            int right = nums.size();
            int validMinIndex = -1;
            
            while (left <= right) {
                int mid = left + (right - left) / 2;
                if (prefixSums[mid] >= target + prefixSums[i]) {
                    validMinIndex = mid;
                    right = mid - 1;
                } else {
                    left = mid + 1;
                }
            }
            if (validIndex != -1) {
                minLength = min(minLength, validMinIndex - i);
            }
        }
        return minLength != numeric_limits<int>::max() ?
               minLength : 0;
    }
};
```

## memo
- `nums`の要素が正なので`prefixSums`は増加数列になり、二分探査が使える
- 時間計算量O(NlogN)、空間計算O(N)
	- `log10^5<log(2^17)=17`ゆえ、1msオーダー程度
- 二分探査を用いて`prefixSums`から`target + prefixSums[i]`以上になる最小のインデックスを探す
- 個人的にSliding Windowの方が自然に書けるし、何より計算量が少なく済む
	- 今回は`prefixSum + target`がintの範囲を超過する可能性があるので`uint64_t`配列にする必要がある
	- 面倒

# 3回目
二分探査は`lower_bound`を使える

```c++
class Solution {
public:
    int minSubArrayLen(int target, const vector<int>& nums) {
        vector<uint64_t> prefixSums(nums.size() + 1, 0);

        for (int i = 0; i < nums.size(); i++) {
            prefixSums[i + 1] = prefixSums[i] + nums[i];
        }

        int minLength = numeric_limits<int>::max();
        for (int i = 0; i < nums.size(); i++) {
            auto it = lower_bound(prefixSums.begin() + i + 1, prefixSums.end(), target + prefixSums[i]);
            
            if (it != prefixSums.end()) {
                int validMinIndex = distance(prefixSums.begin(), it);
                minLength = min(minLength, validMinIndex - i);
            }
        }
        return minLength != numeric_limits<int>::max() ? minLength : 0;
    }
};
```

## memo
- `lower_bound`: `[first, last)`範囲内で指定された値以上が入っているiteratorを返す

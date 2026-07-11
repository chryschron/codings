# 1回目
O(N)なDPを素朴に実装する

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        vector<int> subarray_sums(nums.size());
        subarray_sums[0] = nums[0];
        for (int i = 1; i < nums.size(); i++) {
            if (subarray_sums[i - 1] < 0) {
                subarray_sums[i] = nums[i];
            } else {
                subarray_sums[i] = subarray_sums[i - 1] + nums[i];
            }
        }
        return *max_element(
            subarray_sums.begin(),
            subarray_sums.end()
        );
    }
};
```

## memo
- これまでの要素が作る部分列が正の値なら活用できる、負なら必要ない(合計値が減るだけ)
- 要素数Nとして時間計算量O(N)、空間計算量O(N)
	- `N <= 10^5`なので高々10μs, 100KBオーダー
- maxを保有しておけば空間計算量をO(1)にできる

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        int subarray_sum = nums[0];
        int maxsum = subarray_sum;
        for (int i = 1; i < nums.size(); i++) {
            subarray_sum = max(subarray_sum + nums[i], nums[i]);
            maxsum = max(maxsum, subarray_sum);
        }
        return maxsum;
    }
};
```

# 2回目
Divide and Conquerを活用する

```c++
class Solution {
private:
    int maxCrossingSum(span<int> nums) {
        int middle_idx = nums.size() / 2;

        int left_maxsum = INT_MIN;
        int left_sum = 0;
        for (int i = middle_idx - 1; 0 <= i; i--) {
            left_sum += nums[i];
            left_maxsum = max(left_maxsum, left_sum);
        }

        int right_maxsum = INT_MIN;
        int right_sum = 0;
        for (int i = middle_idx; i < nums.size(); i++) {
            right_sum += nums[i];
            right_maxsum = max(right_maxsum, right_sum);
        }

        return left_maxsum + right_maxsum;
    }
    int maxSubArrayDivAndConq(span<int> nums) {
        if (nums.size() == 1) {
            return nums[0];
        }

        int left_sum = maxSubArrayDivAndConq(nums.subspan(
            0, nums.size() / 2
        ));
        int right_sum = maxSubArrayDivAndConq(nums.subspan(
            0 + nums.size() / 2, dynamic_extent
        ));
        int crossing_sum = maxCrossingSum(nums);

        return max({left_sum, right_sum, crossing_sum});
    }
public:
    int maxSubArray(vector<int>& nums) {
        return maxSubArrayDivAndConq(nums);
    }
};
```

## memo
- 時間計算量O(NlogN)、空間計算量O(logN)
	- `maxCrossingSum`でO(N)、`maxSubArrayDivAndConq`でO(logN)
	- `log_2 10^5≈17`なので大体100μsオーダー
	- `span<>`の使用によってcall stack以外のメモリ使用は定数倍に抑えられる
		- `N <= 10^5`なのでワーストでも10^1オーダー

- iterative
	- 分からなかったのでLLMの助けを借りた
	- recursiveな方でもNが2^10^5(=(2^10)^10^4≈(10^3)^10^4=10^30000)レベル以上にならないとstack overflowしない上、その場合は配列が(ディスク上であっても)収まる訳ない
		- 参考: 観測可能な宇宙上の全原子数: 10^80
		- 実装も大変なのでこれを書ける必要は全くなさそう

```c++
class Solution {
private:
    int maxCrossingSum(span<int> nums) {
        int middle_idx = nums.size() / 2;

        int left_maxsum = INT_MIN;
        int left_sum = 0;
        for (int i = middle_idx - 1; 0 <= i; i--) {
            left_sum += nums[i];
            left_maxsum = max(left_maxsum, left_sum);
        }

        int right_maxsum = INT_MIN;
        int right_sum = 0;
        for (int i = middle_idx; i < nums.size(); i++) {
            right_sum += nums[i];
            right_maxsum = max(right_maxsum, right_sum);
        }

        return left_maxsum + right_maxsum;
    }
    enum Stage {
        Left,
        Right,
        CompareSum
    };
    struct StackFrame {
        span<int> nums;
        enum Stage stage;
        int left_sum;
        int right_sum;
    };
public:
    int maxSubArray(vector<int>& nums) {
        stack<StackFrame> stack;
        stack.push({nums, Stage::Left, 0, 0});
        int maxsum = 0;

        while (!stack.empty()) {
            auto& top = stack.top();
            if (top.nums.size() == 1) {
                maxsum = top.nums[0];
                stack.pop();
                
                if (!stack.empty()) {
                    if (stack.top().stage == Stage::Right) {
                        stack.top().left_sum = maxsum;
                    } else {
                        stack.top().right_sum = maxsum;
                    }
                }
                continue;
            }

            if (top.stage == Stage::Left) {
                top.stage = Stage::Right;
                stack.push({
                    top.nums.subspan(0, top.nums.size() / 2), Stage::Left, 0, 0
                });
            } 
            else if (top.stage == Stage::Right) {
                top.stage = Stage::CompareSum;
                stack.push({
                    top.nums.subspan(top.nums.size() / 2), Stage::Left, 0, 0
                });
            } 
            else {
                int crossing_sum = maxCrossingSum(top.nums);
                maxsum = max({top.left_sum, top.right_sum, crossing_sum});
                
                stack.pop();

                if (!stack.empty()) {
                    if (stack.top().stage == Stage::Right) {
                        stack.top().left_sum = maxsum;
                    } else {
                        stack.top().right_sum = maxsum;
                    }
                }
            }
        }
        return maxsum;
    }
};
```

# 1回目
侵入する家を`x`、しない家を`-`としたら`x-x`、`x--x`という間隔のとり方しかない(それ以上ならもっと侵入できる)ので、3要素前まで遡って最大値を求めていけばO(N)で解けそう

```c++
class Solution {
public:
    int rob(const vector<int>& nums) {
        if (nums.size() == 1) {
            return nums[0];
        } else if (nums.size() == 2) {
            return max(nums[0], nums[1]);
        }

        vector<int> maximum_money(nums.size());
        for (int i = 0; i < 2; i++) {
            maximum_money[i] = nums[i];
        }
        maximum_money[2] = max(nums[0] + nums[2], nums[1]);
        for (int i = 3; i < nums.size(); i++) {
            maximum_money[i] = max({
                maximum_money[i - 3] + nums[i],
                maximum_money[i - 2] + nums[i],
                maximum_money[i - 1]
            });
        }
        return maximum_money[nums.size() - 1];
    }
};
```

## memo
- `nums`の配列長をNとして時間計算量O(N)、空間計算量O(N)
	- `N <= 100`ゆえ100nsオーダー

# 2回目
変数を予め用意しておけば場合分けの必要がなくなるのと空間計算量をO(1)にできる

```c++
class Solution {
public:
    int rob(const vector<int>& nums) {
        int prev1_sum = 0;
        int prev2_sum = 0;
	int max_money = 0;
        for (const int num : nums) {
            max_money = max(prev1_sum, prev2_sum + num);
            prev2_sum = prev1_sum;
            prev1_sum = max_money;
        }
        return max_money;
    }
};
```

## memo
- これでうまく行く理由 (例: `[100, 1, 1, 100]`)
	- 2個前の大きな数は`prev2_sum`側に保管される
	- `prev1_sum == 0, prev2_sum + num == 100`
		- `prev2_sum == 0, prev1_sum == 100`
	- `prev1_sum == 100, prev2_sum + num == 1`
		- `prev2_sum == 100, prev1_sum == 100`
	- `prev1_sum == 100, prev2_sum + num == 101`
		- `prev2_sum == 100, prev1_sum == 101`
	- `prev1_sum == 101, prev2_sum + num == 200`
		- `max_money == 200`

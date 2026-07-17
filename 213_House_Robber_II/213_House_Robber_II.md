# 1回目
最初の家に入るか入らないかで分けられそう

```c++
class Solution {
private:
    int maxMoneyRobbingFirst(const vector<int>& nums) const {
        if (nums.size() == 1) {
            return nums[0];
        }
        
        int prev1 = 0;
        int prev2 = 0;
        int max_money = 0;
        for (int i = 0; i < nums.size() - 1; i++) {
            max_money = max(prev1, prev2 + nums[i]);
            prev2 = prev1;
            prev1 = max_money;
        }
        return max_money;
    }
    int maxMoneyNotRobbingFirst(const vector<int>& nums) const {
        int prev1 = 0;
        int prev2 = 0;
        int max_money = 0;
        for (int i = 1; i < nums.size(); i++) {
            max_money = max(prev1, prev2 + nums[i]);
            prev2 = prev1;
            prev1 = max_money;
        }
        return max_money;
    }
public:
    int rob(const vector<int>& nums) {
        return max(
            maxMoneyRobbingFirst(nums),
            maxMoneyNotRobbingFirst(nums)
        );
    }
};
```

## memo
- `nums`の要素数をNとして時間計算量O(N)、空間計算量O(1)
	- `N <= 100`なので高々100μsオーダー

# 2回目
共通化できる

```c++
class Solution {
private:
    int robWithRange(const vector<int>& nums, int begin, int end) const {
        int prev1 = 0;
        int prev2 = 0;
        int max_money = 0;
        for (int i = begin; i < end; i++) {
            max_money = max(prev1, prev2 + nums[i]);
            prev2 = prev1;
            prev1 = max_money;
        }
        return max_money;
    }
public:
    int rob(const vector<int>& nums) {
        if (nums.size() == 1) {
            return nums[0];
        }
        return max(
            robWithRange(nums, 0, nums.size() - 1),
            robWithRange(nums, 1, nums.size())
        );
    }
};
```

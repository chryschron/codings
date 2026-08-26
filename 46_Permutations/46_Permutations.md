# 1回目
`N <= 6`なので愚直に解いて良さそう

```c++
class Solution {
public:
    vector<vector<int>> permute(const vector<int>& nums) {
        if (nums.empty()) {
            return {{}};
        }

        vector<int> nextLevel(nums.begin() + 1, nums.end());
        vector<vector<int>> nextLevelPermuted = permute(nextLevel);
        vector<vector<int>> res;
        for (const auto& arr : nextLevelPermuted) {
            for (int i = 0; i < nums.size(); i++) {
                vector<int> tmp = arr;
                tmp.insert(tmp.begin() + i, nums[0]);
                res.push_back(tmp);
            }
        }
        return res;
    }
};
```

## memo
- 時間計算量O(N^2 * N!)、空間計算量O(N * N!)
	- `insert`にns掛かるとして高々10μsオーダー

# 2回目
back tracking(DFS)で解く

```c++
class Solution {
private:
    void backtrackPermute(vector<int>& vec, int start, vector<vector<int>>& result) const {
        if (start == vec.size()) {
            result.push_back(vec);
            return;
        }

        for (int i = start; i < vec.size(); i++) {
            swap(vec[start], vec[i]);
            backtrackPermute(vec, start + 1, result);
            swap(vec[start], vec[i]);
        }
    }
public:
    vector<vector<int>> permute(const vector<int>& nums) {
        vector<vector<int>> result;
        vector<int> numsCopy(nums);

        backtrackPermute(numsCopy, 0, result);
        return result;
    }
};
```

## memo
- 時間計算量O(N * N!)
	- 高々10μsオーダー程度
- 引数の`nums`が書き換わるのが気持ち悪いのでコピーを作るようにした

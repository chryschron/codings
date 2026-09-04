# 1回目
backtrackingを使って解いた

```c++
class Solution {
private:
    void generateSubsets(vector<vector<int>>& subsets, int index, vector<int>& subset, const vector<int>& nums) const {
        if (index == nums.size()) {
            return;
        }

        generateSubsets(subsets, index + 1, subset, nums);
        subset.push_back(nums[index]);
        subsets.push_back(subset);
        generateSubsets(subsets, index + 1, subset, nums);
        subset.pop_back();
    }
public:
    vector<vector<int>> subsets(const vector<int>& nums) {
        vector<int> subset{};
        vector<vector<int>> subsets{{}};
        generateSubsets(subsets, 0, subset, nums);
        return subsets;
    }
};
```

## memo
- 時間計算量O(2^N)、空間計算量O(2^N)
	- `N <= 10`なので高々2*10ステップ、maxで10msオーダー以内に収まる
	- 再帰はマックスN回なのでstack overflowの心配もない

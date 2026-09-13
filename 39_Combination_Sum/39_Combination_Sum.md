# 1回目
Back trackingで解いた

```c++
class Solution {
private:
    void buildCombinations(const vector<int>& candidates, int index, int target, vector<vector<int>>& combinations, vector<int>& combination) const {
        if (target < 0 || index >= candidates.size()) {
            return;
        }

        if (target == 0) {
            combinations.push_back(combination);
            return;
        }

        buildCombinations(
            candidates, index + 1,
            target, combinations, combination
        );

        combination.push_back(candidates[index]);
        target -= candidates[index];
        buildCombinations(
            candidates, index,
            target, combinations, combination
        );
        combination.pop_back();
    }
public:
    vector<vector<int>> combinationSum(const vector<int>& candidates, int target) {
        vector<int> combination;
        vector<vector<int>> combinations;

        buildCombinations(candidates, 0, target, combinations, combination);
        return combinations;
    }
};
```

## memo
- `candidates`の要素数をL、要素の最小値をM、`target`の値をNとして時間計算量はO(L^(N/M))、空間計算量はO(N/M)
	- `L <= 30, M = 2, N <= 40`ゆえワースト30^20ステップ(?)
		- TLEしそうだけれどなぜ大丈夫なのだろうか
		- 現実的なボトルネックが`combinations.push_back(combination);`なのと`target < 0`で早めに弾いているから(?)

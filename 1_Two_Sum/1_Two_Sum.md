# 1回目
Trivialに解く

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        for (int i = 0; i < nums.size() - 1; i++) {
            for (int j = i + 1; j < nums.size(); j++)
                if (nums[i] + nums[j] == target)
                return {i, j};
        }
        return {-1, -1};
    }
};
```

## memo
- `nums.length = N`として時間計算量O(N^2)、メモリO(1)
	- `nums.length`の最大値は10^4なので処理回数は大体10^8回、C++なら10^9-10ステップ/s位なので最悪ケースでも1秒以内で処理は終わる

# 2回目
HashMapを使ってそれまでに見たことがある数値を保持すればO(1)で検索可能

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> num_index;
        for (int i = 0; i < nums.size(); i++) {
            if (auto hit = num_index.find(target - nums[i]); hit != num_index.end())
                return {i, hit->second};
            
            num_index[nums[i]] = i;
        }
        return {-1, -1};
    }
};
```

## memo
- 時間計算量O(N)、メモリO(N)
	- intのハッシュ計算なら高々数ステップで済む。仮に10^1オーダーとしても最悪ステップ数は高々10^5
	- ただしハッシュ衝突が多発すると検索がO(N)
	- `std::map`で最悪O(log N)を保証するのも選択肢に入るかも？

# 3回目
メモリ領域をreserveしておくとリハッシュ処理が不要になるが、メモリが常にワーストだけ食うようになる

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        unordered_map<int, int> num_index;
        num_index.reserve(nums.size());
        for (int i = 0; i < nums.size(); i++) {
            if (auto hit = num_index.find(target - nums[i]); hit != num_index.end())
                return {i, hit->second};
            num_index[nums[i]] = i;
        }
        return {-1, -1};
    }
};
```

# 1回目
二分探索なので再帰して問題なさそう

```c++
class Solution {
private:
    int searchInsertWithRange(const vector<int>& nums, int begin_idx, int end_idx, int target) const {
        if (begin_idx == end_idx) {
            return begin_idx;
        }

        int mid_idx = (begin_idx + end_idx) / 2;
        if (target == nums[mid_idx]) {
            return mid_idx;
        } else if (target > nums[mid_idx]) {
            return searchInsertWithRange(
                nums, mid_idx + 1, end_idx, target
            );
        } else {
            return searchInsertWithRange(
                nums, 0, mid_idx, target
            );
        }
    }
public:
    int searchInsert(const vector<int>& nums, int target) {
        if (target > nums.back()) {
            return nums.size();
        }

        return searchInsertWithRange(
            nums, 0, nums.size() - 1, target
        );
    }
};
```

## memo
- `nums`の要素数をNとして時間計算量O(logN)、空間計算量O(logN)
	- `N <= 10^4 (≈2^10*10)`なので高々14回の再帰、10nsオーダー

# 2回目
iterativeに解く。記憶が必要ないのでstackを使わずに実装できる。

```c++
class Solution {
public:
    int searchInsert(const vector<int>& nums, int target) {
        if (target > nums.back()) {
            return nums.size();
        }

        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = (begin + end) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] < target) {
                begin = mid + 1;
            } else {
                end = mid;
            }
        }
        return begin;
    }
};
```

## memo
- 空間計算量がO(1)になる
- 配列内に`target`が複数ある場合を考える
	- 今回の解法ではどこに入るかわからない
- 左端に入れるパターン(lower_bound)
```c++
class Solution {
public:
    int searchInsert(const vector<int>& nums, int target) {
        if (target > nums.back()) {
            return nums.size();
        }

        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = (begin + end) / 2;
            if (nums[mid] < target) {
                begin = mid + 1;
            } else {
                end = mid;
            }
        }
        return begin;
    }
};
```

- 右端に入れるパターン(upper_bound)
	- 今回の条件ではWAになる
```c++
class Solution {
public:
    int searchInsert(const vector<int>& nums, int target) {
        if (target > nums.back()) {
            return nums.size();
        }

        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = (begin + end) / 2;
            if (nums[mid] <= target) {
                begin = mid + 1;
            } else {
                end = mid;
            }
        }
        return begin;
    }
};
```

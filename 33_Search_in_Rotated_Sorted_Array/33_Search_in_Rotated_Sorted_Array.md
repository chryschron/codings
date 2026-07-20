# 1回目
二分したうちの少なくとも一方は増加数列という性質を使えば良さそう

```c++
class Solution {
public:
    int search(const vector<int>& nums, int target) {
        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = (begin + end) / 2;
            if (nums[begin] <= nums[mid]) {
                if (nums[begin] <= target && target <= nums[mid]) {
                    end = mid;
                } else {
                    begin = mid + 1;
                }
            } else {
                if (nums[mid] <= target && target <= nums[end]) {
                    begin = mid;
                } else {
                    end = mid - 1;
                }
            }
        }
        return nums[begin] == target ? begin : -1;
    }
};
```

## memo
- `nums`の要素数をNとして時間計算量O(logN)、空間計算量O(1)
	- `N <= 5000`なので高々13ステップ、10nsオーダー
- 重複がある場合、一番左の要素を指す(lower_bound)


# 2回目
せっかくなので作ったupper_bound (WAになる)

```c++
class Solution {
public:
    int search(const vector<int>& nums, int target) {
        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = (begin + end) / 2;
            if (nums[begin] <= nums[mid]) {
                if (nums[begin] <= target && target < nums[mid]) {
                    end = mid;
                } else {
                    begin = mid + 1;
                }
            } else {
                if (nums[mid] <= target && target < nums[end]) {
                    begin = mid + 1;
                } else {
                    end = mid;
                }
            }
        }
        return begin;
    }
};
```

## memo
- 次の要素を指すように適当な比較を用いる

# 1回目
Search in Rotated Sorted Arrayの応用で解ける

```c++
class Solution {
public:
    int findMin(const vector<int>& nums) {
        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = (begin + end) / 2;
            if (nums[begin] <= nums[mid]) {
                if (nums[mid] <= nums[end]) {
                    return nums[begin];
                } else {
                    begin = mid + 1;
                }
            } else {
                end = mid;
            }
        }
        return nums[begin];
    }
};
```

## memo
- `nums`の要素数をNとして時間計算量O(logN)、空間計算量O(1)
	- `N <= 5000`なので高々13回で見つかる、10nsオーダー
- `begin <= mid`じゃないなら`[begin, mid]`内に存在、そうでないなら`mid <= end`かどうか見る

# 2回目
改善した

```c++
class Solution {
public:
    int findMin(const vector<int>& nums) {
        int begin = 0;
        int end = nums.size() - 1;
        while (begin < end) {
            int mid = begin + (end - begin) / 2;
            if (nums[mid] > nums[end]) {
                begin = mid + 1;
            } else {
                end = mid;
            }
        }
        return nums[begin];
    }
};
```

## memo
- 改善点
	- `(begin + end) > 2^31 - 1`の時のoverflow防止
	- ロジックの簡素化

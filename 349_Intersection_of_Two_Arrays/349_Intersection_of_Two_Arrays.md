# 1回目
制約条件見る限り愚直に実装してよさそう

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> nums1_set;
        for (const int num : nums1) {
            nums1_set.insert(num);
        }

        unordered_set<int> nums2_set;
        for (const int num : nums2) {
            nums2_set.insert(num);
        }

        vector<int> intersection;
        for (const int num : nums1_set) {
            if (nums2_set.contains(num)) {
                intersection.push_back(num);
            }
        }

        return intersection;
    }
};
```

## memo
- `N = max(nums1.length, nums2.length)`として計算量O(N)
	- ハッシュ衝突しまくるとO(N^2)
	- 最悪でも1000要素、ワーストでも1000 * 1000 = 10^6オーダーステップなのでC++なら問題ない
- メモリO(N)

# 2回目
`erase()`を用いれば`unordered_set`は1個で十分なのと、ちょっとしたリファクタリングを施した

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> nums1_set(nums1.begin(), nums1.end());

        vector<int> intersection;
        for (const int num : nums2) {
            if (nums1_set.erase(num)) {
                intersection.push_back(num);
            }
        }

        return intersection;
    }
};
```

## memo
- 理論的な計算量は変わらないが、余分なステップが減る分更に高速

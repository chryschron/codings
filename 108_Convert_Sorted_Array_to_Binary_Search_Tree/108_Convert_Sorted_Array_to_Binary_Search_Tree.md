# 1回目
真ん中をルートにして左をleft、右をrightに格納すれば良い

```c++
class Solution {
private:
    TreeNode* sortedSubArrayToBST(const vector<int>& nums, const int begin, const int end) const {
        if (begin > end) {
            return nullptr;
        }

        int middle = (begin + end) / 2;
        TreeNode* root =  new TreeNode(
            nums[middle],
            sortedSubArrayToBST(nums, begin, middle - 1),
            sortedSubArrayToBST(nums, middle + 1, end)
        );
        return root;
    }
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return sortedSubArrayToBST(nums, 0, nums.size() - 1);
    }
};
```

## memo
- Nを配列の要素数として計算量O(N)、メモリO(N)
	- `N <= 10^4`で関数の再帰的呼び出しが律速としても高々μsオーダー
- `begin + 1 == end`の時は`begin`が`root`になって`begin > middle - 1`、`begin == end`の時は`begin > middle - 1 && middle + 1 > end`なので上手く行く

# 2回目
iterativeに解く

```c++
class Solution {
public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        TreeNode* root = nullptr;
        stack<tuple<TreeNode**, int, int>> node_subarray({{&root, 0, nums.size() - 1}});
        while (!node_subarray.empty()) {
            auto [node_ptr, begin, end] = node_subarray.top();
            node_subarray.pop();

            if (begin > end) {
                *node_ptr = nullptr;
                continue;
            }
            int middle = (begin + end) / 2;
            *node_ptr = new TreeNode(nums[middle]);
            node_subarray.push({&((*node_ptr)->right), middle + 1, end});
            node_subarray.push({&((*node_ptr)->left), begin, middle - 1});
        }
        return root;
    }
};
```

## memo
- ポインタの先が`nullptr`かもしれないので`TreeNode**`を保有するようにした
- 計算量とメモリは変化なし
- `std::span`
	- 配列の部分列を保持。コピーが発生しないので高速
	- 一回目のやつで`begin` `end`を引数に入れる手間が省ける

```c++
class Solution {
private:
    TreeNode* sortedSpanToBST(span<int> nums) const {
        if (nums.empty()) {
            return nullptr;
        }

        int middle = nums.size() / 2;
        TreeNode* root = new TreeNode(
            nums[middle],
            sortedSpanToBST(nums.subspan(0, middle)),
            sortedSpanToBST(nums.subspan(middle + 1))
        );
        
        return root;
    }

public:
    TreeNode* sortedArrayToBST(vector<int>& nums) {
        return sortedSpanToBST(nums);
    }
};
```

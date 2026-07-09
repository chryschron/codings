# 1回目
再帰でroot->left->rightの順に処理していけば良さそうなのは分かったがpreorderのspanを取る処理で詰まったのでカンニングしながら進めた。
(個人的に、今まで解いた中ではかなり難しいと感じた)

```c++
class Solution {
private:
    int searchIndex(span<int> vec, int target) const {
        auto it = find(vec.begin(), vec.end(), target);
        if (it == vec.end()) {
            return -1;
        }
        return distance(vec.begin(), it);
    }
    TreeNode* buildSubTree(span<int> pre, span<int> in) const {
        if (pre.empty() || in.empty()) {
            return nullptr;
        }

        TreeNode* root = new TreeNode(pre[0]);
        int root_idx = searchIndex(in, pre[0]);

        int left_size = root_idx;
        int right_size = in.size() - root_idx - 1;

        root->left = buildSubTree(pre.subspan(1, left_size), in.first(left_size));
        root->right = buildSubTree(pre.subspan(1 + left_size, right_size), in.last(right_size));
        return root;
    }
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        return buildSubTree(preorder, inorder);
    }
};
```

## memo
- Nをノード数として時間計算量O(N^2)、空間計算量O(N)
	- `root_idx`のlookupでO(N)なのが痛い
		- Hashmapを使うべきだった
- `N <= 3000`なので1-10msオーダー
	- stack overflowの心配もない

# 2回目
hashmapを使って書く

```c++
class Solution {
private:
    TreeNode* buildSubTree(span<int> pre, span<int> in, int in_start, unordered_map<int, int>& in_val_idx) const {
        if (pre.empty() || in.empty()) {
            return nullptr;
        }

        TreeNode* root = new TreeNode(pre[0]);
        int root_idx = in_val_idx.find(pre[0])->second - in_start;

        int left_size = root_idx;
        int right_size = in.size() - root_idx - 1;

        root->left = buildSubTree(
            pre.subspan(1, left_size),
            in.first(left_size),
            in_start,
            in_val_idx
        );
        root->right = buildSubTree(
            pre.subspan(1 + left_size, right_size),
            in.last(right_size),
            in_start + left_size + 1,
            in_val_idx
        );
        return root;
    }
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        unordered_map<int, int> in_val_idx;
        for (int i = 0; i < inorder.size(); i++) {
            in_val_idx[inorder[i]] = i;
        }
        return buildSubTree(preorder, inorder, 0, in_val_idx);
    }
};
```

## memo
- `in_val_idx->second`に入るのはspanで切り出す前のインデックスなので、spanのオフセットも持つ必要がある

- iterative
```c++
class Solution {
public:
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        unordered_map<int, int> in_val_idx;
        for (int i = 0; i < inorder.size(); i++) {
            in_val_idx[inorder[i]] = i;
        }

        TreeNode* root = nullptr;
        stack<tuple<TreeNode**, span<int>, span<int>, int>> node_pre_in_inbegin;
        node_pre_in_inbegin.push(({&root, preorder, inorder, 0});
        while (!node_pre_in_inbegin.empty()) {
            auto [node, pre, in, in_begin] = node_pre_in_inbegin.top();
            node_pre_in_inbegin.pop();

            if (pre.empty() || in.empty()) {
                continue;
            }
            *node = new TreeNode(pre[0]);
            int root_idx = in_val_idx.find(pre[0])->second - in_begin;

            int left_size = root_idx;
            int right_size = in.size() - root_idx - 1;

            node_pre_in_inbegin.push({
                &((*node)->right),
                pre.subspan(1 + left_size, right_size),
                in.last(right_size),
                in_begin + left_size + 1
            });
            node_pre_in_inbegin.push({
                &((*node)->left),
                pre.subspan(1, left_size),
                in.first(left_size),
                in_begin
            });
        }
        return root;
    }
};
```

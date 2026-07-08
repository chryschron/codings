# 1回目
分からなかったので調べたところ、InOrderで走査して値が単調増加していれば良いことが分かった。

```c++
class Solution {
private:
    bool inOrderValidation(TreeNode* node, int64_t *prev_val) const {
        if (node == nullptr) {
            return true;
        }

        if (!inOrderValidation(node->left, prev_val)) {
            return false;
        }

        if (*prev_val >= node->val) {
            return false;
        }
        *prev_val = node->val;
        return inOrderValidation(node->right, prev_val);
    }
public:
    bool isValidBST(TreeNode* root) {
        int64_t min = INT64_MIN;
        return inOrderValidation(root, &min);
    }
};
```

## memo
- ノード数Nとして計算量O(N)、メモリワーストO(N)
	- 最悪は`木の高さ == ノード数`
	- 関数呼び出し律速としてμsオーダー
- 値が`[INT32_MIN, INT32_MAX]`なので`int64_t`を用いた
	- `int`(64bitマシンでは32bit)の場合、一番左下のノードが`INT32_MIN`だとバグる
	- どうせ共有のために64bitポインタを使うので、速度面は心配なさそう
- iterativeに解こう

```c++
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        int64_t prev_val = INT64_MIN;
        stack<TreeNode*> inorder_nodes({root});

        while (!inorder_nodes.empty()) {
            TreeNode* node = inorder_nodes.top();
            while (node != nullptr) {
                inorder_nodes.push(node->left);
                node = node->left;
            }
            inorder_nodes.pop();
            if (inorder_nodes.empty()) {
                return true;
            }

            node = inorder_nodes.top();
            inorder_nodes.pop();
            if (prev_val >= node->val) {
                return false;
            }
            prev_val = node->val;
            inorder_nodes.push(node->right);
        }
        return true;
    }
};
```

# 2回目
PreOrderでも解ける

recursive
```c++
class Solution {
private:
    bool preOrderValidation(TreeNode* node, int64_t min, int64_t max) const {
        if (node == nullptr) {
            return true;
        } else if (!(min < node->val && node->val < max)) {
            return false;
        }

        return preOrderValidation(node->left, min, node->val) &&
               preOrderValidation(node->right, node->val, max);
    }
public:
    bool isValidBST(TreeNode* root) {
        return preOrderValidation(root, INT64_MIN, INT64_MAX);
    }
};
```

iterative
```c++
class Solution {
public:
    bool isValidBST(TreeNode* root) {
        stack<tuple<TreeNode*, int64_t, int64_t>> node_min_max;
        node_min_max.push({root, INT64_MIN, INT64_MAX});

        while (!node_min_max.empty()) {
            auto [node, min, max] = node_min_max.top();
            node_min_max.pop();

            if (node == nullptr) {
                continue;
            } else if (!(min < node->val && node->val < max)) {
                return false;
            }

            node_min_max.push({node->right, node->val, max});
            node_min_max.push({node->left, min, node->val});
        }
        return true;
    }
};
```

## memo
- 理論的な計算量は変わらない
	- ただ`min` `max`を両方保有しないといけないので少し悪い
- 木の右側で規則が破れている場合はこっちのほうが速い

# 3回目
別解

- InOrderでvectorに値を並べて確認する
```c++
class Solution {
private:
    void inOrderTraverse(TreeNode* node, vector<int>& vec) const {
        if (node == nullptr) {
            return;
        }
        inOrderTraverse(node->left, vec);
        vec.push_back(node->val);
        inOrderTraverse(node->right, vec);
    }
    bool isVectorSorted(vector<int>& vec) const {
        for (int i = 0; i < vec.size() - 1; i++) {
            if (!(vec[i] < vec[i + 1])) {
                return false;
            }
        }
        return true;
    }
public:
    bool isValidBST(TreeNode* root) {
        vector<int> inorder;

        inOrderTraverse(root, inorder);
        return isVectorSorted(inorder);
    }
};
```

- 計算量O(N)(正確には2N)、メモリは常にO(N)
	- 直感的ではある

- Morris Traversal: O(1)でInOrderする
```c++
class Solution {
private:
public:
    bool isValidBST(TreeNode* root) {
        TreeNode* runner = root;
        int64_t prev_val = INT64_MIN;
        bool is_valid = true;
        while (runner != nullptr) {
            if (runner->left == nullptr) {
                if (prev_val >= runner->val) {
                    is_valid = false;
                }
                prev_val = runner->val;
                runner = runner->right;
                continue;
            }
            
            TreeNode *last_node = runner->left;
            while (last_node->right != nullptr &&
                   last_node->right != runner) {
                last_node = last_node->right;
            }
            if (last_node->right == nullptr) {
                last_node->right = runner;
                runner = runner->left;
            } else {
                last_node->right = nullptr;
                if (prev_val >= runner->val) {
                    is_valid = false;
                }
                prev_val = runner->val;
                runner = runner->right;
            }
        }
        return is_valid;
    }
};
```

- 部分木で一番最後に巡回するノードの`right`は必ず`nullptr`なので、そこに親ノードの情報を入れてしまえという発想
- 計算量O(N)、メモリO(1)
	- 復元が必要な場合は全ノード探査が必要
	- 一時的に木が書き換わるので木の所有権が完全に渡っていない場合は使ってはいけない

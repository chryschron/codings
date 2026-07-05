# 1回目
再帰で解けそう。
非破壊で解こうと思ったが返り値が生ポインタなので注意する

```c++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        TreeNode* new_root = new TreeNode;
        if (root1 != nullptr && root2 != nullptr) {
            new_root->val = root1->val + root2->val;
            new_root->left = mergeTrees(root1->left, root2->left);
            new_root->right = mergeTrees(root1->right, root2->right);
        } else if (root1 != nullptr) {
            new_root->val = root1->val;
            new_root->left = mergeTrees(root1->left, nullptr);
            new_root->right = mergeTrees(root1->right, nullptr);
        } else if (root2 != nullptr) {
            new_root->val = root2->val;
            new_root->left = mergeTrees(nullptr, root2->left);
            new_root->right = mergeTrees(nullptr, root2->right);
        } else {
            delete new_root;
            return nullptr;
        }
        return new_root;
    }
};
```

一応`unique_ptr`バージョン
```c++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        unique_ptr<TreeNode> new_root = nullptr;
        if (root1 != nullptr && root2 != nullptr) {
            new_root = make_unique<TreeNode>(root1->val + root2->val, mergeTrees(root1->left, root2->left), mergeTrees(root1->right, root2->right));
        } else if (root1 != nullptr) {
            new_root = make_unique<TreeNode>(root1->val, mergeTrees(root1->left, nullptr), mergeTrees(root1->right, nullptr));
        } else if (root2 != nullptr) {
            new_root = make_unique<TreeNode>(root2->val, mergeTrees(nullptr, root2->left), mergeTrees(nullptr, root2->right));
        }
        return new_root.release();
    }
};
```

## memo
- Nをノード数として時間計算量O(N)、メモリO(N)
	- `make_unique()`を律速(10^1-2ns)としてワーストで4000 * 100 = 4 * 10^5ns(=10μsオーダー)程度?
		- 実際: 7ms
	- メモリは高々KBオーダー消費なのでヒープアロケーションがボトルネックになっていることはなさそう
- `std::unique_ptr`について
	- 所有権を唯一持つことが保証されたポインタ
		- 今回わざわざ使うメリットはない

- 安全にfreeする関数
```c++
void freeTree(Tree* node) {
        if (node == nullptr) {
            return;
        }
        freeTree(node->left);
        freeTree(node->right);
        delete node;
}
```

- せっかくなので`unique_ptr`バージョン
```c++
class Solution {
public:
    unique_ptr<TreeNode> mergeTrees(const unique_ptr<TreeNode>& root1, const unique_ptr<TreeNode>& root2) {
        unique_ptr<TreeNode> new_root = nullptr;
        if (root1 != nullptr && root2 != nullptr) {
            new_root = make_unique<TreeNode>(root1->val + root2->val, mergeTrees(root1->left, root2->left), mergeTrees(root1->right, root2->right));
        } else if (root1 != nullptr) {
            new_root = make_unique<TreeNode>(root1->val, mergeTrees(root1->left, nullptr), mergeTrees(root1->right, nullptr));
        } else if (root2 != nullptr) {
            new_root = make_unique<TreeNode>(root2->val, mergeTrees(nullptr, root2->left), mergeTrees(nullptr, root2->right));
        }
        return new_root;
    }
};
```

# 2回目
破壊ありの場合

```c++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if (root1 == nullptr) {
            return root2;
        } else if (root2 == nullptr) {
            return root1;
        }

        root1->val += root2->val;
        root1->left = mergeTrees(root1->left, root2->left);
        root1->right = mergeTrees(root1->right, root2->right);
        return root1;
    }
};
```

## memo
- 理論的な計算量は変わらないが、メモリ関連の処理が不要なので速い
	- 関数呼び出しを律速としてもμsオーダー
- iterative

```c++
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if (root1 == nullptr) {
            return root2;
        } else if (root2 == nullptr) {
            return root1;
        }

        stack<pair<TreeNode*, TreeNode*>> stack({{root1, root2}});
        while (!stack.empty()) {
            auto [node1, node2] = stack.top();
            stack.pop();

            node1->val += node2->val;
            if (node1->left == nullptr) {
                node1->left = node2->left;
            } else if (node2->left != nullptr) {
                stack.push({node1->left, node2->left});
            }
            if (node1->right == nullptr) {
                node1->right = node2->right;
            } else if (node2->right != nullptr) {
                stack.push({node1->right, node2->right});
            }
        }
        return root1;
    }
};
```

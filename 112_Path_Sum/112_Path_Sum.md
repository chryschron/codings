# 1回目
ノード数が十分少ないのでDFSで解けば良さそう

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int target_sum) {
        if (!root) {
            return false;
        }
        
        if (root->left == nullptr &&
            root->right == nullptr) {
            return root->val == target_sum;
        } else if (hasPathSum(root->left, target_sum - root->val)) {
            return true;
        } else if (hasPathSum(root->right, target_sum - root->val)) {
            return true;
        }
        return false;
    }
};
```

## memo
- ノード数Nとして計算量O(N)、メモリO(N)
	- ワーストは`木の高さ == N`
	- `N <= 5000`なので、stack overflowは大丈夫
		- KBオーダー
	- 再帰を律速としてもμsオーダー
- iterative

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int target_sum) {
        if (!root) {
            return false;
        }

        stack<pair<TreeNode*, int>> node_sum({{root, root->val}});
        while (!node_sum.empty()) {
            auto [node, sum] = node_sum.top();
            node_sum.pop();

            if (node->left == nullptr &&
                node->right == nullptr &&
                sum == target_sum) {
                return true;
            }
            if (node->left != nullptr) {
                node_sum.push({node->left, sum + node->left->val});
            }
            if (node->right != nullptr) {
                node_sum.push({node->right, sum + node->right->val});
            }
        }
        return false;
    }
};
```

# 2回目
`stack`を`queue`にすればBFSになる

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int target_sum) {
        if (root == nullptr) {
            return false;
        }

        queue<pair<TreeNode*, int>> node_sum({{root, root->val}});
        while (!node_sum.empty()) {
            auto [node, sum] = node_sum.front();
            node_sum.pop();

            if (node->left == nullptr &&
                node->right == nullptr &&
                sum == target_sum) {
                return true;
            }
            if (node->left != nullptr) {
                node_sum.push({node->left, sum + node->left->val});
            }
            if (node->right != nullptr) {
                node_sum.push({node->right, sum + node->right->val});
            }
        }
        return false;
    }
};
```

## memo
- DFSとBFS、どっちがいいか
	- 浅いところに答えがあるならBFSが有利
	- 深い、あるいは葉が広いときはDFSの方がメモリを食わない

# 3回目
きれいにする

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int target_sum) {
        if (!root) {
            return false;
        }
        
        if (root->left == nullptr &&
            root->right == nullptr) {
            return root->val == target_sum;
        } 
        
        return hasPathSum(root->left, target_sum - root->val) ||
               hasPathSum(root->right, target_sum - root->val);
    }
};
```

```c++
class Solution {
public:
    bool hasPathSum(TreeNode* root, int target_sum) {
        stack<pair<TreeNode*, int>> node_sum({{root, 0}});
        while (!node_sum.empty()) {
            auto [node, sum] = node_sum.top();
            node_sum.pop();

            if (node == nullptr) {
                continue;
            }

            sum += node->val;
            if (node->left == nullptr &&
                node->right == nullptr &&
                sum == target_sum) {
                return true;
            }
            node_sum.push({node->left, sum});
            node_sum.push({node->right, sum});
        }
        return false;
    }
};
```

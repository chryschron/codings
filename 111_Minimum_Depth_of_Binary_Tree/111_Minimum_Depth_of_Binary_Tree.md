# 1回目
最短距離なのでBFSで解くのが良さそう

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        int depth = 0;
        if (root == nullptr) {
            return depth;
        }

        queue<TreeNode*> bfs({root});
        for (;;) {
            depth++;
            int same_depth_count = bfs.size();
            for (int i = 0; i < same_depth_count; i++) {
                TreeNode* node = bfs.front();
                bfs.pop();

                if (node->left == nullptr && node->right == nullptr) {
                    return depth;
                }
                if (node->left != nullptr) {
                    bfs.push(node->left);
                }
                if (node->right != nullptr) {
                    bfs.push(node->right);
                }
            }
        }
        return -1;
    }
};
```

## memo
- Nをノード数、Hを木の高さとして計算量O(H)、メモリワーストO(N)
	- 内側のfor文の処理がnsオーダーとして最悪10^5回処理が走るのでワースト10^5ns(=10^2μs)オーダー
		- ワーストは`H == N`
	- メモリ最悪は完全二分木で8 * 10^5 / 2 byte(=40KB)+α程度

# 2回目
DFSを使っても解ける

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        }

        int min_depth = INT_MAX;
        stack<pair<TreeNode*, int>> stack({{root, 1}});
        while (!stack.empty()) {
            for (;;) {
                auto [node, depth] = stack.top();
                stack.pop();

                if (node->left == nullptr && node->right == nullptr) {
                    min_depth = depth < min_depth ? depth : min_depth;
                    break;
                }
                if (node->left != nullptr) {
                    stack.push({node->left, depth + 1});
                }
                if (node->right != nullptr) {
                    stack.push({node->right, depth + 1});
                }
            }
        }
        return min_depth;
    }
};
```

- 計算量は常にO(N)、メモリはワーストでO(N)
	- 処理時間はBFSより長く1msオーダーだった
		- stackの処理がqueueより重い?
- recursive

```c++
class Solution {
public:
    int minDepth(TreeNode* root) {
        if (root == nullptr) {
            return 0;
        } else if (root->left == nullptr || root->right == nullptr) {
            return 1 + max(minDepth(root->left), minDepth(root->right));
        } else {
            return 1 + min(minDepth(root->left), minDepth(root->right));
        }
    }
};
```

- 片方だけ子があるときはそちらについて再帰する必要がある

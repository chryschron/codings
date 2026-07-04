# 1回目
depthは高々10^4なので再帰して大丈夫そう

```c++
class Solution {
private:
    int maxDepthInternal(TreeNode* root) {
        int left_depth = 1;
        int right_depth = 1;
        if (root->left) {
            left_depth += maxDepthInternal(root->left);
        }
        if (root->right) {
            right_depth += maxDepthInternal(root->right);
        }
        return max(left_depth, right_depth);
    }
public:
    int maxDepth(TreeNode* root) {
        if (!root) {
            return 0;
        }

        int left_depth = 1;
        int right_depth = 1;
        if (root->left) {
            left_depth += maxDepthInternal(root->left);
        }
        if (root->right) {
            right_depth += maxDepthInternal(root->right);
        }
        return max(left_depth, right_depth);
    }
};
```

## memo
- 木の要素数をN、木のdepthをHとして計算量O(N)、メモリO(H)
	- N <= 10^4なので再帰を律速としても10^4ns=1μsオーダーに収まる
	- スタック消費も高々KBオーダーなのでoverflowも心配ない

# 2回目
iterativeに解こう

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) {
            return 0;
        }

        queue<TreeNode*> node_queue({root});
        int depth = 0;
        while (!node_queue.empty()) {
            depth++;
            
            int same_depth_count = node_queue.size();
            for (int i = 0; i < same_depth_count; i++) {
                TreeNode* cur_node = node_queue.front();
                node_queue.pop();

                if (cur_node->left) {
                    node_queue.push(cur_node->left);
                }
                if (cur_node->right) {
                    node_queue.push(cur_node->right);
                }
            }
        }
        return depth;
    }
};
```

## memo
- BFSを使った
- 計算量もメモリもrecursiveの場合と同様
	- DFSを使うパターン

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) {
            return 0;
        }

        stack<pair<TreeNode*, int>> node_stack({{root, 1}});
        int max_depth = 1;
        while (!node_stack.empty()) {
            auto [node, depth] = node_stack.top();
            node_stack.pop();

            max_depth = max_depth < depth ? depth : max_depth;

            if (node->left) {
                node_stack.push({node->left, depth + 1});
            }
            if (node->right) {
                node_stack.push({node->right, depth + 1});
            }
        }
        return max_depth;
    }
};
```

- イメージ
	- DFS: 今扱っているノードについて掘り下げる: stackを使う
	- BFS: 最後に扱ったノードについて掘り下げる: queueを使う
		- DFSならPDAで扱える、BFSはTMが必須
- どっちが優れているか
	- DFS: 完全二分木等、木が横に広い時: メモリがO(log N)で済む、BFSではO(N/2)
	- BFS: 木が縦に長いとき: DFSではO(N)を引く

# 3回目
recursiveとDFSはもっとコンパクトに書ける

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) {
            return 0;
        }
        return 1 + max(maxDepth(root->left), maxDepth(root->right));
    }
};
```

```c++
class Solution {
public:
    int maxDepth(TreeNode* root) {
        if (!root) {
            return 0;
        }

        stack<pair<TreeNode*, int>> node_stack({{root, 1}});
        int max_depth = 1;
        while (!node_stack.empty()) {
            auto [node, depth] = node_stack.top();
            node_stack.pop();

            if (!node) {
                continue;
            }
            max_depth = max_depth < depth ? depth : max_depth;
            node_stack.push({node->left, depth + 1});
            node_stack.push({node->right, depth + 1});
        }
        return max_depth;
    }
};
```

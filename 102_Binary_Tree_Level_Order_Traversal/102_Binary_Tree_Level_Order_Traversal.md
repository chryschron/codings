# 1回目
深さ順に探索するBFSが相性良い

```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> same_level =  {};
        queue<TreeNode*> nodes;

        nodes.push(root);
        while (!nodes.empty()) {
            vector<int> same_level_val;
            int same_level_count = nodes.size();
            for (int i = 0; i < same_level_count; i++) {
                TreeNode* node = nodes.front();
                nodes.pop();

                if (node == nullptr) {
                    continue;
                }
                same_level_val.push_back(node->val);
                nodes.push(node->left);
                nodes.push(node->right);
            }
            if (same_level_val.size() > 0) {
                same_level.push_back(same_level_val);
            }
        }
        return same_level;
    }
};
```

## memo
- ノード数をNとして計算量O(N)、メモリワーストO(N)
	- 最悪は完全二分木
	- 要素数2000なので最悪でもμsオーダー
- DFSでも解ける。こっちは完全二分木のときにメモリO(logN)で済む
	- `same_level`で結局O(N)食べるのであまり意味はない?

# 2回目
DFS

recursive
```c++
class Solution {
private:
    void levelOrderWithDepth(TreeNode* node, int depth, vector<vector<int>>& result) {
        if (node == nullptr) {
            return;
        } else if (result.size() == depth) {
            result.push_back(vector<int>());
        }
        result[depth].push_back(node->val);
        levelOrderWithDepth(node->left, depth + 1, result);
        levelOrderWithDepth(node->right, depth + 1, result);
    }
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result = {};
        levelOrderWithDepth(root, 0, result);
        return result;
    }
};
```

iterative
```c++
class Solution {
public:
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector<vector<int>> result = {};
        stack<pair<TreeNode*, int>> node_depth({{root, 0}});
        while (!node_depth.empty()) {
            auto [node, depth] = node_depth.top();
            node_depth.pop();

            if (node == nullptr) {
                continue;
            } else if (result.size() == depth) {
                result.push_back(vector<int>());
            }
            result[depth].push_back(node->val);
            node_depth.push({node->right, depth + 1});
            node_depth.push({node->left, depth + 1});
        }
        return result;
    }
};
```

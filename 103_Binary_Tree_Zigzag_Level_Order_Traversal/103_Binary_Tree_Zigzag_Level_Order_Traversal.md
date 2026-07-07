# 1回目
同階層を左から訪問する、BFSで解くのが素直?

```c++
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        queue<TreeNode*> nodes({root});
        
        bool left_to_right = true;
        while (!nodes.empty()) {
            int same_depth_count = nodes.size();
            vector<int> same_depth_vals = {};
            for (int i = 0; i < same_depth_count; i++) {
                TreeNode* node = nodes.front();
                nodes.pop();

                if (node == nullptr) {
                    continue;
                }
                if (left_to_right) {
                    same_depth_vals.push_back(node->val);
                } else {
                    same_depth_vals.insert(same_depth_vals.begin(), node->val);
                }
                nodes.push(node->left);
                nodes.push(node->right);
            }
            if (!same_depth_vals.empty()) {
                result.push_back(move(same_depth_vals));
            }
            left_to_right = !left_to_right;
        }
        return result;
    }
};
```

# memo
- ノード数Nとして計算量O(N^2)、メモリO(N)
	- ワーストは完全二分木
- `vector.insert(begin, val)`が律速になる
	- それまでの配列の要素が全て移動する
		- `N <= 2000`なのでワースト1000個の大移動
		- KBオーダー、良くない
	- 一要素移動をnsオーダーとしてもmsオーダー
	- `deque`を使う方が良さそう

# 2回目
dequeを使う

```c++
class Solution {
public:
    vector<vector<int>> zigzagLevelOrder(TreeNode* root) {
        vector<vector<int>> result;
        queue<TreeNode*> nodes({root});
        
        bool left_to_right = true;
        while (!nodes.empty()) {
            int same_depth_count = nodes.size();
            deque<int> same_depth_vals = {};
            for (int i = 0; i < same_depth_count; i++) {
                TreeNode* node = nodes.front();
                nodes.pop();

                if (node == nullptr) {
                    continue;
                }
                if (left_to_right) {
                    same_depth_vals.push_back(node->val);
                } else {
                    same_depth_vals.push_front(node->val);
                }
                nodes.push(node->left);
                nodes.push(node->right);
            }
            if (!same_depth_vals.empty()) {
                result.push_back(vector<int>(
                    same_depth_vals.begin(),
                    same_depth_vals.end()
                ));
            }
            left_to_right = !left_to_right;
        }
        return result;
    }
};
```

## memo
- 計算量がO(N)になり、μsオーダーに収まる
	- 使用するデータ型選択はすごく大事
- `vector` vs `deque`
	- `vector`: メモリ上連続的に配置された配列
		- RAは配列の開始位置+オフセットだけで可能
		- 但し末尾以外のinsertでは後続の要素をすべて移動する必要がある
	- `deque`: 固定サイズの複数の配列+管理用の配列
		- RAでは管理用配列からさらなる参照(計2回)が必要
		- 一方先頭のinsertはO(1)
- RAが頻発かつ先頭へのinsertがないならvector、RAがあまりおこらず先頭へのinsertが度々発生するならdeque
	- ランダムな場所へのinsertが頻発する場合は双方向連結リスト(`std::list`)が良いがRAに弱い
	- 場合によってはHashsetも選択肢になるかも
- 律速は最後のdeque->vectorか
	- 変換の際にどうしても`same_depth_vals`全要素のコピーが必要
	- ワーストN/2個の移動
		- ただし、移動は配列が完成してから発生するので、総移動回数はvectorよりも少なく済む

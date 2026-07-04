# 1回目
良くなさそうな解法しか浮かばなかった

```c++
class Solution {
    vector<vector<char>> transformable_table;
private:
    bool isTransformable(string& str1, string& str2) const {
        bool is_diff_found = false;
        for (int i = 0; i < str1.size(); i++) {
            if (str1[i] != str2[i]) {
                if (is_diff_found) {
                    return false;
                }
                is_diff_found = true;
            }
        }
        return is_diff_found;
    }
    void initTransformableTable(vector<string>& word_list) {
        transformable_table.resize(word_list.size(), vector<char>(word_list.size()));

        for (int i = 0; i < word_list.size() - 1; i++) {
            for (int j = i + 1; j < word_list.size(); j++) {
                if (isTransformable(word_list[i], word_list[j])) {
                    transformable_table[i][j] = 1;
                    transformable_table[j][i] = 1;
                }
            }
        }
    }
    int ladderLengthInWordList(int index_begin, int index_end, vector<string>& word_list, unordered_set<int>& used_index) {
        int shortest = INT_MAX;
        for (int i = 0; i < word_list.size(); i++) {
            if (used_index.contains(i) || transformable_table[index_begin][i] != 1) {
                continue;
            } else if (i == index_end) {
                return 2;
            }

            used_index.insert(i);
            int length = ladderLengthInWordList(i, index_end, word_list, used_index);
            if (length != 0) {
                shortest = (length + 1) < shortest ? length + 1 : shortest;
            }
            used_index.erase(i);
        }
        return shortest != INT_MAX ? shortest : 0;
    }
public:
    int ladderLength(string begin_word, string end_word, vector<string>& word_list) {
        initTransformableTable(word_list);

        int index_end = -1;
        for (int i = 0; i < word_list.size(); i++) {
            if (word_list[i] == end_word) {
                index_end = i;
            }
        }
        if (index_end == -1) {
            return 0;
        }

        int shortest = INT_MAX;
        for (int i = 0; i < word_list.size(); i++) {
            if (!isTransformable(begin_word, word_list[i])) {
                continue;
            } else if (i == index_end) {
                return 2;
            }

            unordered_set<int> used_index;
            used_index.insert(i);
            int length = ladderLengthInWordList(i, index_end, word_list, used_index);
            if (length != 0) {
                shortest = (1 + length) < shortest ? (1 + length) : shortest;
            }
        }
        return shortest != INT_MAX ? shortest : 0;
    }
};
```

## memo
- `word_list.length = N` `word.length = L`として時間計算量O(N^2 * L + N!)、メモリO(N^2)
- `initTransformableTable()`はワーストで5000 * 5000 * 10=10^8オーダーステップ、`ladderLengthInWordList()`はワースト(=`transformable_table`が全要素1)で約5000!ステップ再帰が走る
	- 当然TLE
- アルゴリズム的にはDFS

# 2回目
最短経路探索なのでBFSを使うと良い

```c++
class Solution {
    vector<vector<char>> transformable_table;
private:
    bool isTransformable(string& str1, string& str2) const {
        bool is_diff_found = false;
        for (int i = 0; i < str1.size(); i++) {
            if (str1[i] != str2[i]) {
                if (is_diff_found) {
                    return false;
                }
                is_diff_found = true;
            }
        }
        return is_diff_found;
    }
    void initTransformableTable(vector<string>& word_list) {
        transformable_table.resize(word_list.size(), vector<char>(word_list.size()));

        for (int i = 0; i < word_list.size() - 1; i++) {
            for (int j = i + 1; j < word_list.size(); j++) {
                if (isTransformable(word_list[i], word_list[j])) {
                    transformable_table[i][j] = 1;
                    transformable_table[j][i] = 1;
                }
            }
        }
    }
    int ladderLengthInWordList(string& end_word, vector<string>& word_list, queue<pair<int, int>>& index_length, unordered_set<int>& used_indexes) {
        int length = 2;
        bool added_new_index = true;
        while (added_new_index) {
            added_new_index = false;
            while (index_length.front().second == length) {
                auto idx_len = index_length.front();
                index_length.pop();

                for (int i = 0; i < word_list.size(); i++) {
                    if (used_indexes.contains(i) || transformable_table[idx_len.first][i] != 1) {
                        continue;
                    } else if (word_list[i] == end_word) {
                        return idx_len.second + 1;
                    }

                    index_length.push({i, idx_len.second + 1});
                    added_new_index = true;
                    used_indexes.insert(i);
                }
            }
            length++;
        }
        return 0;
    }
public:
    int ladderLength(string begin_word, string end_word, vector<string>& word_list) {
        initTransformableTable(word_list);

        int index_end = -1;
        for (int i = 0; i < word_list.size(); i++) {
            if (word_list[i] == end_word) {
                index_end = i;
            }
        }
        if (index_end == -1) {
            return 0;
        }

        unordered_set<int> used_indexes;
        queue<pair<int, int>> index_length;
        for (int i = 0; i < word_list.size(); i++) {
            if (used_indexes.contains(i) || !isTransformable(begin_word, word_list[i])) {
                continue;
            } else if (word_list[i] == end_word) {
                return 2;
            }

            used_indexes.insert(i);
            index_length.push({i, 2});
        }
        return ladderLengthInWordList(end_word, word_list, index_length, used_indexes);
    }
};
```

## memo
- 時間計算量O(N^2 * L + N^2)、メモリO(N^2)
	- ワーストは配列の全要素で一直線のladderが作られるとき。
	- `ladderLengthInWordList`が10^8オーダーステップ、一番内側の`for`文の処理が律速でnsオーダーかかるとすると10^-1s(=100ms)オーダーかかる
		- 実際: 816ms
- BFSは次のstateとしてありえるものを予めすべて列挙しておく
	- DFSはあり得るすべてのルートを試す
	- ありえる状態の中で`end_word`にたどり着いたものがあればそれが最短なのは自明、他のルートは試すまでもない

# 3回目
文字列の比較方法に改善の余地あり

```c++
class Solution {
public:
    int ladderLength(string begin_word, string end_word, vector<string>& word_list) {
        unordered_set<string> available_words(word_list.begin(), word_list.end());
        if (!available_words.contains(end_word)) {
            return 0;
        }

        int ladder_length = 1;
        queue<string> bfs_word;
        bfs_word.push(begin_word);
        while (!bfs_word.empty()) {
            ladder_length++;

            int same_level_count = bfs_word.size();
            for (int i = 0; i < same_level_count; i++) {
                string cur_word = bfs_word.front();
                bfs_word.pop();
                
                for (int j = 0; j < cur_word.size(); j++) {
                    char orig_c = cur_word[j];
                    for (char c = 'a'; c <= 'z'; c++) {
                        cur_word[j] = c;
                        if (!available_words.contains(cur_word)) {
                            continue;
                        } else if (cur_word == end_word) {
                            return ladder_length;
                        }

                        available_words.erase(cur_word);
                        bfs_word.push(cur_word);
                    }
                    cur_word[j] = orig_c;
                }
            }
        }
        return 0;
    }
};
```
- 計算量O(N * L^2 * 26)、メモリO(NL)
	- `L <= 10`と十分に小さいので、`word_list`の全要素について比較するより一文字ずつ変えて`contains`を走らせる方が高速
	- stringのハッシュ計算は一文字ずつ処理するので、実際にはO(L)
	- `for (char c = 'a'; c <= 'z'; c++)`内の処理がnsオーダーとしてワースト5000 * 10 * 10 * 26 = 10^7ステップ=10msオーダー
		- 実際: 36ms
- さらなる高速化: 双方向BFS
	- `begin_word` `end_word`双方から探索する
	- 単方向BFSでは一方向の木、双方向なら両方向(木の階層=ループ数削減)
- 最短距離が長いときに有効

```c++
class Solution {
public:
    int ladderLength(string begin_word, string end_word, vector<string>& word_list) {
        unordered_set<string> available_words(word_list.begin(), word_list.end());
        if (!available_words.contains(end_word)) {
            return 0;
        }
        available_words.erase(end_word);

        unordered_set<string> forward_set{begin_word};
        unordered_set<string> backward_set{end_word};
        int ladder_length = 1;
        while (!forward_set.empty() && !backward_set.empty()) {
            ladder_length++;
            
            if (backward_set.size() < forward_set.size()) {
                swap(forward_set, backward_set);
            }
            unordered_set<string> next_set;
            for (string word : forward_set) {
                for (int i = 0; i < word.size(); i++) {
                    char orig_c = word[i];
                    for (char c = 'a'; c <= 'z'; c++) {
                        if (c == orig_c) {
                            continue;
                        }

                        word[i] = c;
                        if (backward_set.contains(word)) {
                            return ladder_length;
                        } else if (available_words.contains(word)) {
                            available_words.erase(word);
                            next_set.insert(word);
                        }
                        word[i] = orig_c;
                    }
                }
            }
            forward_set = next_set;
        }
        return 0;
    }
};
```

- 1つのノードが平均n個の子を持ち、ladderの最短距離をlとする
	- 単方向BFS: ノード数n^l
	- 双方向BFS: ノード数2 * n^(l/2)

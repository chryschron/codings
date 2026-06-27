# 1回目
HashMap<char, int>で特定文字列内のアルファベット出現回数を保持して比較すればアナグラムか否か楽に判定できそうと思い素直に実装した。

```c++
class Solution {
public:
    using AnagramMap = unordered_map<char, int>;
    struct AnagramGroupInfo {
        string str;
        AnagramMap map;
    };

    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        vector<AnagramGroupInfo> anagram_groups;
        vector<vector<string>> result;

        for (const auto& str : strs) {
            AnagramMap str_anagram_map;
            for (const char c : str) {
                str_anagram_map[c]++;
            }

            auto group_str = findIdenticalAnagramGroupStr(anagram_groups, str_anagram_map);
            if (group_str == nullopt) {
                result.push_back({str});
                anagram_groups.push_back({str, str_anagram_map});
            } else {
                for (int i = 0; i < result.size(); i++) {
                    if (result[i][0] == group_str.value()) {
                        result[i].push_back(str);
                        break;
                    }
                }
            }
        }
        return result;
    }
private:
    optional<string> findIdenticalAnagramGroupStr(vector<AnagramGroupInfo>& anagram_groups, AnagramMap& target_anagram_map) {
        for (const auto anagram_info : anagram_groups) {
            if (anagram_info.map == target_anagram_map) {
                return anagram_info.str;
            }
        }
        return nullopt;
    }
};
```

## memo
- `N = strs.length`として時間計算量O(N^2 * L)
	- 各要素100文字、10^4個の配列を渡されたときを考える
	- 10^4 * 100 * 10^4 = 10^10ステップ、C++だと1-10sオーダー
		- ワーストだとTLEしそう(した)
- メモリO(NL)
- `anagram_groups`がvectorで線形探査が必須になっているのが良くないが、HashMapをキーとしたHashMapはハッシュ関数を自前で作る必要があるのでやめた

# 2回目
アナグラムで一致 = ソートした文字列が一致 という性質を利用すればO(N * L log L)にできるとLLMに教えてもらった。

```c++
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        unordered_map<string, vector<string>> anagram_groups;

        for (const auto& str : strs) {
            string key = str;
            sort(key.begin(), key.end());
            if (auto it = anagram_groups.find(key); it != anagram_groups.end()) {
                it->second.push_back(str);
            } else {
                anagram_groups[key] = {str};
            }
        }

        vector<vector<string>> result;
        for (const auto& group : anagram_groups) {
            result.push_back(group.second);
        }
        return result;
    }
};
```

## memo
- `std::sort`のpractical guarantee
	- 一般にintrosortで実装されている
		- 基本はクイックソートだが、再帰が深くなった(=最悪パターン)のを自動検知してヒープソートに切り替える
		- 最悪でもO(N log N)を維持
	- 順序保持の必要がないため、`std::stable_sort`は不要
- 時間計算量O(N * L log L)、メモリO(NL)、最悪でも高々10^8ステップに収まる

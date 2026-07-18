# 1回目
後ろからsuffixを取っていく手法の方が個人的に直感的なのでそう実装した

```c++
class Solution {
public:
    bool wordBreak(string_view s, const vector<string>& wordDict) {
        // reserve first element for empty string.
        vector<char> shrinkablePos(s.size() + 1);
        shrinkablePos[s.size()] = 1;

        for (int i = s.size(); 0 < i; i--) {
            if (shrinkablePos[i] == 0) {
                continue;
            }

            auto substr = s.substr(0, i);
            for (auto& word : wordDict) {
                if (!substr.ends_with(word)) {
                    continue;
                }
                
                shrinkablePos[i - word.size()] = 1;
            }
        }
        return shrinkablePos[0] == 1;
    }
};
```

## memo
- `s.size() == L, wordDict.size() == M, wordDict[i].size <= N`として時間計算量O(MNL)、空間計算量O(L)
	- `L <= 300, M <= 1000, N <= 20`ゆえms秒オーダー

# 2回目
HashTableを使うとLookupをO(1)に出来る

```c++
class Solution {
public:
    bool wordBreak(string_view s, const vector<string>& wordDict) {
        vector<char> shrinkablePos(s.size() + 1);
        shrinkablePos[s.size()] = 1;
        
        unordered_set<string_view> wordSet(wordDict.begin(), wordDict.end());

        for (int i = s.size() - 1; 0 <= i; i--) {
            for (int j = i + 1; j <= s.size(); j++) {
                if (shrinkablePos[j] == 0) {
                    continue;
                }

                if (wordSet.contains(s.substr(i, j - i))) {
                    shrinkablePos[i] = 1;
                    break;
                }
            }
        }
        return shrinkablePos[0] == 1;
    }
};
```

## memo
- 時間O(L^2)空間O(L+MN)
	- 100μsオーダー
- ところでHashTableの計算は重くないのか
	- `string_view`のハッシュ計算は[`_Hash_bytes()`](https://github.com/gcc-mirror/gcc/blob/master/libstdc%2B%2B-v3/libsupc%2B%2B/hash_bytes.cc)が担当
		- これは`string.length`に依存する
	- 故に`wordSet()`の初期化にO(MN)、Lookupに最大O(L)かかる
	- 実際の時間計算量はO(L^3+MN)
		- 1回目とそこまで変わらない

# 1回目
`s.length <= 10^5`なのでC++だと高々O(N^2)まで耐えられそうだと考えた。

```c++
class Solution {
public:
    int firstUniqChar(string s) {
        unordered_map<char, int> alphabet_map;
        for (const char c : s) {
            alphabet_map[c]++;
        }

        for (int i = 0; i < s.size(); i++) {
            if (alphabet_map.find(s[i])->second == 1) {
                return i;
            }
        }
        return -1;
    }
};
```

## memo
- `N = s.length`として計算量O(N)、メモリO(1)
	- ~~今回sはlowercase onlyなのでそこそこの頻度でハッシュ衝突する~~
	- とはいえ高々O(N^2)、10^5 * 10^5 = 10^10ステップなので最悪でも1sオーダーで処理できる

# 2回目
sはlowercase only、だったらHashMapを用意するまでもなく固定長配列で十分。

```c++
class Solution {
public:
    int firstUniqChar(string s) {
        int alphabet_map[26] = {0};

        for (const char c : s) {
            alphabet_map[c - 'a']++;
        }
        for (int i = 0; i < s.size(); i++) {
            if (alphabet_map[s[i] - 'a'] == 1) {
                return i;
            }
        }
        return -1;
    }
};
```

## memo
- 計算量O(N)、メモリO(1)
	- 予め来る要素がわかっている場合、HashMapを使うまでもない

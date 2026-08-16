# 1回目
Sliding Windowで解く

```c++
class Solution {
public:
    int lengthOfLongestSubstring(const string& s) {
        int longestLength = 0;
        int l, r;
        unordered_set<char> charInSubstr;

        l = r = 0;
        while (r < s.length()) {
            auto [it, inserted] = charInSubstr.insert(s[r]);
            if (!inserted) {
                longestLength = max(longestLength, r - l);
                while (s[l] != s[r]) {
                    charInSubstr.erase(s[l]);
                    l++;
                }
                l++;
            }
            r++;
        }
        longestLength = max(longestLength, r - l);
        return longestLength;
    }
};
```

## memo
- `s.length == N`として時間計算量O(N)、空間計算量O(N)
	- `N <= 10^5`、charなのでハッシュ計算もns程度に収まると考え100μsオーダー

# 2回目
入力がASCIIのみなら普通のテーブルを使って空間計算量をO(1)に落とせる

```c++
class Solution {
public:
    int lengthOfLongestSubstring(const string& s) {
        int longestLength = 0;
        int l, r;
        char charInSubstr[256] = {0};

        l = r = 0;
        while (r < s.length()) {
            if (charInSubstr[s[r]] != 0) {
                longestLength = max(longestLength, r - l);
                while (s[l] != s[r]) {
                    charInSubstr[s[l]] = 0;
                    l++;
                }
                l++;
            } else {
                charInSubstr[s[r]] = 1;
            }
            r++;
        }
        longestLength = max(longestLength, r - l);
        return longestLength;
    }
};
```

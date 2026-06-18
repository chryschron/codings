# 1回目
unordered_map+priority_queue。

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        std::unordered_map<int, int> frequency;
        for (const int num : nums)
            frequency[num]++;
        
        std::priority_queue<pair<int, int>> freq_and_num;
        for (const auto& [num, freq] : frequency)
            freq_and_num.push({freq, num});

        std::vector<int> result;
        for (int i = 0; i < k; i++) {
            result.push_back(freq_and_num.top().second);
            freq_and_num.pop();
        }

        return result;
    }
};
```

## memo
- `nums`内の全要素数をN、ユニークな要素数をMとすると、時間計算量はO(N + MlogM)
	- unordered_mapがO(N)、priority_queueがO(MlogM + KlogM)
- メモリはO(M)、M<=NよりワーストO(N)
- `std::unordered_map`
	- https://ja.cppreference.com/cpp/container/unordered_map
	- ハッシュによって整理、unordered_setの連想配列ver

# 2回目
priority_queueを小さい順にすれば時間計算量をO(N + MlogK)にできる(木の高さが低く保たれるため) 

```c++
class Solution {
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        std::unordered_map<int, int> frequency;
        for (const int num : nums)
            frequency[num]++;
        
        using freq_num = pair<int, int>;
        std::priority_queue<freq_num, std::vector<freq_num>, std::greater<freq_num>> freq_and_num;
        for (const auto& [num, freq] : frequency) {
            freq_and_num.push({freq, num});
            if (freq_and_num.size() > k)
                freq_and_num.pop();
        }

        std::vector<int> result;
        while (!freq_and_num.empty()) {
            result.push_back(freq_and_num.top().second);
            freq_and_num.pop();
        }
        return result;
    }
};
```

## memo
- わざわざpushしたあとpopするので`nums`が小さいときは不利
	- 大きいときは木の高さを低く保てるから有利
- Quick Selectなる方法が言及されているのを見た
	- 順序バラバラの配列からk番目の要素を取る。平均O(N)、最悪O(N^2)
	- 今回の問題にはそぐわないように感じる

# 1回目
MNが十分小さいので普通に解いた

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<vector<int>> grid(m,vector<int>(n));
        for (int i = 0; i < m; i++) {
            grid[i][0] = 1;
        }
        for (int j = 0; j < n; j++) {
            grid[0][j] = 1;
        }

        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                grid[i][j] = grid[i - 1][j] + grid[i][j - 1];
            }
        }
        return grid[m - 1][n - 1];
    }
};
```

## memo
- 時間計算量O(MN)、空間計算量O(MN)
	- `1 <= M, N <= 100`なので10μs, 10KBオーダー程度
- 数学的には`(M+N-2)!/((M-1)!(N-1)!)`を計算すれば良い
	- 時間計算量O(min(M,N))、空間計算量O(1)

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        uint64_t ans = 1;
        int numerator = m + n - 2;
        int denominator = min(m - 1, n - 1); 

        for (int i = 1; i <= denominator; i++) {
            ans = ans * (numerator - denominator + i) / i;
        }
        return (int)ans;
    }
};
```

# 2回目
二次元配列において、一個前のindexの配列の情報しか必要でないから、実は一次元配列で十分

```c++
class Solution {
public:
    int uniquePaths(int m, int n) {
        vector<int> paths(min(m, n), 1);

        for (int k = 1; k < max(m, n); k++) {
            for (int i = 1; i < min(m, n); i++) {
                paths[i] += paths[i - 1];
            }
        }
        return paths[min(m, n) - 1];
    }
};
```

- 時間計算量O(MN)、空間計算量O(min(M, N))

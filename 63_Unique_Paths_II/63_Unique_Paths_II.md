# 1回目
Unique Pathsの応用で解ける

```c++
class Solution {
public:
    int uniquePathsWithObstacles(const vector<vector<int>>& obstacleGrid) {
        int gridHeight = obstacleGrid.size();
        int gridWidth = obstacleGrid[0].size();
        
        vector<int> pathSums(gridWidth);
        for (int j = 0; j < gridWidth; j++) {
            if (obstacleGrid[0][j] == 1) {
                break;
            } else {
                pathSums[j] = 1;
            }
        }

        for (int i = 1; i < gridHeight; i++) {
            if (obstacleGrid[i][0] == 1) {
                pathSums[0] = 0;
            }
            for (int j = 1; j < gridWidth; j++) {
                if (obstacleGrid[i][j] == 1) {
                    pathSums[j] = 0;
                } else {
                    pathSums[j] += pathSums[j - 1];
                }
            }
        }
        return pathSums[gridWidth - 1];
    }
};
```

## memo
- M×N行列が入力として時間計算量O(MN)、空間計算量O(N)
	- `1 <= M, N <= 100`より10μsオーダー
- 数学的に解くのは面倒くさそう(特に障害物が複数になった場合)

# 2回目
ゴール地点から再帰しても解ける

```c++
class Solution {
private:
    vector<vector<int>> pathSums;
    int getPathSum(const vector<vector<int>>& grid, int m, int n) {
        if (m < 0 || n < 0) {
            return 0;
        }
        if (!(m < grid.size() && n < grid[0].size())) {
            return 0;
        }
        if (grid[m][n] == 1) {
            return 0;
        }

        if (m == 0 && n == 0) {
            return 1;
        }

        if (pathSums[m][n] == -1) {
            pathSums[m][n] = getPathSum(grid, m - 1, n)
                           + getPathSum(grid, m, n - 1);
        }
        return pathSums[m][n];
    }
public:
    int uniquePathsWithObstacles(const vector<vector<int>>& obstacleGrid) {
        int gridHeight = obstacleGrid.size();
        int gridWidth = obstacleGrid[0].size();
        pathSums.assign(gridHeight, vector<int>(gridWidth, -1));
        
        return getPathSum(
            obstacleGrid,
            gridHeight - 1,
            gridWidth - 1
        );
    }
};
```

## memo
- メリット: 到達不能時に早めにリターンできる
	- 元は絶対にO(MN)
- デメリット: 再帰分のスタック消費、そのままだとマルチスレッド化できない
	- 排他処理をつけないと行けない
- 空間計算量はO(MN)に増加
	- マックスの再帰量は`max(M, N)`なのでどっちかが10^5オーダー以上に足を踏み込むと怪しい

# 1回目
`200. Number of Islands`を少し変えれば解ける。こっちも配列最大長は十分小さいので再帰で解いて大丈夫。

```c++
class Solution {
private:
    const int dr[4] = {1, -1, 0, 0};
    const int dc[4] = {0, 0, 1, -1};
    int getIslandArea(vector<vector<int>>& grid, int row, int col) const {
        int area = 1;
        grid[row][col] = 0;

        for (int i = 0; i < 4; i++) {
            int new_row = row + dr[i];
            int new_col = col + dc[i];

            if (0 <= new_row && new_row < grid.size() &&
                0 <= new_col && new_col < grid[0].size() &&
                grid[new_row][new_col] == 1) {
                area += getIslandArea(grid, new_row, new_col);
            }
        }
        return area;
    }
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int max_area = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int col = 0; col < grid[0].size(); col++) {
                if (grid[row][col] == 1) {
                    int area = getIslandArea(grid, row, col);
                    max_area = area > max_area ? area : max_area;
                }
            }
        }
        return max_area;
    }
};
```

## memo
- 時間計算量O(mn)、メモリ最悪O(mn)
- iterativeの場合は以下の通り、計算量メモリともに変わらない
	- 同じマスを複数回pushしてしまうのでalready visitedのところで弾く
		- すでに訪れた場所を別に保有しても良い

```c++
class Solution {
private:
    const int dr[4] = {1, -1, 0, 0};
    const int dc[4] = {0, 0, 1, -1};
    int getIslandArea(vector<vector<int>>& grid, int starting_row, int starting_col) const {
        stack<pair<int, int>> island_domain = {};
        island_domain.push({starting_row, starting_col});

        int area = 0;
        while (!island_domain.empty()) {
            const auto [row, col] = island_domain.top();
            island_domain.pop();

            if (grid[row][col] == 0) {
                // already visited
                continue;
            }
            grid[row][col] = 0;
            area++;
            
            for (int i = 0; i < 4; i++) {
                int new_row = row + dr[i];
                int new_col = col + dc[i];

                if (0 <= new_row && new_row < grid.size() &&
                    0 <= new_col && new_col < grid[0].size() &&
                    grid[new_row][new_col] == 1) {
                    island_domain.push({new_row, new_col});
                }
            }
        }
        return area;
    }
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int max_area = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int col = 0; col < grid[0].size(); col++) {
                if (grid[row][col] == 1) {
                    int area = getIslandArea(grid, row, col);
                    max_area = area > max_area ? area : max_area;
                }
            }
        }
        return max_area;
    }
};
```

# 2回目
enumで水陸を管理している方がいたので採用する

```c++
class Solution {
private:
    enum CellType {
        WATER,
        LAND
    };
    const int dr[4] = {1, -1, 0, 0};
    const int dc[4] = {0, 0, 1, -1};
    int getIslandArea(vector<vector<int>>& grid, int row, int col) const {
        int area = 1;
        grid[row][col] = WATER;

        for (int i = 0; i < 4; i++) {
            int new_row = row + dr[i];
            int new_col = col + dc[i];

            if (0 <= new_row && new_row < grid.size() &&
                0 <= new_col && new_col < grid[0].size() &&
                grid[new_row][new_col] == LAND) {
                    area += getIslandArea(grid, new_row, new_col);
                }
        }
        return area;
    }
public:
    int maxAreaOfIsland(vector<vector<int>>& grid) {
        int max_area = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int col = 0; col < grid[0].size(); col++) {
                if (grid[row][col] == LAND) {
                    int area = getIslandArea(grid, row, col);
                    max_area = area > max_area ? area : max_area;
                }
            }
        }
        return max_area;
    }
};
```

# 1回目
`m, n <= 300`なので全要素を舐めて良さそう

```c++
class Solution {
private:
    void exploreIsland(vector<vector<char>>& grid, int row, int column) {
        grid[row][column] = '0';
        if (0 <= (row - 1) && grid[row - 1][column] == '1') {
            exploreIsland(grid, row - 1, column);
        }
        if (0 <= (column - 1) && grid[row][column - 1] == '1') {
            exploreIsland(grid, row, column - 1);
        }
        if ((row + 1) < grid.size() && grid[row + 1][column] == '1') {
            exploreIsland(grid, row + 1, column);
        }
        if ((column + 1) < grid[row].size() && grid[row][column + 1] == '1') {
            exploreIsland(grid, row, column + 1);
        }
    }
public:
    int numIslands(vector<vector<char>>& grid) {
        int total_islands = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int column = 0; column < grid[row].size(); column++) {
                if (grid[row][column] == '1') {
                    exploreIsland(grid, row, column);
                    total_islands++;
                }
            }
        }
        return total_islands;
    }
};
```

## memo
- 計算量O(mn)、ワーストでメモリO(mn)
	- 配列が大きいときにstack overflowする
		- 例えばWindowsの[既定値は1MB](https://learn.microsoft.com/en-us/cpp/build/reference/stack-stack-allocations?view=msvc-170)
		- saved rbpのみがpushされるとしても10^6回程度以上の再帰的呼び出しで踏み越える
			- 10^6 * 1で全要素が1の配列が与えられるとまずい
		- Linuxは8MB? (`ulimit -s`)
	- 明示的なスタックを活用してiterativeに解く(2回目の方に掲載)

# 2回目
iterativeに解こう

```c++
class Solution {
private:
    void exploreIsland(vector<vector<char>>& grid, int starting_row, int starting_column) {
        stack<pair<int, int>> island_domain;
        
        island_domain.push({starting_row, starting_column});
        while (!island_domain.empty()) {
            auto [row, column] = island_domain.top();
            island_domain.pop();
            grid[row][column] = '0';

            if (0 <= (row - 1) && grid[row - 1][column] == '1') {
                island_domain.push({row - 1, column});
            }
            if (0 <= (column - 1) && grid[row][column - 1] == '1') {
                island_domain.push({row, column - 1});
            }
            if ((row + 1) < grid.size() && grid[row + 1][column] == '1') {
                island_domain.push({row + 1, column});
            }
            if ((column + 1) < grid[row].size() && grid[row][column + 1] == '1') {
                island_domain.push({row, column + 1});
            }
        }
    }
public:
    int numIslands(vector<vector<char>>& grid) {
        int total_islands = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int column = 0; column < grid[row].size(); column++) {
                if (grid[row][column] == '1') {
                    exploreIsland(grid, row, column);
                    total_islands++;
                }
            }
        }
        return total_islands;
    }
};
```

## memo
- stackを明示的に使うようにしただけ
	- `grid`のmodificationが禁じられてる場合はすでに訪れた領域を別に保有すれば良さそう

```c++
class Solution {
private:
    vector<vector<bool>> visited_domain;
    void exploreIsland(vector<vector<char>>& grid, int starting_row, int starting_column) {
        stack<pair<int, int>> island_domain;
        
        island_domain.push({starting_row, starting_column});
        visited_domain[starting_row][starting_column] = true;
        while (!island_domain.empty()) {
            auto [row, column] = island_domain.top();
            island_domain.pop();

            if (0 <= (row - 1) && grid[row - 1][column] == '1' && !visited_domain[row - 1][column]) {
                island_domain.push({row - 1, column});
                visited_domain[row - 1][column] = true;
            }
            if (0 <= (column - 1) && grid[row][column - 1] == '1' && !visited_domain[row][column - 1]) {
                island_domain.push({row, column - 1});
                visited_domain[row][column - 1] = true;
            }
            if ((row + 1) < grid.size() && grid[row + 1][column] == '1' && !visited_domain[row + 1][column]) {
                island_domain.push({row + 1, column});
                visited_domain[row + 1][column] = true;
            }
            if ((column + 1) < grid[row].size() && grid[row][column + 1] == '1' && !visited_domain[row][column + 1]) {
                island_domain.push({row, column + 1});
                visited_domain[row][column + 1] = true;
            }
        }
    }
public:
    int numIslands(vector<vector<char>>& grid) {
        visited_domain.resize(grid.size());
        for (int i = 0; i < grid.size(); i++) {
            visited_domain[i].resize(grid[i].size(), false);
        }

        int total_islands = 0;
        for (int row = 0; row < grid.size(); row++) {
            for (int column = 0; column < grid[row].size(); column++) {
                if (grid[row][column] == '1' && !visited_domain[row][column]) {
                    exploreIsland(grid, row, column);
                    total_islands++;
                }
            }
        }
        return total_islands;
    }
};
```

# 3回目
動かす方向を配列で保有すればもっときれいになる
あとは`grid`をメンバ変数にする(引数が多くないのであまり意味はないが)

```c++
class Solution {
private:
    vector<vector<char>> grid_;
    void exploreIsland(int row, int column) {
        int dr[4] = {1, -1, 0, 0};
        int dc[4] = {0, 0, 1, -1};

        grid_[row][column] = '0';
        for (int i = 0; i < 4; i++) {
            int next_row = row + dr[i];
            int next_column = column + dc[i];

            if (next_row < 0 || grid_.size() <= next_row || 
                next_column < 0 || grid_[next_row].size() <= next_column) {
                continue;
            }
            
            if (grid_[next_row][next_column] == '1') {
                exploreIsland(next_row, next_column);
            }
        }
    }
public:
    int numIslands(vector<vector<char>>& grid) {
        int total_islands = 0;
        grid_ = grid;
        for (int row = 0; row < grid_.size(); row++) {
            for (int column = 0; column < grid_[row].size(); column++) {
                if (grid_[row][column] == '1') {
                    exploreIsland(row, column);
                    total_islands++;
                }
            }
        }
        return total_islands;
    }
};
```

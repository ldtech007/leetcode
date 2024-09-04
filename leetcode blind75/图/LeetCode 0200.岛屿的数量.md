> *题目链接：* https://leetcode.cn/problems/number-of-islands/
>
> *视频题解：* https://www.bilibili.com/video/BV1LUsMezEtq/

# LeetCode 200.岛屿的数量

## 题目描述

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。

岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

此外，你可以假设该网格的四条边均被水包围。

**举个例子：**

```
输入：grid = [
  ["1","1","0","0","0"],
  ["1","1","0","0","0"],
  ["0","0","1","0","0"],
  ["0","0","0","1","1"]
]
输出：3
```

## 知识回顾

**广度优先搜索（BFS）** 是一种图形搜索算法，它的基本思想是从一个顶点出发，先访问它的所有邻接顶点，然后再按照同样的方式访问这些邻接顶点的未访问邻接顶点，直到图中所有顶点都被访问。广度优先搜索算法通常使用队列来存储待访问的顶点，并使用一个数组或者集合来记录已访问的顶点。

## 思路解析

先明确怎样才能连成一个岛，水平方向或竖直方向的陆地(`‘1’`)相连，这样的陆地组成一个岛。如下图`区域1`和`区域2`属于不同的两个岛。

![](https://gitee.com/ldtech007/picture/raw/master/pic/lc-0200-01.png)

本题采用**广度优先搜索**来解，算法如下：

1. 采用数组`visit`来保存`grid`中的坐标是否被访问过。`nums`来保存岛屿的数量。
2. 对`grid`中所有未被访问过且`grid[i][j] == 1`的坐标`(i, j)`进行广搜并计数，`++nums`，每次搜索都能保证坐标所在的岛屿被遍历一遍。
3. 返回`nums`。

广搜都是采用队列来实现的，实现广搜的关键是**明确入队的条件**，本题的入队条件是根据当前坐标遍历其**上下左右**四个新坐标，新坐标没有被访问过，而且落在`grid`中，对应的值为`1`就把新坐标压入队列。


## C++代码

```cpp
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int nums = 0;
        int rows = grid.size();
        if (!rows) return nums;
        int cols = grid[0].size();
        vector<vector<bool>> visit(rows, vector<bool>(cols, false));
        //对所有没有被访问过且对应矩阵中的值为1的坐标进行广搜
        for (int row = 0; row < rows; ++row) {
            for(int col = 0; col < cols; ++col) 
                if (grid[row][col] == '1' && !visit[row][col]) {
                    bfs(grid, row, col, visit);
                    ++nums;
                }
        }
        return nums;
    }

    void bfs(vector<vector<char>>& grid, int row, int col, vector<vector<bool>>& visit) {

        queue<pair<int, int>> q;
        visit[row][col] = true;
        q.push({row, col});
        
        while (!q.empty()) {
            pair<int, int> temp = q.front();
            q.pop();
            //保存当前坐标的下、上、右、左四个方向
            vector<vector<int>> directions = {{1,0}, {-1,0}, {0,1}, {0,-1}};
            for (int i = 0; i < directions.size(); ++i) {
                int new_row = temp.first + directions[i][0];
                int new_col = temp.second + directions[i][1];
                //新坐标在grid中，并且新坐标对应的值为1，并且新坐标没有被访问过，就把新坐标压入队列并打上已访问标记
                if (new_row >= 0 && new_row < grid.size() && new_col >= 0 && new_col < grid[0].size() && grid[new_row][new_col] == '1' && !visit[new_row][new_col]) {
                    q.push({new_row, new_col});
                    visit[new_row][new_col] = true;
                }
            } 

        }

    }
};
```

## java代码

```java
class Solution {
    public int numIslands(char[][] grid) {
        int nums = 0;
        int rows = grid.length;
        if (rows == 0) return nums;
        int cols = grid[0].length;
        boolean[][] visit = new boolean[rows][cols];
        // 对所有没有被访问过且对应矩阵中的值为1的坐标进行广搜
        for (int row = 0; row < rows; row++) {
            for (int col = 0; col < cols; col++) {
                if (grid[row][col] == '1' && !visit[row][col]) {
                    bfs(grid, row, col, visit);
                    nums++;
                }
            }
        }
        return nums;
    }

    private void bfs(char[][] grid, int row, int col, boolean[][] visit) {
        Queue<int[]> q = new LinkedList<>();
        visit[row][col] = true;
        q.offer(new int[]{row, col});

        while (!q.isEmpty()) {
            int[] temp = q.poll();
            // 保存当前坐标的下、上、右、左四个方向
            int[][] directions = {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
            for (int i = 0; i < directions.length; i++) {
                int newRow = temp[0] + directions[i][0];
                int newCol = temp[1] + directions[i][1];
                // 新坐标在grid中，并且新坐标对应的值为1，并且新坐标没有被访问过，就把新坐标压入队列并打上已访问标记
                if (newRow >= 0 && newRow < grid.length && newCol >= 0 && newCol < grid[0].length && grid[newRow][newCol] == '1' && !visit[newRow][newCol]) {
                    q.offer(new int[]{newRow, newCol});
                    visit[newRow][newCol] = true;
                }
            }
        }
    }
}
```

## python代码

```python
class Solution:
    def numIslands(self, grid: List[List[str]]) -> int:
        nums = 0
        rows = len(grid)
        if not rows:
            return nums
        cols = len(grid[0])
        visit = [[False] * cols for _ in range(rows)]
        # 对所有没有被访问过且对应矩阵中的值为1的坐标进行广搜
        for row in range(rows):
            for col in range(cols):
                if grid[row][col] == '1' and not visit[row][col]:
                    self.bfs(grid, row, col, visit)
                    nums += 1
        return nums

    def bfs(self, grid: list[list[str]], row: int, col: int, visit: list[list[bool]]):
        q = deque([(row, col)])
        visit[row][col] = True

        while q:
            temp = q.popleft()
            # 保存当前坐标的下、上、右、左四个方向
            directions = [(1, 0), (-1, 0), (0, 1), (0, -1)]
            for direction in directions:
                new_row = temp[0] + direction[0]
                new_col = temp[1] + direction[1]
                # 新坐标在grid中，并且新坐标对应的值为1，并且新坐标没有被访问过，就把新坐标压入队列并打上已访问标记
                if 0 <= new_row < len(grid) and 0 <= new_col < len(grid[0]) and grid[new_row][new_col] == '1' and not visit[new_row][new_col]:
                    q.append((new_row, new_col))
                    visit[new_row][new_col] = True
```

## 复杂度分析

**时间复杂度：** *O(n)*，`n`为网格中元素的总数量。

**空间复杂度：** *O(n)*，`n`为网格中元素的总数量。


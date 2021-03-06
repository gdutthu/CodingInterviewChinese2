# 面试题47：礼物的最大价值



【题目】 在一个 m*n 的棋盘中的每一个格都放一个礼物，每个礼物都有一定的价值（价值大于0）.你可以从棋盘的左上角开始拿各种里的礼物，并每次向左或者向下移动一格，直到到达棋盘的右下角。给定一个棋盘及上面个的礼物，请计算你最多能拿走多少价值的礼物？



**提示：**

0 < grid.length <= 200
0 < grid[0].length <= 200



例如，在下面的棋盘中

```python
输入: 
[
  [1,3,1],
  [1,5,1],
  [4,2,1]
]
输出: 12
解释: 路径 1→3→5→2→1 可以拿到最多价值的礼物
```



LeetCode:[礼物的最大价值](https://leetcode-cn.com/problems/li-wu-de-zui-da-jie-zhi-lcof/)



**思路：动态规划**



```Python
class Solution:
    def maxValue(self, grid: List[List[int]]) -> int:
        rows = len(grid)                                         # 获取礼物数组的行与列
        cols = len(grid[0])

        if rows == 1 and cols == 1:                              # 数组只有一个元素时，直接返回
            return grid[0][0]

        count = [[0 for i in range(cols)] for j in range(rows)]  # 初始化二维数组，记录不同位置能获得礼物的最大价值
        count[0][0] = grid[0][0]

        for rowIndex in range(rows):
            for colIndex in range(cols):
                # 起始点位置不做任何修改

                if rowIndex == 0 and colIndex > 0:    # 第一行元素
                    count[rowIndex][colIndex] = grid[rowIndex][colIndex] + count[rowIndex][colIndex - 1]
                elif colIndex == 0 and rowIndex > 0:  # 第一列元素
                    count[rowIndex][colIndex] = grid[rowIndex][colIndex] + count[rowIndex - 1][colIndex]
                elif rowIndex > 0 and colIndex > 0:   # 其他位置元素
                    count[rowIndex][colIndex] = grid[rowIndex][colIndex] + max(count[rowIndex - 1][colIndex],count[rowIndex][colIndex - 1])
        return count[rows - 1][cols - 1]


```












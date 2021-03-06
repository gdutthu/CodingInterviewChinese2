# 面试题4：二维数组中的查找

**题目：** 在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序进行排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断列表中是否有该整数。



这道题目在左神初级班上也讲过，这里我就直接贴上我之前看视频做的笔记。

LeetCode:[二维数组中的查找](https://leetcode-cn.com/problems/er-wei-shu-zu-zhong-de-cha-zhao-lcof/)



【题目】 给定一个有N*M的整型矩阵matrix和一个整数K，matrix的每一行和每一 列都是排好序的。 实现一个函数， 判断K是否在matrix中。

 例如： 0 1 2 5 2 3 4 7 44 4 8 5 7 7 9

 如果K为7， 返回true； 如果K为6， 返回false。

|  0   |  1   |  2   |  2   |
| :--: | :--: | :--: | :--: |
|  2   |  3   |  4   |  7   |
|  4   |  4   |  4   |  8   |
|  5   |  7   |  7   |  9   |

【要求】 时间复杂度为O(N+M)， 额外空间复杂度为O(1)。



【解题思路】

假设我要从下面这个矩阵array元素中去寻找是否存在数值num=4

1. 先从一行的最右边元素开始遍历，因为端点元素6比数值num=4大，再因为列是已经排序好的，那么6下面元素肯定比6大，那更加肯定比数值num=4大
2. 寻找方向左移一位，遇到元素5，因为元素5比数值num=4大，以此类推，寻找方向继续左移一位
3. 寻找方向左移遇到元素3，因为元素3比数值num=4小，因为行元素是已经排序好的，那么元素3左边的元素肯定比4小，寻找方向下移一位
4. ......
5. 上面的规律就是，从一行最右边元素开始遍历，遇到元素数值比num大，就左移一位；遇到元素数值比num小，就下移一位，直到元素索引下标越界

|  1   |  3   |  5   |  6   |
| :--: | :--: | :--: | :--: |
|  2   |  5   |  7   |  9   |
|  4   |  6   |  8   |  10  |



**算法复杂度：**

从矩阵右上角的点开始取点与该数比较，如果大于该数，那么说明这个点所在的列都不存在该数，将这个点左移；如果这个点上的数小于该数，那么说明这个点所在的行不存在该数，将这个点下移。直到找到与该数相等的点为止。最坏的情况是，该数只有一个且在矩阵左下角上，那么时间复杂度为O(M-1+N-1)=O(M+N)



```python
#函数功能：判断二维数组中是否含有该整数k
#解题思路：将该问题转化成求解数组中位数的问题
#算法复杂度:O(N+M)

def FindInPartiallySortedMatrix(array,k):
    if array.shape[0]==0 or array.shape[1]==0: #判断数组是否为空数组
        return None
    rowIndex=0                           #初始化移动下标,从数组的右上角开始寻找
    colIndex=array.shape[1]-1
    while rowIndex<=array.shape[0]-1 and colIndex>=0:
        if array[rowIndex,colIndex]==k:  #当前元素数值等于整数k
            return True
        elif array[rowIndex,colIndex]>k: #当前元素数较大时，该矩阵为递增矩阵，列位置减1，移动到较小的元素
            colIndex=colIndex-1
        elif array[rowIndex,colIndex]<k: #当前元素数较小时，该矩阵为递增矩阵，行位置加1，移动到较大的元素
            rowIndex=rowIndex+1
    return False
```


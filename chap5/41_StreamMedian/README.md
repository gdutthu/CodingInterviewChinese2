# 面试题41：数据流的中位数

问题描述：如何得到一个数据流中的中位数

备注：

如果数据流中读出奇数个数值，那么中位数就是所有数值排序之后的位于中间的数值

如果数据流中读出偶数个数值，那么中位数就是所有数值排序之后的中间两个数的平均值



LeetCode:[数据流的中位数](https://leetcode-cn.com/problems/shu-ju-liu-zhong-de-zhong-wei-shu-lcof/)



**思路一：（一般用来处理静态数据，也就是数据本身不变化）**

1. 先对数组进行快速排序，算符复杂度为O(N*log N)
2. 当N为偶数的时候，中位数就排序后数组中间两个数的和再除以2。当N为奇数的时候，中位数就是排序后数组中间那个数值。

**思路一的弊端：**

每次要查询的时候，都要进行数组排序，不能做到实时查询。

**思路二：（一般用来处理动态数据，也就是数据还会不断增加进来）**

1. 采用两个堆结构来存放数组，其中一个是大根堆（用来存放个数N/2的较小的数值),另外一个是一个是小根堆（用来存放个数N/2的较大的数值),
2. 那么大根堆的堆顶元素就是较小的个数N/2的数组较小数值的最大值，小根堆的堆顶元素就是较大的个数N/2的数组较小数值的最小值。这两个堆顶元素就是这个数组的中位数。
3. 构建这两个堆的步骤如下：

- 数组不断弹出元素来插入到堆里面（array[i   for i in range(len(array)-1)] )，第一个元素默认进大根堆。然后在进行插入的时候，若数组的元素小于大根堆的堆顶元素，就进入大根堆，大于大根堆堆顶元素就进入小根堆。
- 时刻保持大根堆的元素个数和小根堆的元素个数差值不得大于2，如果大根堆元素个数多，就弹出大根堆的堆顶元素到小根堆的堆顶，然后对这两个堆都进行堆调整（即heapify操作）。



```python
################大根堆模块：建立大根堆、调整大根堆################
# 建立大根堆,本质上是向上调整
def bigHeapInsert(array, index):
    parentIndex = (index - 1) // 2  # 父节点的下标索引
    while parentIndex >= 0 and array[parentIndex] < array[index]:
        array[index], array[parentIndex] = array[parentIndex], array[index]
        index = parentIndex         # 交换后，更新节点信息
        parentIndex = (index - 1) // 2


# 调整大根堆,本质上是向下调整
def bigHeapIfy(array, index):
    leftSonIndex = 2 * index + 1                    # 左右儿子节点的下标索引
    rightSonIndex = 2 * index + 2
    while leftSonIndex <= len(array) - 1:           # 大跟堆向下调整的前提是该节点至少存在左儿子节点
        if rightSonIndex > len(array) - 1:          # 该节点只存在左儿子节点，不存在右儿子节点
            if array[leftSonIndex] > array[index]:  # 若左儿子节点数值比该节点数值大，就进行交换
                array[index], array[leftSonIndex] = array[leftSonIndex], array[index]
            break                                   # 无论是否进行数值交换，都在该轮后跳出循环
        else:                                                # 该节点同时存在左右儿子节点
            if array[leftSonIndex] >= array[rightSonIndex]:  # 找出左右儿子节点所对应数值的较大值
                bigIndex = leftSonIndex
            else:
                bigIndex = rightSonIndex
            if array[bigIndex] > array[index]:               # 若该节点数值比儿子节点的较大值小，就进行交换
                array[index], array[bigIndex] = array[bigIndex], array[index]
                index = bigIndex                             # 交换后，更新节点信息
                leftSonIndex = 2 * index + 1                 # 左右儿子节点的下标索引
                rightSonIndex = 2 * index + 2
            else:                                            # 若该节点数值比儿子节点的较大值还大，则不需要进行交换，直接跳出循环
                break


################小根堆模块：建立小根堆、调整小根堆################
# 建立小跟堆，本质是向上调整
def smallHeapInsert(array, index):
    parentIndex = (index - 1) // 2  # 父节点的下标索引
    while parentIndex >= 0 and array[index] < array[parentIndex]:
        array[index], array[parentIndex] = array[parentIndex], array[index]
        index = parentIndex         # 交换后，更新节点信息
        parentIndex = (index - 1) // 2


# 调整小根堆，本质是向下调整
def smallHeapIfy(array, index):
    leftSonIndex = 2 * index + 1            # 左右儿子节点的下标索引
    rightSonIndex = 2 * index + 2
    while leftSonIndex <= len(array) - 1:   # 小跟堆向下调整的前提是该节点至少存在左儿子节点
        if rightSonIndex > len(array) - 1:  # 该节点只存在左儿子节点，不存在右儿子节点
            if array[leftSonIndex] < array[index]:  # 若左儿子节点数值比该节点数值小，就进行交换
                array[index], array[leftSonIndex] = array[leftSonIndex], array[index]
            break   # 无论是否进行数值交换，都在该轮后跳出循环
        else:       # 该节点同时存在左右儿子节点
            if array[leftSonIndex] <= array[rightSonIndex]:  # 找出左右儿子节点所对应数值的较小值
                smallIndex = leftSonIndex
            else:
                smallIndex = rightSonIndex
            if array[smallIndex] < array[index]:  # 若该节点数值比儿子节点的较小值大，就进行交换
                array[index], array[smallIndex] = array[smallIndex], array[index]
                index = smallIndex                # 交换后，更新节点信息
                leftSonIndex = 2 * index + 1      # 左右儿子节点的下标索引
                rightSonIndex = 2 * index + 2
            else:     # 若该节点数值比儿子节点的较小值还小，则不需要进行交换，直接跳出循环
                break


#####################解法主函数入口###############

class MedianFinder:

    def __init__(self):
        """
        initialize your data structure here.
        """
        self.size = 0        # 统计数据流的元素个数
        self.bigHeap = []    # 大根堆
        self.smallHeap = []  # 小根堆

    def addNum(self, num: int) -> None:
        if self.size == 0:  # 第一个元素压入大跟堆
            self.bigHeap.append(num)
        else:
            # 若新读取的数值比大根堆头结点（大根堆最大值）大，那将该数放入小根堆
            # 反之，那将该数放入大根堆
            if num > self.bigHeap[0]:
                self.smallHeap.append(num)
                smallHeapInsert(self.smallHeap, len(self.smallHeap) - 1)
            else:
                self.bigHeap.append(num)
                bigHeapInsert(self.bigHeap, len(self.bigHeap) - 1)
                # 调整堆结构
                # 大根堆的长度和小根堆的长度相差不能超过2
        if len(self.bigHeap) >= len(self.smallHeap) + 2:              # 大根堆的长度比小根堆的长度长2以上
            # 大根堆的头结点和尾节点先进行交换
            self.bigHeap[len(self.bigHeap) - 1], self.bigHeap[0] = self.bigHeap[0], self.bigHeap[len(self.bigHeap) - 1]
            self.smallHeap.append(self.bigHeap.pop())                 # 再将大根堆的尾节点移到小根堆上
            smallHeapInsert(self.smallHeap, len(self.smallHeap) - 1)  # 分别对大、小根堆进行堆调整
            bigHeapIfy(self.bigHeap, 0)
        elif len(self.bigHeap) + 2 <= len(self.smallHeap):            # 小根堆的长度比大根堆的长度长2以上
            # 小根堆的头结点和尾节点先进行交换
            self.smallHeap[len(self.smallHeap) - 1], self.smallHeap[0] = self.smallHeap[0], self.smallHeap[len(self.smallHeap) - 1]
            self.bigHeap.append(self.smallHeap.pop())  # 再将小根堆的尾节点移到大根堆上
            smallHeapIfy(self.smallHeap, 0)            # 对大、小根堆进行堆调整
            bigHeapInsert(self.bigHeap, len(self.bigHeap) - 1)

        # 更新数据里中元素个数
        self.size += 1


    # 返回数据流中的中位数
    def findMedian(self) -> float:
        # 数据流的个数为奇数
        if self.size % 2 != 0:
            if len(self.bigHeap) > len(self.smallHeap):
                return self.bigHeap[0]
            else:
                return self.smallHeap[0]
        # 数据流的个数为偶数
        else:
            return (self.bigHeap[0] + self.smallHeap[0]) / 2


# Your MedianFinder object will be instantiated and called as such:
# obj = MedianFinder()
# obj.addNum(num)
# param_2 = obj.findMedian()
```


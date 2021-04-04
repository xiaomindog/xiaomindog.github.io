---
title: 手撕排序之堆排序
date: 2021-03-16 16:46:44
tags: ['leetcode','排序算法']
categories: ['leetcode']
---

堆排序的原理是很好理解的，但是如果让你手撕堆排序，你能解决吗？

代码如下，测试通过。[排序数组](https://leetcode-cn.com/problems/sort-an-array/submissions/)

<!--more-->

```python
class heapSort:
    def __init__(self):
        pass

    def heapify(self, arr, i):
        """把最大元素推到堆顶"""
        left, right = 2 * i + 1, 2 * i + 2
        largest = i
        if left < arr_len and arr[left] > arr[largest]:
            largest = left
        if right < arr_len and arr[right] > arr[largest]:
            largest = right

        if largest != i:
            # 最大元素推到堆顶，同时向下调整继续保持为大顶堆
            arr[largest], arr[i] = arr[i], arr[largest]
            self.heapify(arr, largest)

    def build_max_heap(self, arr):
        """建大顶堆堆, 调用 heapify()"""

        for i in range(len(arr) // 2, -1, -1):
            self.heapify(arr, i)

    def heap_sort(self, arr):
        """堆排序"""
        global arr_len
        arr_len = len(arr)
        self.build_max_heap(arr)
        for i in range(arr_len - 1, 0, -1):
            arr[0], arr[i] = arr[i], arr[0]
            arr_len -= 1
            self.heapify(arr, 0)

        return arr

class Solution:
    def sortArray(self, nums: List[int]) -> List[int]:
        return heapSort().heap_sort(nums)
        
```


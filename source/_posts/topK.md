---
title: 最小的K个数的多种解法
date: 2021-04-01 15:02:31
tags: ['leetcode']
categories: ['leetcode']
---

最小的k个数在面试中遇到了4次，记录一下多种解法。

<!--more-->

##  快速选择

- 初始版本
  -  时间复杂度最坏情况下O(NlogN)
  - 空间复杂度O(logN)

```python
class Solution(object):
    def getLeastNumbers(self, arr, k):
        """
        :type arr: List[int]
        :type k: int
        :rtype: List[int]
        """
        if k < 0 or k > len(arr): return
        if k == 0: return []
        self.quickSort(arr, 0, len(arr) - 1, k)
        return arr[:k]

    def quickSort(self, arr, i, j, k):
        if i > j: return
        p = self.partition(arr, i, j)
        if p == k:
            return
        elif p > k:
            self.quickSort(arr, i, p - 1, k)
        else:
            self.quickSort(arr, p + 1, j, k)

    def partition(self, arr, i, j):
        # if i >= j: return i
        pivot = arr[i]
        while i < j:
            while i < j and arr[j] >= pivot:
                j -= 1
            arr[i] = arr[j]

            while i < j and arr[i] <= pivot:
                i += 1
            arr[j] = arr[i]

        arr[i] = pivot
        return i
```

- 优化版本

  **三数取中的方法优化选取枢轴**

  ```python
  def partition(self, arr, i, j):
          mid = (i + j) >> 1
          if arr[i] > arr[j]:  # 把小的放到i上去
              arr[i], arr[j] = arr[j], arr[i]
          if arr[mid] > arr[j]:  # 把最大的放到最后去
              arr[mid], arr[j] = arr[j], arr[mid]
          if arr[mid] > arr[i]:
              arr[mid], arr[i] = arr[i], arr[mid]
  
          pivot = arr[i]
          while i < j:
              while i < j and arr[j] >= pivot:
                  j -= 1
              arr[i] = arr[j]
  
              while i < j and arr[i] <= pivot:
                  i += 1
              arr[j] = arr[i]
  
          arr[i] = pivot
          return i
  ```

  

##  最大堆

要找到最小的K个数，需要建立一个大顶堆，初始化一个k的元素大顶堆，对于剩下的n-k个数，遍历一下，如果遇到比堆顶元素小的，则删除堆顶元素，该元素入堆，更新堆。

时间复杂度：建堆：O(NlogK)

在python中内置的堆为小顶堆，若要找到最小的K个数，将元素取反即可。

```python
class Solution(object):
    def getLeastNumbers(self, arr, k):
        """
        :type arr: List[int]
        :type k: int
        :rtype: List[int]
        """
        if k < 0 or k > len(arr): return
        if k == 0: return []
        import heapq
        arr = [-a for a in arr]
        hp = arr[:k]
        heapq.heapify(hp)

        for i in range(k, len(arr)):
            if arr[i] > hp[0]:
                heapq.heappop(hp)
                heapq.heappush(hp, arr[i])

        return [-i for i in hp]
```

##  计数排序

数据范围有限时直接计数排序就行了：时间复杂度O(N)

```python
class Solution(object):
    def getLeastNumbers(self, arr, k):
        """
        :type arr: List[int]
        :type k: int
        :rtype: List[int]
        """

        counter = [0] * 10000
        for a in arr:
            counter[a] += 1

        ans = []
        idx = 0

        for i in range(len(counter)):
            while counter[i] > 0 and idx < k:
                ans.append(i)
                idx += 1
                counter[i] -= 1
            if idx == k: break
        return ans
```


---
title: 快速排序与快速选择
date: 2018-08-19 20:57:08
tags: [c++,算法]
---
# 快速排序与快速选择

## 快速排序

本文中的快速排序算法来自于 YouTube 视频：[Quicksort ](https://www.youtube.com/watch?v=aQiWF4E8flQ&t=188s)。

### 中心思想

快速排序的思想是分而治之。随机选取一个元素 pivot，然后对数据进行原地重排，使得数据变为如下形式：pivot 左侧元素均比 pivot 小，右侧元素均比它大。然后分别对左右两侧的元素进行该操作，不断递归。

通常选取数组最后一个元素作为 pivot，但是最坏情况的时间复杂度会达到 O(n<sup>2</sup>)，优化方法是随机选取 pivot 后与最后的元素进行交换。

### 算法流程

对于长度为 n - 1 的数组，定义以下三个值。

`pivot`：位于 n - 1 的`元素`（序列最后一个元素），是进行数组分割的中间值。

`spliter`：是最终分割序列的`元素位置`，从 0 开始。每次发现位于 current 的元素小于 pivot 时向后移动 1；完成一次排序扫描后，将 pivot 元素交换至 spliter 位置，分别对其分割出的前后元素序列进行递归排序。

`current`：当前进行比较的`元素位置`，从 0 开始，在循环中向后不断移动。每次循环中此元素与 pivot 比较，如果小于 pivot 则与 spliter 位置的元素进行交换。最后在 pivot 前停止。

![](http://qiniu1.letow.top/quicksort-1.jpg)

<!--more-->

开始时 spliter = current = 0，arr[current] = 5 < pivot = 6，交换 arr[spliter] 与 arr[current]，实际上没有发生交换；spliter++。

![](http://qiniu1.letow.top/quicksort-2.jpg)

现在 spliter = current = 1，arr[current] = 2 < pivot = 6，交换，spliter++。

![](http://qiniu1.letow.top/quicksort-3.jpg)

过程同上。

![](http://qiniu1.letow.top/quicksort-4.jpg)

此时 spliter = current = 3，arr[current] = 7 > pivot = 6，不进行交换。

![](http://qiniu1.letow.top/quicksort-5.jpg)

此时 spliter = 3，current  = 4，arr[current] = 3 < pivot = 6，需要交换 arr[current] 与 arr[spliter]，并且 spliter++。

![](http://qiniu1.letow.top/quicksort-6.jpg)

交换完成后 spliter = current = 4。此时 current 元素位于 pivot 之前一位，循环结束。需要将 arr[current] 与 pivot 元素进行交换。

![](http://qiniu1.letow.top/quicksort-7.jpg)

交换完成后，本次 quickSort 函数调用结束。

本次函数运行的结果：pivot 位于两端序列中间，前面的序列元素均比 pivot 小，后面的序列元素均比 pivot 大。此时分别对这两段序列运行 quickSort 函数。

![](http://qiniu1.letow.top/quicksort-8.jpg)

![](http://qiniu1.letow.top/quicksort-9.jpg)

![](http://qiniu1.letow.top/quicksort-10.jpg)

![](http://qiniu1.letow.top/quicksort-11.jpg)

![](http://qiniu1.letow.top/quicksort-12.jpg)

![](http://qiniu1.letow.top/quicksort-13.jpg)

前一段的子序列排序完成后，序列再次被位于 spliter 位置的 pivot 分开，再次对前后两个序列进行分别递归。

![](http://qiniu1.letow.top/quicksort-14.jpg)

![](http://qiniu1.letow.top/quicksort-15.jpg)

![](http://qiniu1.letow.top/quicksort-16.jpg)

对于长度为 0 或 1 的序列，需要进行额外判断，以防止递归出错。

### 实现代码

```c++
template <typename T>
void quickSort(T *t, int n)
{
    if (n <= 1)
    {
        return;
    }
    int split = 0;
    for (int i = 0; i < n - 1; i++)
    {
        if (t[i] < t[n - 1])
        {
            swap(t[i], t[split]);
            split++;
        }
    }
    swap(t[split], t[n - 1]);
    quickSort(t, split);
    quickSort(t + split + 1, n - split - 1);
}
```

## 快速选择

快速选择是一种从序列中选取大小为第 n 位的元素的方法。快速排序和快速选择都是 C. A. R. Hoare （东尼霍尔）提出的，二者也有相似之处。

> 快速选择的总体思路与快速排序一致，选择一个元素作为基准来对元素进行分区，将小于和大于基准的元素分在基准左边和右边的两个区域。不同的是，快速选择并不递归访问双边，而是只递归进入一边的元素中继续寻找。这降低了平均时间复杂度，从O(*n*log*n*)至O(*n*)，不过最坏情况仍然是O(*n*2)。 

以上引自[维基百科](https://zh.wikipedia.org/wiki/%E5%BF%AB%E9%80%9F%E9%80%89%E6%8B%A9)。

```c++
template <class T>
T quickSelect(T *t, int n, int idx, int des)
{
    if (n <= 1)
    {
        if (idx + n == des)
        {
            return *(t + n);
        }
    }
    int split = 0;
    for (int i = 0; i < n - 1; i++)
    {
        if (t[i] < t[n - 1])
        {
            swap(t[i], t[split]);
            split++;
        }
    }
    swap(t[split], t[n - 1]);
    if (idx + split == des)
    {
        return *(t + split);
    }
    else if (idx + split > des)
    {
        return quickSelect(t, split, idx, des);
    }
    else
    {
        return quickSelect(t + split + 1, n - split - 1, idx + split + 1, des);
    }
}
```



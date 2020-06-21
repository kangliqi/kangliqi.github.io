# 排序、查找算法

- [排序、查找算法](#排序查找算法)
  - [冒泡排序](#冒泡排序)
  - [选择排序](#选择排序)
  - [插入排序](#插入排序)
  - [归并排序](#归并排序)
  - [快速排序](#快速排序)
  - [计数排序](#计数排序)
  - [二分查找](#二分查找)
    - [简单二分查换](#简单二分查换)
    - [优化版](#优化版)
    - [近似查找](#近似查找)

## 冒泡排序

- **代码实现**

    ```go
    // BubbleSort an array
    func BubbleSort(arr []int) {
        size := len(arr)

        for i := 0; i < size; i++ {
            flag := false
            for j := 0; j < size-i-1; j++ {
                if arr[j] > arr[j+1] {
                    arr[j], arr[j+1] = arr[j+1], arr[j]
                    flag = true
                }
            }
            if !flag {
                break
            }
        }
    }
    ```

## 选择排序

- **代码实现**

    ```go
    // SelectSort an array
    func SelectSort(arr []int) {
        size := len(arr)
        for i := 0; i < size; i++ {
            minIdx := i
            min := arr[i]
            for j := i + 1; j < size; j++ {
                if arr[j] < min {
                    minIdx = j
                    min = arr[j]
                }
            }
            arr[i], arr[minIdx] = arr[minIdx], arr[i]
        }
    }
    ```

## 插入排序

- **代码实现**

    ```go
    // InsertSort an array
    func InsertSort(arr []int) {
        size := len(arr)
        for i := 1; i < size; i++ {
            temp := arr[i]
            j := i - 1
            for ; j >= 0; j-- {
                if arr[j] > temp {
                    arr[j+1] = arr[j]
                } else {
                    break
                }
            }
            arr[j+1] = temp
        }
    }
    ```

## 归并排序

- **代码实现**

    ```go
    // MergeSort an array
    func MergeSort(arr []int) {
        mergeSort(arr, 0, len(arr)-1)
    }

    func mergeSort(arr []int, left, right int) {
        if left >= right {
            return
        }

        mid := left + (right-left)>>1
        mergeSort(arr, left, mid)
        mergeSort(arr, mid+1, right)
        merge(arr, left, mid, right)
    }

    func merge(arr []int, left, mid, right int) {
        temp := make([]int, right-left+1)
        l, r, k := left, mid+1, 0

        for i := left; i <= right; i++ {
            if l > mid {
                temp[k] = arr[r]
            } else if r > right {
                temp[k] = arr[l]
                l++
            } else {
                if arr[l] <= arr[r] {
                    temp[k] = arr[l]
                    l++
                } else {
                    temp[k] = arr[r]
                    r++
                }
            }
            k++
        }

        for i := 0; i < right-left+1; i++ {
            arr[left+i] = temp[i]
        }
    }
    ```

## 快速排序

    ```go
    // QuickSort an array
    // arr: The array to sort
    func QuickSort(arr []int) {
        quickSort(arr, 0, len(arr)-1)
    }

    func quickSort(arr []int, left, right int) {
        if left >= right {
            return
        }

        mid := partition(arr, left, right)
        quickSort(arr, left, mid-1)
        quickSort(arr, mid+1, right)
    }

    func partition(arr []int, left, right int) int {
        pivot := arr[right]
        i := left
        for j := left; j < right; j++ {
            if arr[j] < pivot {
                arr[i], arr[j] = arr[j], arr[i]
                i++
            }
        }
        arr[right], arr[i] = arr[i], pivot
        return i
    }
    ```

## 计数排序

基于桶排序的升级版

- **代码实现**

    ```go
    // CountingSort an array
    func CountingSort(arr []int) {
        size := len(arr)
        max := arr[0]
        for i := 1; i < size; i++ {
            if arr[i] > max {
                max = arr[i]
            }
        }

        // 计数
        var c = make([]int, max+1)
        for i := 0; i < size; i++ {
            c[arr[i]]++
        }

        // 累加
        for i := 1; i < len(c); i++ {
            c[i] = c[i-1] + c[i]
        }

        // 排序
        var index int
        var temp = make([]int, size)
        for i := size - 1; i >= 0; i-- {
            index = c[arr[i]] - 1 // 从计数累加数组中取出索引
            temp[index] = arr[i]
            // temp[c[arr[i]-1]] = arr[i]
            c[arr[i]]--
        }

        // 拷贝回原数组
        for i := 0; i < size; i++ {
            arr[i] = temp[i]
        }
    }

    ```

## 二分查找

### 简单二分查换

- **代码实现**

    ```go
    // BinarySearch a value from arr
    // arr: 	The array search for
    // value: 	The search value
    func BinarySearch(arr []int, value int) int {
        low, high := 0, len(arr)-1
        var mid int

        for low <= high {
            mid = low + (high-low)>>1
            if value < arr[mid] {
                high = mid - 1 // 要找的值在左边
            } else if value > arr[mid] {
                low = mid + 1 // 要找的值在右边
            } else {
                return mid
            }
        }
        return -1
    }
    ```

### 优化版

    ```go
    // BinarySearch a value from arr
    // arr: 	The array search for
    // value: 	The search value
    // left: 	Option search the left or right if there any duplicate value
    func BinarySearch(arr []int, value int, left bool) int {
        low, high := 0, len(arr)-1
        var mid int

        for low <= high {
            mid = low + (high-low)>>1
            if value < arr[mid] {
                high = mid - 1 // 要找的值在左边
            } else if value > arr[mid] {
                low = mid + 1 // 要找的值在右边
            } else {
                if left {
                    // 如果有重复元素，查找左边第一个
                    if mid == 0 || arr[mid-1] < value {
                        return mid
                    }
                    high = mid - 1
                } else {
                    // 如果有重复元素，查找右边第一个
                    if mid == len(arr)-1 || arr[mid+1] > value {
                        return mid
                    }
                    low = mid + 1
                }
            }
        }
        return -1
    }
    ```

### 近似查找

- 二分查找大于给定值的第一个元素

    ```go
    // BinarySearchGt 二分查找大于给定值的第一个元素
    func BinarySearchGt(arr []int, value int) int {
        size := len(arr)
        low := 0
        high := size - 1
        var mid int

        // 边界情况，第一个元素就大于给定值
        if arr[0] > value {
            return 0
        }

        for low <= high {
            mid = low + (high-low)>>1

            // 要查找的元素在右边，此时中间值小于或等于给定值
            if arr[mid] <= value {
                // 如果中间值右边一位大于给定值，那该元素就是大于给定值的第一个元素
                if mid != size-1 && arr[mid+1] > value {
                    return mid + 1
                }
                low = mid + 1
            } else {
                high = mid - 1
            }
        }

        return -1
    }
    ```

- 二分查找小于给定值的最后一个元素

    ```go
    // BinarySearchLt 二分查找小于给定值的最后一个元素
    func BinarySearchLt(arr []int, value int) int {
        size := len(arr)
        low := 0
        high := size - 1
        var mid int

        // 边界情况，最后一个元素都小于给定值
        if arr[size-1] < value {
            return size - 1
        }

        for low <= high {
            mid = low + (high-low)>>1

            // 要查找的元素在左边，此时中间值大于或等于给定值
            if arr[mid] >= value {
                // 如果中间值左边一位小于给定值，那该元素就是小于给定值的最后一个元素
                if mid != 0 && arr[mid-1] < value {
                    return mid - 1
                }
                high = mid - 1
            } else {
                low = mid + 1
            }
        }
        return -1
    }

    ```
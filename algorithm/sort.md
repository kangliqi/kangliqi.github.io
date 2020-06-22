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

- **算法原理**

    1. 比较相邻的元素。如果第一个比第二个大，就交换他们两个。
    2. 对每一对相邻元素做同样的工作，从开始第一对到结尾的最后一对。在这一点，最后的元素应该会是最大的数。 
    3. 针对所有的元素重复以上的步骤，除了最后一个。 
    4. 持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。

- **时间复杂度**

    若文件的初始状态是正序的，一趟扫描即可完成排序。所需的关键字比较次数 C 和记录移动次数 M 均达到最小值：

    C<sub>min</sub>  = n - 1, M<sub>min</sub> = 0

    所以，冒泡排序最好的时间复杂度为 O(n)

    若初始文件是反序的，需要进行 `n - 1` 趟排序。每趟排序要进行 `n - i` 次关键字的比较`(1≤i≤n-1)`，且每次比较都必须移动记录三次来达到交换记录位置。在这种情况下，比较和移动次数均达到最大值：

    C<sub>max</sub> = n(n - 1) / 2 = O(n<sup>2</sup>)

    M<sub>max</sub> = 3n(n - 1) / 2 = O(n<sup>2</sup>)

    冒泡排序的最坏时间复杂度为 O(n<sup>2</sup>)

    综上，因此冒泡排序总的平均时间复杂度为 O(n<sup>2</sup>)

- **算法稳定性**

    冒泡排序就是把小的元素往前调或者把大的元素往后调。比较是相邻的两个元素比较，交换也发生在这两个元素之间。所以，如果两个元素相等，是不会再交换的；如果两个相等的元素没有相邻，那么即使通过前面的两两交换把两个相邻起来，这时候也不会交换，所以相同元素的前后顺序并没有改变，所以冒泡排序是一种稳定排序算法。

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

- **实现思路**

    首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后，再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。以此类推，直到所有元素均排序完毕。

- **时间复杂度**

    选择排序的交换操作介于 `0` 和 `(n - 1)` 次之间。选择排序的比较操作为 `n (n - 1) / 2` 次之间。选择排序的赋值操作介于 `0` 和 `3 (n - 1)` 次之间。比较次数 O(n<sup>2</sup>)，比较次数与关键字的初始状态无关，总的比较次数`N=(n-1)+(n-2)+...+1=n*(n-1)/2`。交换次数`O(n)`，最好情况是，已经有序，交换0次；最坏情况交换`n-1`次，逆序交换`n/2`次。交换次数比冒泡排序少多了，由于交换所需CPU时间比比较所需的CPU时间多，n值较小时，选择排序比冒泡排序快。

- **算法稳定性**

    选择排序是给每个位置选择当前元素最小的，比如给第一个位置选择最小的，在剩余元素里面给第二个元素选择第二小的，依次类推，直到第 `n-1` 个元素，第n个元素不用选择了，因为只剩下它一个最大的元素了。那么，在一趟选择，如果一个元素比当前元素小，而该小的元素又出现在一个和当前元素相等的元素后面，那么交换后稳定性就被破坏了。比较拗口，举个例子，序列 `5 8 5 2 9`，我们知道第一遍选择第1个元素5会和2交换，那么原序列中两个5的相对前后顺序就被破坏了，所以选择排序是一个不稳定的排序算法。

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

- **算法原理**

    插入排序的工作方式像许多人排序一手扑克牌。开始时，我们的左手为空并且桌子上的牌面向下。然后，我们每次从桌子上拿走一张牌并将它插入左手中正确的位置。为了找到一张牌的正确位置，我们从右到左将它与已在手中的每张牌进行比较。拿在左手上的牌总是排序好的，原来这些牌是桌子上牌堆中顶部的牌

    插入排序是指在待排序的元素中，假设前面 `n-1` (其中 `n>=2 `)个数已经是排好顺序的，现将第 `n` 个数插到前面已经排好的序列中，然后找到合适自己的位置，使得插入第n个数的这个序列也是排好顺序的。按照此法对所有元素进行插入，直到整个序列排为有序的过程，称为插入排序

- **时间复杂度**

    在插入排序中，当待排序数组是有序时，是最优的情况，只需当前数跟前一个数比较一下就可以了，这时一共需要比较 `n - 1` 次，时间复杂度为 O(n)

    最坏的情况是待排序数组是逆序的，此时需要比较次数最多，总次数记为：`1+2+3+…+N-1`，所以，插入排序最坏情况下的时间复杂度为 O(n<sup>2</sup>)

    平均来说，`A[1..j-1]` 中的一半元素小于 `A[j]`，一半元素大于 `A[j]`。插入排序在平均情况运行时间与最坏情况运行时间一样，是输入规模的二次函数

- **算法稳定性**

    如果待排序的序列中存在两个或两个以上具有相同关键词的数据，排序后这些数据的相对次序保持不变，即它们的位置保持不变，则该算法是稳定的；如果排序后，数据的相对次序发生了变化，则该算法是不稳定的。关键词相同的数据元素将保持原有位置不变，所以该算法是稳定的

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

归并排序（MERGE-SORT）是建立在归并操作上的一种有效的排序算法,该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。将已有序的子序列合并，得到完全有序的序列；即先使每个子序列有序，再使子序列段间有序。若将两个有序表合并成一个有序表，称为二路归并。归并排序是一种稳定的排序方法

- **算法原理**



- **时间复杂度**



- **算法稳定性**



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

快速排序由C. A. R. Hoare在1960年提出。它的基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

- **算法原理**



- **时间复杂度**



- **算法稳定性**



- **代码实现**

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

- **代码实现**

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
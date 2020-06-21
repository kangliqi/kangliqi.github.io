# 两种跳表的实现

- [两种跳表的实现](#两种跳表的实现)
  - [链表法](#链表法)
    - [代码实现](#代码实现)
  - [链表 + 数组法（优化方案）](#链表--数组法优化方案)
    - [代码实现](#代码实现-1)

## 链表法

此方法理解起来容易，但是实现起来稍有点复杂，并且空间利用率没有下面方法高，并不推荐使用

实现思路：

### 代码实现

- 帮助函数

    ```go
    const (
        skipListP = 0.5
        maxLevel  = 16
    )

    // randLevel Generate a rand level in O(1)
    func randLevel() int {
        r := rand.New(rand.NewSource(time.Now().UnixNano()))
        level := 1
        for level < maxLevel && r.Float64() > skipListP {
            level++
        }
        return level
    }

    func max(a, b int) int {
        if a >= b {
            return a
        }
        return b
    }
    ```

- 数据结构

    ```go
    // SkipNode is a skip list node
    type SkipNode struct {
        // extends linked list node
        next *SkipNode
        // down point to
        down *SkipNode

        data int
    }

    // SkipList is a list implement by Linked list
    type SkipList struct {
        // extends from linked list
        heads []*SkipNode

        size       int
        levelCount int
    }

    // NewSkipList new and init a skip list
    func NewSkipList() *SkipList {
        heads := make([]*SkipNode, maxLevel)

        heads[0] = &SkipNode{}
        for i := 1; i < maxLevel; i++ {
            heads[i] = &SkipNode{down: heads[i-1]}
        }
        return &SkipList{heads: heads, levelCount: 1}
    }
    ```

- 查询

    查询操作最主要的是链表右移和下移的操作

    ```go
    // Find a value from skip list
    func (sl *SkipList) Find(value int) *SkipNode {
        node := sl.heads[sl.levelCount-1]
        for node.down != nil || (node.next != nil && node.next.data < value) {
            if node.next != nil && node.next.data < value {
                node = node.next
            } else {
                node = node.down
            }
        }

        if node.next != nil && node.next.data == value {
            return node.next
        }
        return nil
    }
    ```

- 插入

    插入的核心是在链表下移之前判断是否到达需要建立缓存的层，如果到达那就执行插入

    ```go
    // Insert a value to skip list
    func (sl *SkipList) Insert(value int) {
        level := randLevel()

        // new nodes
        newNodes := make([]*SkipNode, level)
        newNodes[0] = &SkipNode{data: value}
        for i := 1; i < level; i++ {
            newNodes[i] = &SkipNode{data: value, down: newNodes[i-1]}
        }

        i := max(sl.levelCount-1, level-1)
        node := sl.heads[i]
        for i >= 0 {
            if node.next != nil && node.next.data < value {
                node = node.next
                continue
            } else {
                if i < level {
                    newNodes[i].next = node.next
                    node.next = newNodes[i]
                }
                if node.down != nil {
                    node = node.down
                }
                i--
            }
        }

        sl.size++
        if sl.levelCount < level {
            sl.levelCount = level
        }
    }
    ```

- 删除

    删除元素的核心是记录查询时链表下移所走过的路径，方便后面执行删除

    ```go
    // Delete a value from skip list
    func (sl *SkipList) Delete(value int) bool {
        backs := make([]*SkipNode, sl.levelCount)

        level := sl.levelCount - 1
        node := sl.heads[sl.levelCount-1]

        for level >= 0 {
            if node.next != nil && node.next.data < value {
                node = node.next
            } else {
                backs[level] = node

                if node.down != nil {
                    node = node.down
                }
                level--
            }
        }

        if node.next == nil || node.next.data != value {
            return false
        }

        // delete cache
        for i := sl.levelCount - 1; i >= 0; i-- {
            if backs[i].next != nil && backs[i].next.data == value {
                backs[i].next = backs[i].next.next
            }
        }

        sl.resizeLevelCount()
        sl.size--
        return true
    }
    ```

## 链表 + 数组法（优化方案）

参考文献：[Skip Lists: Done Right](https://ticki.github.io/blog/skip-lists-done-right/)

实现思路：

### 代码实现

- 数据结构

    ```go
    // Already define in skip_list.go
    // const (
    // 	skipListP = 0.5
    // 	maxLevel  = 16 // Keep this in 2^n, help to generate a rand level
    // )

    // CacheNode is a cache skip list node
    type CacheNode struct {
        // data
        data int

        // Cache nodes
        cacheNodes []*CacheNode
    }

    // CacheSkipList is a list implement by skip cache
    type CacheSkipList struct {
        head       *CacheNode
        size       int
        levelCount int
    }

    // NewCacheSkipList : new and init a CacheSkipList
    func NewCacheSkipList() *CacheSkipList {
        head := &CacheNode{cacheNodes: make([]*CacheNode, maxLevel)}
        return &CacheSkipList{head: head, size: 0, levelCount: 1}
    }
    ```

    - 查询

    查询元素，核心思想是按层来查询，每一层对应数组 `cacheNodes` 相应的下标，`i--`即下移一层，`node = node.cacheNodes[i]` 即链表在当前层进行右移

    ```go
    // Find a value from skip list
    func (csl *CacheSkipList) Find(value int) *CacheNode {
        node := csl.head
        for i := csl.levelCount - 1; i >= 0; i-- {
            // Get node by index level
            for node.cacheNodes[i] != nil && node.cacheNodes[i].data < value {
                // forward at index level
                node = node.cacheNodes[i]
            }
        }
        // continue forward a index at level 0
        node = node.cacheNodes[0]

        // current at level 0, and the node value is >= by value
        if node.data == value {
            return node
        }
        return nil
    }
    ```

- 插入

    插入元素的核心是当到达可以插入的层（随机出来的层数）以后，就执行插入，一直到第`0`层（数据层），即同时完成了全部索引的更新

    ```go
    // Insert a value to skip list
    func (csl *CacheSkipList) Insert(value int) {
        // Already define in skip_list.go
        level := randLevel()
        newNode := &CacheNode{data: value, cacheNodes: make([]*CacheNode, level)}

        node := csl.head
        for i := max(csl.levelCount-1, level); i >= 0; i-- {
            // Find the index that insert node to
            for node.cacheNodes[i] != nil && node.cacheNodes[i].data < value {
                node = node.cacheNodes[i]
            }

            if i < level {
                // Insert the new node at level i
                newNode.cacheNodes[i] = node.cacheNodes[i]
                node.cacheNodes[i] = newNode
            }
        }

        // update levelCount
        if csl.levelCount < level {
            csl.levelCount = level
        }
        csl.size++
    }
    ```

- 删除

    删除元素的核心思想是使用一个数组来记录查询时所走过的路径，实现快速找到要删除的索引

    ```go
    // Delete a value from skip list
    func (csl *CacheSkipList) Delete(value int) bool {
        backs := make([]*CacheNode, csl.levelCount)
        node := csl.head
        for i := csl.levelCount - 1; i >= 0; i-- {
            for node.cacheNodes[i] != nil && node.cacheNodes[i].data < value {
                node = node.cacheNodes[i]
            }

            // record back nodes
            backs[i] = node
        }

        // if next node value not equals than value
        if node.cacheNodes[0] == nil || node.cacheNodes[0].data != value {
            return false
        }

        // remove the node and it's index
        for i := csl.levelCount - 1; i >= 0; i-- {
            if backs[i].cacheNodes[i] != nil && backs[i].cacheNodes[i].data == value {
                backs[i].cacheNodes[i] = backs[i].cacheNodes[i].cacheNodes[i]
            }
        }
        csl.resizeLevelCount()
        csl.size--
        return true
    }
    ```

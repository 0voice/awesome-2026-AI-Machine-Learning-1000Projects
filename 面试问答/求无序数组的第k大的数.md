# 求无序数组的第k大的数（手撕最优版）
## 题目描述
给定一个无序整数数组 `nums` 和整数 `k`，请找出数组中**第 k 大**的元素（注意：不是第 k 个不同的元素，且 k 从 1 开始计数）。
**示例**：
输入：`nums = [3,2,1,5,6,4], k = 2` → 输出 `5`（第2大）；
输入：`nums = [3,2,3,1,2,4,5,5,6], k = 4` → 输出 `4`（第4大）。

## 核心思路：快速选择（QuickSelect）
这是该题的**最优解法**，基于快排的「分区思想」，无需全排序，只定向找目标位置，平均时间复杂度 $O(n)$（最坏 $O(n^2)$，随机选基准可规避），空间复杂度 $O(\log n)$（递归栈）。

### 核心逻辑
1. **分区（Partition）**：选一个基准值，将数组分为「左（>基准）、中（基准）、右（<基准）」三部分（降序分区，方便找第k大）；
2. **定向递归**：
   - 若基准的索引 == k-1（第k大对应索引k-1），直接返回基准；
   - 若基准索引 < k-1，说明目标在右侧，递归处理右侧；
   - 若基准索引 > k-1，说明目标在左侧，递归处理左侧；
3. **优化**：随机选基准，避免有序数组导致的最坏时间复杂度。

## 代码实现（Python 最优版）
```python
import random

def findKthLargest(nums: list[int], k: int) -> int:
    def quick_select(left: int, right: int) -> int:
        # 递归终止条件：区间只有一个元素
        if left == right:
            return nums[left]
        
        # 优化：随机选基准，避免有序数组的最坏情况
        pivot_idx = random.randint(left, right)
        # 将基准交换到区间最右侧（适配分区逻辑）
        nums[pivot_idx], nums[right] = nums[right], nums[pivot_idx]
        
        # 分区：降序排列，返回基准最终位置
        pivot_pos = partition(left, right)
        
        # 判断基准位置是否是第k大（索引k-1）
        if pivot_pos == k - 1:
            return nums[pivot_pos]
        elif pivot_pos < k - 1:
            # 目标在右侧，递归右侧
            return quick_select(pivot_pos + 1, right)
        else:
            # 目标在左侧，递归左侧
            return quick_select(left, pivot_pos - 1)
    
    def partition(left: int, right: int) -> int:
        pivot = nums[right]  # 基准值（区间最右侧）
        i = left - 1         # 大于基准区域的右边界
        
        # 遍历[left, right-1]，将大于基准的放左侧
        for j in range(left, right):
            if nums[j] >= pivot:
                i += 1
                nums[i], nums[j] = nums[j], nums[i]
        
        # 将基准放到最终位置（i+1）
        nums[i+1], nums[right] = nums[right], nums[i+1]
        return i + 1
    
    # 调用快速选择，初始区间是整个数组
    return quick_select(0, len(nums)-1)

# 测试用例
if __name__ == "__main__":
    assert findKthLargest([3,2,1,5,6,4], 2) == 5
    assert findKthLargest([3,2,3,1,2,4,5,5,6], 4) == 4
    assert findKthLargest([1], 1) == 1
    assert findKthLargest([5,2,4,1,3,6,0], 4) == 3
    print("所有测试用例通过！")
```

## 其他解法（对比学习）
### 方法1：排序法（简单但效率低）
```python
def findKthLargest_sort(nums: list[int], k: int) -> int:
    nums.sort(reverse=True)  # 降序排序
    return nums[k-1]
```
- 时间复杂度：$O(n\log n)$（排序的时间）；
- 空间复杂度：$O(1)$（原地排序）；
- 适用场景：小数据量、快速验证结果。

### 方法2：小顶堆法（适合大数据/海量数据）
```python
import heapq

def findKthLargest_heap(nums: list[int], k: int) -> int:
    # 维护一个大小为k的小顶堆，堆顶是堆中最小元素（即当前第k大）
    heap = []
    for num in nums:
        heapq.heappush(heap, num)
        # 堆大小超过k时，弹出堆顶（最小的）
        if len(heap) > k:
            heapq.heappop(heap)
    # 堆顶就是第k大的元素
    return heap[0]
```
- 时间复杂度：$O(n\log k)$（遍历n个元素，每次堆操作$O(\log k)$）；
- 空间复杂度：$O(k)$（堆的大小）；
- 适用场景：大数据量（如内存无法装下整个数组）、流式数据。

## 关键细节解释（面试必说）
1. **为什么随机选基准？**
   若数组有序（如 `[1,2,3,4,5]`），固定选最右侧为基准会导致分区后一侧为空，时间复杂度退化为 $O(n^2)$；随机选基准可将最坏情况概率降到极低。

2. **分区为什么用降序？**
   第k大的元素对应索引k-1，降序分区后，基准位置直接对应「第几大」，逻辑更直观；若用升序，需将k转换为「第n-k+1小」。

3. **各方法对比选型**：
   - 小数据量：排序法（简单）；
   - 常规数据量：快速选择（最优，$O(n)$）；
   - 大数据/流式数据：小顶堆（$O(n\log k)$，省内存）。

## 总结
1. **核心解法**：快速选择（QuickSelect），平均时间 $O(n)$，是该题的最优解，核心是「分区+定向递归」；
2. **优化技巧**：随机选基准规避最坏时间复杂度；
3. **选型原则**：小数据用排序，大数据用堆，常规场景用快速选择。

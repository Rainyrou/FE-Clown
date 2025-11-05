查找中间值（快速选择）：

- 随机选择基准元素
- 遍历所有元素并与基准元素比较，小于基准元素的置于左区，大于基准元素的置于右区
- 分区后基准元素在最终排序后置于正确位置 P
- 若 K < P 则中间值位于左子数组，我们废弃右半部分，只递归左边，若 K > P 则中间值位于右子数组，我们废弃左半部分，只递归右边，若 K === P 即为所求
* 平均时间复杂度 N + N/2 + N/4 + N/8 + ... + 1 ≈ 2N → O (N)
* 空间复杂度 O(N)

查找前 100 条 IP 地址：

- 维护大小为 K=100 的最小堆，堆顶元素即该 100 条 IP 地址中的最小值
- 遍历 1 亿个 IP 地址，若当前 IP 地址小于等于堆顶元素，直接忽略，否则弹出堆顶元素，将当前 IP 地址插入堆中，通过这种方式，堆始终维护着遍历至当前位置的最大 100 条 IP 地址
* 时间复杂度 -> O(NlogK) 空间复杂度 -> O(K)

[数组中的第 K 个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

```js
const findTopK = (nums, k) => {
  if (nums.length <= k) return nums.sort((a, b) => b - a);
  let heapSize = nums.length;
  buildMaxHeap(nums, heapSize);
  for (let i = nums.length - 1; i >= nums.length - k; --i) {
    [nums[0], nums[i]] = [nums[i], nums[0]];
    --heapSize;
    maxHeapify(nums, 0, heapSize);
  }
  return nums.slice(nums.length - k).reverse();
};

const buildMaxHeap = (nums, heapSize) => {
  for (let i = (heapSize >> 1) - 1; i >= 0; --i) maxHeapify(nums, i, heapSize);
};

const maxHeapify = (nums, i, heapSize) => {
  while (i < heapSize) {
    let largest = i;
    let left = i * 2 + 1,
      right = i * 2 + 2;
    if (left < heapSize && nums[left] > nums[largest]) largest = left;
    if (right < heapSize && nums[right] > nums[largest]) largest = right;
    if (i !== largest) {
      [nums[i], nums[largest]] = [nums[largest], nums[i]];
      i = largest;
    } else {
      break;
    }
  }
};
```

`maxHeapify` 维护以索引 i 为根节点的子树满足最大堆性质
`buildMaxHeap` 叶子节点无需调整，因此从最后一个非叶子节点开始调用 `maxHeapify` 函数

判断某 IP 地址是否位于某 1 亿条 IPV4 地址：

1. 数据转换：IPv4 地址本质为 32 位无符号整数，通过位运算转换为整数（字符串存储 1 亿条 _ 12 字节 ≈ 1.2 GB -> 整数存储 1 亿条 _ 4 字节 ≈ 400 MB + 字符串比较 O(K) -> 整数比较 O(1)）
2. 方案：

- 排序数组 + 二分查找：将 1 亿条 4 字节整数置于数组中并进行快排，通过二分查询新 IP 是否位于该有序数组中
- 哈希表：创建桶数组，通过哈希函数将 IP 整数 `x` 映射到桶索引 `index = hash(x) % len`，各个桶存储哈希冲突的 IP 链表；新 IP 转化为 `x` 计算其桶索引 `index = hash(x) % len`，定位到对应的桶，遍历该桶判断是否存在 `x`

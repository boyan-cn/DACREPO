# 二分查找

## （一）Leetcode 题号

- [704.二分查找](https://leetcode.cn/problems/binary-search/)

- [35.搜索插入位置](https://programmercarl.com/0035.搜索插入位置.html)
- [34.在排序数组中查找元素的第一个和最后一个位置](https://programmercarl.com/0034.在排序数组中查找元素的第一个和最后一个位置.html)
- [69.x 的平方根](https://leetcode.cn/problems/sqrtx/)
- [367.有效的完全平方数](https://leetcode.cn/problems/valid-perfect-square/)

## （二）Pa！下意识思路

- 如何 pa！的一下想到二分查找的思路转换：

  - 普通查找：O(n)，当遇到n级别的时间复杂度时，脑子瞬间就要反应一下：有没有什么解法能够把时间复杂度降到O(logn)?

  - 二分查找：O(logn)
    - 使用前提：有序数组、数组中无重复元素（否则返回下标不是唯一的）


## （三）算法思路：二分查找

​	找准查找时区间定义的关键：“不变量”，需要遵循“循环不变量”规则，处理每次循环查找区间的边界：	

- [left, right]：每次搜索都包含左右两端位数值的比较
- [left, right)：每次查找不包含右端位数值的比较

​	现在我下意识习惯的写法是第一种，更为直观；

## （四）题解

### 704. 二分查找

本题是最经典的二分查找原始题型，需要注意的点如下，请见注释：

```java
class Solution {
    public int search(int[] nums, int target) {
      	// 优化：避免当 target 不在 nums[] 区间内时多次循环运算的浪费
        if( target < nums[0] || target > nums[nums.length - 1]) return -1;
        // 定义辅助变量
        int n = nums.length;
        int left = 0;
        int right = n - 1;
        int middle = (left + right) / 2; // 位序偏取小
        int result = -1;
        // 开始循环二分查找
        while (left <= right) {
            // 优化：把==放在第一个，减少语句执行条数
            if (target == nums[middle]) {   
                return middle;
            } else if (target < nums[middle]) { 
              	// 每个小循环中都有：
              		// 1.判断
                	// 2.更新指针位 * 3种情况
                right = middle - 1;
                middle = (left + right) / 2; 
            } else if (target > nums[middle]) {
                left = middle + 1;
                middle = (left + right) / 2; 
            }  
        }
        return result;
    }
}
```




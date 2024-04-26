# Leetcode 59.螺旋矩阵II

> [!TIP]
>
> - 本题关键还是在**转圈**的逻辑，在二分搜索中提到的区间定义，在这里又用上了。

Leetcode 题目跳转： [59.螺旋矩阵II](https://leetcode.cn/problems/spiral-matrix-ii/)

Leetcode 题解跳转：[lc[59]. 螺旋矩阵II｜直接模拟（坚持循环不变量原则）](https://leetcode.cn/problems/spiral-matrix-ii/solutions/2757490/lc59-luo-xuan-ju-zhen-iizhi-jie-mo-ni-ji-cfw5)

<img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/lc59.png" alt="image-20240426185142117" style="width:70%;" />



## 题解

### 解法：直接模拟（坚持循环不变量原则）

#### 解题思路

- 由外向内一圈一圈这么画下去：每圈中都要画四条边，每画一条边都要坚持一致的**左闭右开**（图中所示），或者左开右闭的原则，这样这一圈才能按照统一的规则画下来。

- 图解：

  <img src="https://cdn.jsdelivr.net/gh/boyan-uni/pic-bed/img/lc59-%E7%9B%B4%E6%8E%A5%E6%A8%A1%E6%8B%9F%E5%BE%AA%E7%8E%AF%E4%B8%8D%E5%8F%98%E9%87%8F-%E5%9B%BE%E8%A7%A3.png" alt="image-20240426192206017" style="width:80%;" />

  

#### 复杂度

- 时间复杂度：O(n^2)，遍历n*n个位置；
- 空间复杂度: O(n)，新建空白矩阵存储结果；



## Code

### 直接模拟

```java
// 直接模拟（矩阵的生成过程）：坚持循环不变量原则
class Solution {
    public int[][] generateMatrix(int n) {
        // 初始化辅助变量
        int[][] matrix = new int[n][n]; // 空白矩阵
        int loop = 1;                   // 计圈数，1-(int n/2)
        int x = 0, y = 0;               // x 行，y 列
        int startX = 0, startY = 0;     // 每圈起始位置（因为是矩阵所以很好更新）
        int count = 1;                  // 往矩阵中对应位置存入的数值；
        // 开始循环
        while (loop <= (n >> 1)) {      // n >> 1 位运算，比 /2 计算速度更快
            // 顶部
            for (y = startY; y < n - loop; y++) {
                // 赋值
                matrix[startX][y] = count++;
            }
            // 右侧
            for (x = startX; x < n - loop; x++) {
                // 赋值
                matrix[x][y] = count++;
            }
            // 底部
            for (y = n - loop; y > startY; y--) {
                // 赋值
                matrix[x][y] = count++;
            }
            // 左侧
            for (x = n - loop; x > startX; x--) {
                // 赋值
                matrix[x][y] = count++;
            }
            // 进入下一圈，更新数据
            loop++;
            startX++;
            startY++;
        }
        // 如果 n 是奇数，需要对矩阵中间位置单独存值；
        if (n % 2 == 1) {
            matrix[n/2][n/2] = count;
        }
        // 返回结果
        return matrix;
    }
}
// 时间复杂度：O(n^2)，遍历n*n个位置；
// 空间复杂度：O(n)，新建空白矩阵存储结果；

作者：BOYAN
链接：https://leetcode.cn/problems/spiral-matrix-ii/solutions/2757490/lc59-luo-xuan-ju-zhen-iizhi-jie-mo-ni-ji-cfw5/
来源：力扣（LeetCode）
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```




























































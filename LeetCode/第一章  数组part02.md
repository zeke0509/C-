# 209.长度最小的子数组
题目描述：
给定一个含有 `n` 个正整数的数组和一个正整数 `target` **。**

找出该数组中满足其总和大于等于 `target` 的长度最小的 **子数组** `[numsl, numsl+1, ..., numsr-1, numsr]` ，并返回其长度**。**如果不存在符合条件的子数组，返回 `0` 。
## 一刷总结
### 暴力求解法中的问题
1. 我的错误
   错误理解：如果加了某一轮的完整的sum，当```sum<target```的时候，我直接return 0。这是不对的。因为尽管这一轮的```sum<target```，但是上一轮遍历过后的```sum可能>target```，不应该直接return0
2. break跳出for循环的那一次，不会再执行for中的第三句指令j++

### 滑动窗口法
1. 算法思路：
   常规方法就是移动后面的指针求和，直到满足条件就返回当前数组的长度。那么我们可以想到：当满足条件的时候，我们移动初始指针的位置，就可以实现找到下一个满足条件的数组。
   - 维护两个指针，第一个快指针，遍历所有元素并求和
   - 当加和满足条件的时候，向后移动一位慢指针，并把加和减掉数组中的首个元素。
2. 错误点
   - 三目运算符是是一个带有返回值的条件判断
     ```return m == INT_MAX ? 0: m;```
     0的后面是冒号
   - `for`内层的判断语句是`while`而不是`if`：
	   假如索引0,1,2,3满足条件了，那么接下来，我要求快指针和慢指针怎么动？
	   答：此时`fastP = 3, slowP = 0`，接下来`fastP`==保持不变==，`slowP++`，这就要求内部循环一定是`while`；如果是`if`那么就会直接进入下一次`for`循环，导致`fastP`已经+1了。
   - 这种取最小值的小技巧：先令`res = INT_MAX`，然后在结尾补充`res = INT_MAX`的真实结果。

# 59. 螺旋矩阵II
## 一刷总结
### 算法思路：
1. 找到循环不变量
   - 以遍历一圈为外层循环：
     第一圈：(1,1) -> (1,n) -> (n,n) -> (n,1) -> (1,1)
     第二圈（令offset = 1）：(1+offset, 1+offset)  -> (1+offset,n-offset) -> (n-offset,n-offset) -> (n-offset ,1+offset) -> (1+offset,1+offset)
     那么说明外层循环只需要一个循环不变量offset就可以完美遍历
   - 内存循环为：一圈内分别计算四条边，同样保证循环不变量的特性，那么每条边保证左闭右开
   - 所以循环不变量只需要一个offset足矣
1. 处理奇数圈：可以开始把所有元素都置为n^2 

### 代码实现
```
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> mat(n, vector<int>(n, n*n));
        int offset = 1;
        int loop = n/2;
        int count = 1;
        while (loop--) {
            for (int j = offset - 1; j < n - offset; j++) mat[offset-1][j] = count++;
            for (int i = offset - 1; i < n - offset; i++) mat[i][n-offset] = count++;
            for (int j = n - offset; j > offset - 1; j--) mat[n - offset][j] = count++;
            for (int i = n - offset; i > offset - 1; i--) mat[i][offset - 1] = count++;
            offset++;
        }
  
        return mat;
    }
};
```
# 开发商购买土地
1. 题目链接：[44. 开发商购买土地（第五期模拟笔试）](https://kamacoder.com/problempage.php?pid=1044)
# 704. 二分查找
## 一刷总结
1. 二分查找适用于：
   - 有序数组
   - 无重复元素
2. 注意事项
   - 索引target定义在left、right区间，一般选择左闭右开（左闭右闭也可以）
     以左闭右开为例：left = 0，right = numsize
3. 错误点
   - 对于```if (nums[middle] > target)      right = middle;```
     我原来的思考是：如果数组长度为10，那么第一次二分后middle = 5。若更新```right = middle = 5, 对应的左闭右开区间 [0,5) 正好能索引到前5个元素，正好是一半```。然而这样的思考是不严谨的。
     正确的思考方式是：if条件语句中只判断了```nums[middle]>target``` 说明中间元素比目标值大，那么目标中一定出现在中间元素左侧区域（且不包含中间元素），所以使用```rihgt = middle = 5``` 对应的右开区间正好是不包括中间元素的区间。
     同理，如果满足```nums[middle] > target```，那么target会出现在中间元素（不包括中间元素）往右的区间中，因为是左闭，所以要令```left = middle+1```，才能去除中间元素
   - 引用vector：```vector<int> &nums```

# [27. 移除元素](https://leetcode.cn/problems/remove-element/description/)
## 一刷总结
### 算法思路
1. 常规想法就是两层for循环，第一个for循环遍历判断数组中元素是否与目标值相等；第二个for循环来将后面的元素都向前移动一位。
2. 而双指针可以有效解决：第二层for循环依赖第一层for循环的题目。
3. 思路：当快指针指向的元素 不等于 要删除的元素，那么快指针对应的值是我们想要的，所以赋值给慢指针。

# 977. 有序数组的平方
## 一刷总结
### 算法思路
1. 因为原来的数组已经排好了序，利用最大的数据一定出现在数组头或尾来构建双指针
### 我写的代码（正确）
```class Solution {

	public:
	
	    vector<int> sortedSquares(vector<int>& nums) {
	
	        int len = nums.size();
	
	        int indexS = 0;
	
	        int indexE = len - 1;
	
	        vector<int> results(len);
	
	        while (indexS <= indexE) {
	
	            if (abs(nums[indexS]) >= abs(nums[indexE])) {
	
	                results[len - 1] = nums[indexS] * nums[indexS];
	
	                indexS++;
	
	                len--;
	
	            } else {
	
	                results[len - 1] = nums[indexE] * nums[indexE];
	
	                indexE--;
	
	                len--;
	
	            }
	
	        }
	
	  
	
	        return results;
	
	  
	
	    }
	
	};
```
### 改进措施
1. while中的indexS和indexE都是要更新的，所以可以考虑用for循环
2. 注意定义新的容器results的方法：```vector<int> results(n)```
   其中是小括号，而不是中括号
3. 改进后的代码
	```
	class Solution {
	public:
	    vector<int> sortedSquares(vector<int>& nums) {
	        int n = nums.size();
	        vector<int> results(n);
	        for (int i = 0, j = n - 1, k = n - 1; i <= j; ) {
	            if (nums[i] * nums[i] >= nums[j] * nums[j]) {
	                results[k--] = nums[i] * nums[i];
	                i++;
	            } else {
	                results[k--] = nums[j] * nums[j];
	                j--;
	            }
	        }
	        return results;	
	    }
	};
	```
	- for循环中，可以将最后一个条件置空。且注意第一个条件的声明语句，只能写一句！即
	  ``` int i = 0, j = n - 1, k = n - 1;```是正确的
	    ```int i = 0, int j = n - 1, int k = n - 1```是错误的
		
	
# [454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)
## 算法思路
- 分辨出这道题是：四个不同的数组相加。普通方法是四个for循环。
- 我们相当可以分成两组，先算前两个数组的和，并记录出现次数；然后遍历后两个元素的和，然后去前两个数组中查找满足条件的次数。
- 将每次满足条件的次数递加，得到结果
## 注意事项
- 对于```map类型的 results，使用 results[count]++是可以实现从0开始递加的```
  因为map的特殊机制，如果找不到count对应的值，会自动置0

# [15. 三数之和](https://leetcode.cn/problems/3sum/)
## 算法思路
- 这道题是在同一个数组中找到符合条件的元素，考虑双指针法。
  为什么不用哈希法呢？
  如果在同一个数组中遍历，优先推荐双指针法；
  如果很多个数组遍历，可以考虑哈希法去查找。
- 用双指针之前要先对数组排序
## 代码实现
```
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> results;
        for (int i = 0; i < nums.size() - 2; i++) {
            if (i > 0 && nums[i] == nums[i-1]) continue;  // 不要用i++
            cout << "i = " << i << endl;
            int left = i + 1;
            int right = nums.size() - 1;
            while (left < right) {
                if (nums[i] + nums[left] + nums[right] > 0) right--;
                else if (nums[i] + nums[left] + nums[right] < 0) left++;
                else {
                    cout << "i = " << i << ", left = " << left << ", right = " << right << endl;
                    if (left == i + 1 || nums[left] != nums[left - 1]) {
                        results.push_back({nums[i], nums[left], nums[right]});
                    }
                    right--;
                    left++;
                }
            }
        }
        return results;
    }
};
```
## 注意事项
- 去重是一个难点
  - 对于i的去重：无需考虑是否已经得到了结果，只要下一次的```nums[i]```与上一次相等，我就无需再计算当前i对应的情况，因为这已经在上一次计算过了。举个例子如下：
	```
	nums = [-2, -2, 0, 2, 4];
	第一次遍历（i=0）：满足条件的情况有，(-2, -2, 4)、(-2, 0, 2)
	那么我第二次(i=1)是否还需要遍历呢？
	发现从(i=1)遍历其实就是对应上一次(i=0, left >= 2)之后的所有情况，所以无需再计算。
	```
  - 对于left和right的去重：不能像对i一样，即发现```left[i] = left[i-1]```就去重，这样会很麻烦，因为我们本来就不仅要在满足条件的循环中修改left的值，还要再不满足条件的循环中修改left的值，这样再去判断```left[i] = left[i-1]```就很累。所以考虑如下思路：
    当我们发现此刻的```left[i] != left[i-1]```，那就保留这个结果进入容器！但是又有一个问题，left的初始值是i+1，那么此时left没有前一刻的结果，怎么办？
    那我们就再加入这一种情况进入判断条件！考虑到：如果初始情况满足条件，那么我们必须要这种初始情况而无需考虑去重。有如下：
    ```if (left == i + 1 || number[left] != number[left - 1]) {把数组加入容器}```
- 排序的代码
  ```sort (vec.begin(), vec.end());```

## 优化
- i遍历是到第nums.size() - 2 个元素就停止
- 剪枝操作：在某次i内，如果发生以下情况：
  整个数组的最小值都已经比0大了，后面无论如何都不会满足条件，直接break
  ```nums[i] + nums[i+1] + nums[i+2] > 0;```
  当前确定i下面的遍历情况的最大值都小于0，所以直接遍历下一个i，continue
  ```nums[i] + nums[nums.size() - 2] + nums[nums.size() - 1] < 0```
优化后的完整代码
```
class Solution {

public:

    vector<vector<int>> threeSum(vector<int>& nums) {
        sort(nums.begin(), nums.end());
        vector<vector<int>> results;
        for (int i = 0; i < nums.size() - 2; i++) {
            if (i > 0 && nums[i] == nums[i-1]) continue;  // 不要用i++
            if ((nums[i] + nums[i+1] + nums[i+2]) > 0) break;
            if ((nums[i] + nums[nums.size() - 2] + nums[nums.size() - 1]) < 0) continue;
            cout << "i = " << i << endl;
            int left = i + 1;
            int right = nums.size() - 1;
            while (left < right) {
                if (nums[i] + nums[left] + nums[right] > 0) right--;
                else if (nums[i] + nums[left] + nums[right] < 0) left++;
                else {
                    cout << "i = " << i << ", left = " << left << ", right = " << right << endl;
                    if (left == i + 1 || nums[left] != nums[left - 1]) {
                        results.push_back({nums[i], nums[left], nums[right]});
                    }
                    right--;
                    left++;
                }
            }
        }
        return results;
    }
};
```
# [18. 四数之和](https://leetcode.cn/problems/4sum/description/)
## 算法思路
- （类似于三数之和）首先，观察是几个数组？
  1个数组，那么用双指针法而不是哈希法。
- 双指针：只能判断是否保留某两个数，我们这里让双指针指向num3和num4
- 那么我们需要用两层for循环来遍历num1和num2
- 剪枝：两层for循环，所以需要一共两级剪枝
## 注意事项
- 勿忘排序！
- 由于测试数据很大，int类型已无法满足。所以我们要使用long long类型
	```
	long long sum = (long long)num[i] + num[j] + num[left] + num[right]; // 正确
	long long sum = (long long)(num[i] + ...)      // 错误
	```
	注：第一种形式，先将后面4个num从int转换成long long类型，再完成相加；正确
		第二种形式，先用int型相加，得到int型的和（此时可能已经溢出），再转换成long long
- 第二次剪枝，前两个数是```nums[i] + nums[j]， 而不是 nums[i] + nums[i+1]```；第三个数是```nums[j+1]，而不是nums[i+2]```
- 注意剪枝中，一种情况是break，另一种情况是continue
- 去重操作：若是循环内的第一次循环，是不去重的。所以要注意去重条件，例如：
  ```if ((left == j + 1 || nums[left] != nums[left - 1]))```
# 242.有效的字母异位词
题目链接：[242](https://leetcode.cn/problems/valid-anagram/)
## 算法思路
- 只统计小写字母，数据量小且连续性好：使用数组类型的哈希表计数字符串s每个字母出现的次数
- 第二次只需要遍历字符串t，在count哈希数组中减去出现过的字母
- 在判断count数组是否为0
## 注意事项
- 可以使用 for (const char& c : s)方式遍历

# [349. 两个数组的交集](https://leetcode.cn/problems/intersection-of-two-arrays/)
## 算法思路
1. 原来的想法：
   用两个集合分别去掉nums1和nums2中的重复元素，然后再去找到交集。这样做其实麻烦了，因为去重一次其实就可以
2. 改进后的思路：
   用一个mySet1记录nums1中的元素（去重）
   然后遍历nums2中的元素，如果在mySet1中出现过，就说明我们想要这个元素，输出它。那么如何能够保证我们在nums2中遍历到重复元素而只输出它一次呢？
   这就需要我们在mySet1找到这个元素并把它删除掉，这样下次nums2再找到该元素的时候，mySet1中没有了，就不会再输出了。关键代码如下：
	```
	for (int& x : nums2){
		if (mySet1.erase(x)) count.push_back(x);
	}
	```
## 注意事项
- 在集合中查找一个元素是否出现过
  正常思路：```if (mySet1.find(x) != mySet1.end())```
  还可以考虑：```if (mySet1.erase(x));  // 如果删除成功，返回1```

# [202. 快乐数](https://leetcode.cn/problems/happy-number/)
## 算法思路
- 首先需要思考：快乐数的定义，到底在讲什么？
  无限循环？重点！在循环上，这是一个很好的提示！它不是无限不循环！
- 我们发现快乐数的计算，会把原来的数变小。即99->2x81; 999->3x81;所以再大的数也会被化简，这也就验证了如果不满足快乐数，那它一定是一个循环！
- 对于判断是否为循环，有下面两种办法
  1. 快慢指针法
  2. 哈希法（略占空间）
## 注意点
- 计算一个整数的各位数字，是一个固定套路
	```
	vector<int> getDig(int x) {
	    vector<int> results;
	    while (x) {
	        results.push_back(x % 10);   // 计算最后一位
	        x /= 10;                     // 去掉最后一位
	    }
	    return results;
	}
	```
- 如果slow为1返回true，否则返回false的简化写法
	```return (slow == 1);```
- 初始情况，我们是需要将slow和fast都置为n的，且它至少要循环一次，才能判断。这也就说明用do-while循环更合适。
## 哈希法代码实现
```
class Solution {

public:
    bool isHappy(int n) {
        unordered_set<int> mySet;
        do {
            mySet.insert(n);
            n = getSum(n);
            // cout << "平方后的和 = " << n << endl;
        } while (mySet.find(n) == mySet.end());
        return (n == 1);
    }

private:
    int getSum(int n) {
        int sum = 0;
        while (n) {
            sum += (n % 10) * (n % 10);
            n /= 10;
        }
        return sum;
    }
};
```
需要注意：这里用了do-while循环，因为无论第一次插入n进哈希表是一定要做的事情。再注意while内的条件判断， == end() 才表示未找到元素。

# [1. 两数之和](https://leetcode.cn/problems/two-sum/)
## 算法思路
- 常规思路：两层for循环，但是时间复杂度会为O(n^2)
- 能否通过一次遍历就解决问题呢？有！遍历一次的过程，我再去查找之前遍历的元素中有没有我的期望元素。但是怎么去查找呢？
  哈希方法可以有效解决查找问题。
- 因为我不仅要找到期望值，还有返回索引，那么我可以用unordered_map实现。

## 注意事项
- 以`nums[i]`为`key`是否担心重复的`nums`怎么办？
	先判断第二个`nums[i]`是否满足条件，如果不满足就不用考虑这个重复数字了，因为题目中已经说明只有一种答案。
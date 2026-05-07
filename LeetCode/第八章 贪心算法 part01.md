# [376. 摆动序列](https://leetcode.cn/problems/wiggle-subsequence/description/)
## 贪心算法
### 算法思路
- 遍历第1~n-1个数据，维护两个值`now = nums[i] - nums[i - 1]`、`next = nums[i + 1] - nums[i]`；
- 如果`next = 0`直接`continue`不处理，也不更新`now = next`
- 只有在满足极值条件的时候，才会更新`now = next`
- 对于第一个数据的处理：可以加上一个初始`now`= 0，然后再满足极值条件的判断中加上`now = 0`，这是因为`next`不可能为0，因为遇到0就直接跳
### 代码实现
```
class Solution {

public:

    int wiggleMaxLength(vector<int>& nums) {

        int n = nums.size();

        if (n < 2) return n;

        int nowDiff = 0;

        int nextDiff;

        int count = 1;

        for (int i = 0; i < n - 1; i++) {

            nextDiff = nums[i+1] - nums[i];

            if (nextDiff == 0) continue;

            if ((nowDiff <= 0 && nextDiff > 0) || (nowDiff >= 0 && nextDiff < 0)) {

                count++;

                nowDiff = nextDiff;

            }

        }

        return count;

    }

};

```
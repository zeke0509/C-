# [150. 逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/submissions/645048208/)
## 算法思路
- 两个数字遇见一个操作符，做一个消除的操作
## 注意事项
- 类型转换（str -> int）：```x = stoi(s);```

# [239. 滑动窗口最大值](https://leetcode.cn/problems/sliding-window-maximum/submissions/645083403/)
## 算法思路 
- 分析：滑动窗口的题目可以用队列或双指针（即暴力求解），我们希望队列中只维护最大值和可能成为最大值的值即可，那么其实是一个单调队列。
  假设要入栈的元素比左侧的历史元素小，那我们就不要这个元素，所以这是一个单调减的队列。
## 算法细节
- 如何维护队列内的元素是单调减的？
  若要入队列的元素比尾部的值大，就弹出原队列的尾部值。继续比较原队列新尾部的值并弹出，直到原队列新尾部的值大于入队列的元素，让新元素正常入队列。
  要入队列的元素如果比原队列尾部的值小，就不要让它入队列。
- 这种算法保证滑动窗口内的最大值始终在队首
- 何时该弹出队首元素呢？
  可以为pop()函数加一个参数pop(int x)，比较第i次入队该弹出的元素值与此时队首元素值，若相等则弹出，若不相等则不执行任何操作。
我们发现，我们既需要队尾弹出又需要队首弹出，所以想到用双端队列。

## 代码实现
```
class Solution {
private:
    class MyQueue{
    public:
        deque<int> dq;
        // 将新加入元素为之前所有 <= 新加入元素的元素 弹出；保证单调减
        void push(int value) {
            while (!dq.empty() && value > dq.back()) dq.pop_back(); // 这个位置不能写成>=，分析见笔记
            dq.push_back(value);
        }
        // 只有当 参数=队首元素 才弹出队首元素
        void pop(int value) {
            if (value == dq.front()) dq.pop_front();
        }
  
        // 返回队列最大值
        int front() {
            return dq.front();
        }
    };
public:
    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
        vector<int> res;
        MyQueue q;
        for (int i = 0; i < k; i++) {
            q.push(nums[i]);
        }
        res.push_back(q.front());
        for (int i = k; i < nums.size(); i++) {
            q.pop(nums[i - k]);
            q.push(nums[i]);
            res.push_back(q.front());
        }
  
        return res;
    }
};
```
  
## 注意点
- push函数中，我们是要弹出前方小于value的元素（单调不增），还是弹出小于等于value的元素呢（单调减）？
  这个与我们的pop规则是对应的。我们的pop规则：当首元素等于要此时该弹出元素的时候，弹出首元素。所以我们在push的时候应该保留原有和此时值相等的最大值，保证新的最大值不被提前弹出。

## 算法改进
- 可以改进的地方：上面发现我们在弹出首元素的方法上，是为pop添加了一个参数去比较首元素与该弹出元素的值。这是因为，我们判断是否弹出的条件本质上是判断i满足什么条件，那么我们能否直接用i作为弹出条件呢？
  可以！那就需要我们在队列中存入信息i而不是nums\[i\]。
- 完整代码如下：
	```
	class Solution {
	public:
	    vector<int> maxSlidingWindow(vector<int>& nums, int k) {
	        deque<int> q;
	        vector<int> res(nums.size() - k + 1, 0);
	        for (int i = 0; i < nums.size(); i++) {
	            while (!q.empty() && nums[i] > nums[q.back()]) q.pop_back();
	            q.push_back(i);     // 注意：这里队列放的nums的索引
	            int left = i - k + 1;   // 加入i后，队列的左端点
	            if (q.front() < left) q.pop_front();
	            if (left >= 0) res[left] = nums[q.front()];
	        }
	        return res;
	    }
	};
	```
## 注意事项
- 队列获取首元素使用：```q.front()```；栈是```st.top()```
## 算法理解
其实这是一个构建单调队列的过程：若新加入元素破坏了队列单调性，就队列中原有元素尾部删除掉，直至满足单调性；若新加入元素不破坏队列单调性，则直接加入队列。

## 二刷建议
每次在窗口中新加入元素后，那么在它之前比这个新元素小的值都可以不要。所以这时一个单调不增的队列。所以这是一个构建单调队列的问题。

## 二刷思路
我们把这道题分解成，进行`n`次移动窗口，找到`n+1`个最大值。每次移动窗口可以被分解为如下三步（在已知我要使用单调队列的前提下）：
（1）`pop`掉过期元素。当队头元素=过期元素时，进行`pop`操作；否则无需操作，因为并没有将该过期元素维护在队列中
（2）`push`新元素。弹出队列中尾部所有小于新元素的元素
（3）获取窗口最大值，即队头元素。



# [347.前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/description/)
## 算法思路
### 堆简介
堆分为：大顶堆（父节点>=左右孩子）、小顶堆（父节点<=左右孩子）
实现：完全二叉树
### 算法
我们只需要维护前k个频率最高的元素，统计频率使用“计数哈希”。
因为我们需要统计每个元素的频率，这个元素很大也可能是离散的，故选择map哈希结构。键为元素，值为频率。
正常的排序方法：把unordered_map中的元素复制到vector中，然后调用sort排序。
然后我们并不需要重新排序所有哈希的值，而是只维护前k个就可以，所以想到使用优先级队列，队列中保持只有k个元素。（优先级队列本质是堆）
思考：是用大顶堆还是小顶堆呢？
每次插入的值如果满足条件，我们应该在堆中弹出最小值（根节点），所以我们应该让根节点是最小值，所以用小顶堆。
比较规则：因为我们传入堆中的是键值对，比较规则无法用functional头文件中自带的greater比较规则，所以我们需要自定义比较规则。设计比较规则的原则：当compare为true时，弹出后者。
### 代码实现
```
class Solution {
private:
    struct Comp {
        bool operator()(const pair<int, int>& a, const pair<int, int>& b) {
            return a.second >= b.second;
        }
    };
public:
    vector<int> topKFrequent(vector<int>& nums, int k) {
        vector<int> res(k);
        unordered_map<int, int> mp;
        priority_queue<pair<int, int>, vector<pair<int, int>>, Comp> q;
        for (int x : nums) mp[x]++;
        for (auto& it: mp) {
            q.push(it);
            if (q.size() > k) q.pop();
        }
        for (int i = k - 1; i >= 0; i--) {
            res[i] = q.top().first;
            q.pop();
        }

        return res;
    }
};
```
### 注意事项
- 定义小顶堆时候，使用声明语法：```priority_queue<T, Container, Compare> q;```
  需要注意以下几点
  声明后面接的是```<...>而不是(...)```
  Compare 是一种类型（可以自定义），但是这个类型要满足：有operator()内置方法，且返回值为bool类型。一般都是通过==结构体==实现（结构体可以看作一种数据类型）
  Comapre 的值为true时，说明后者被弹出优先级高
- vector的声明可以直接用```vector<int> res(k)```，就可以表示大小为k
- 优先级队列只能获取顶部元素```q.top()```；不能像真正的队列一样使用front

## 二刷错误
1. `priority_queue`的第二个模板参数要求的是底层顺序容器，通常是`vector`。所以是`vector<pair<int, int>>`而不是`unordered_map<int, int>`
2. `mp[val]++`。如果`mp[val]`不存在，会自动初始化一个`key = val, value = 0`的键值对
  
# [232. 用栈实现队列](https://leetcode.cn/problems/implement-queue-using-stacks/description/)
## 算法思路
通过两个栈拼接成一个整体的队列，第一个栈为进栈口，第二个栈为出栈口。例如：
按照a,b,c,d进入队列（由两个栈构成）
第一个栈（进入队列）：【d, c, b, a
第二个栈（弹出队列）——为实现按照dcba的顺序弹出，让第一个栈的元素进入
则此时，第一个栈为空栈，第二个栈为【a, b, c, d
- 对于弹出队列：当出栈为空时，要把进栈的所有元素都弹出并压入出栈，才会保证顺序
## 代码实现
```
class MyQueue {
private:
	stack<int> inSt, outSt;
	void in2out() {
		while (!inSt.empty()) {
			outSt.push(inSt.top());
			inSt.pop();
		}
	}

public:
	void push(int x) {
		inSt.push(x);
	}
	
	int pop() {
		if (outSt.empty()) in2out();
		int res = outSt.top();
		outSt.pop();
		return res;
	}
	
	int peek() {
		if (outSt.empty()) in2out();
		int res = outSt.top();
		return res;
	}
	
	bool empty() {
		return (inSt.empty() && outSt.empty());
	}
};
```

# [225. 用队列实现栈](https://leetcode.cn/problems/implement-stack-using-queues/description/)
## 算法思路
- 因为队列没办法改变出入的顺序，所以我们考虑通过在push一个元素前。把push的元素push进备份队列中，然后把现有主队列全部拷贝在备份队列中，然后再交换主副队列，这样就能实现新加入的元素被移动到了队头。
## 进阶：只用一个队列完成
- 延续上一个算法的思路：push新元素到一个空队列中，然后再进行一次拷贝原队列。那么我们就可以通过如下方式，实现上面操作：
  将新元素添加到原队列的队尾，然后依次弹出并重新压入前面size-1个元素，这样也能够实现新加入的元素来到了队首。

# [20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/description/)
## 算法思路
- 本质：邻项消除类型 —— 可以用栈来解决
- 栈解决问题的具体实现
  遍历字符串，遇见左括号入栈。
  如若遇见右括号，检查栈顶元素是否为对应左括号：若否或者栈为空，则返回false
- 优化1：这里可以优化的点是，遇见左括号，把对应的右括号入栈，这样会极大简化判断的条件。
- 优化2（剪枝）：如果字符串的长度为奇数，一定是无效括号
- 注意事项
  在遇见右括号的时候，不能仅判断是否对应，要记得判断栈为空的情况
- 完整代码
	```
	class Solution {
	public:
	    bool isValid(string s) {
	        if (s.size() % 2) return false;
	        stack<char> st;
	        for (char c : s) {
	            if (c == '(') st.push(')');
	            else if (c == '{') st.push('}');
	            else if (c == '[') st.push(']');
	            else {
	                if (st.empty() || st.top() != c) return false;
	                st.pop();
	            }
	        }
	        return st.empty();
	    }
	};
	```

## 算法优化
- 可以用哈希表来储存“左右括号”的映射关系，因为括号是字符，不连续，所以用```unordered_map类型```
- 完整代码：
	```
	class Solution {
	private:
	    unordered_map<char, char> mp = {{'(', ')'}, {'{', '}'}, {'[', ']'}};
	public:
	    bool isValid(string s) {
	        if (s.size() % 2) return false;
	        stack<char> st;
	        for (char ch : s) {
	            if (mp.contains(ch)) st.push(mp[ch]);  // 若ch为左括号，压对应右括号入栈
	            else {
	                if (st.empty() || st.top() != ch) return false;
	                st.pop();
	            }
	        }
	        return st.empty();
	    }
	};
	```

# [1047. 删除字符串中的所有相邻重复项](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/description/)
## 算法思路
- 看到这种相邻元素间的操作，就想到用栈的思路来解题。那么对于这道题如何解呢？
- 遍历过的元素入栈，检测此次遍历的元素与栈顶元素是否相等，进行操作。
- 最终逆序返回（即栈底~栈顶）
- 注意事项
  未声明字符串长度，就不能用索引赋值。如```string s;  s[0] = 'a';是错误的```
## 算法优化
- 字符串天生就可以作为栈！！！那么这题直接把字符串看作栈将简单非常多
- 遍历元素进入字符串的尾端（不选头端，是保证新字符串的顺序与原字符串一致）
- 比较即将遍历元素与字符串尾端元素是否相等进行操作
## 完整代码
```
class Solution {
public:
    string removeDuplicates(string s) {
        string results;
        for (char ch : s) {
            if (results.empty() || results.back() != ch) results.push_back(ch);
            else results.pop_back();
        }
        return results;
    }
};
```
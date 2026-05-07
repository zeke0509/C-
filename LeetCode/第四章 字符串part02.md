# [151. 反转字符串里的单词](https://leetcode.cn/problems/reverse-words-in-a-string/description/)
## 算法思路
- 若想降低时间复杂度和空间复杂度，需要：避免使用erase函数；不要开辟辅助空间记录新字符串，而是直接在原字符串上实现原地反转。
- 分为如下三步
  - 先获得满足空格条件的、正序的字符串
    本质是移除元素，即移除多余的空格。那么很合理的使用双指针法移除元素。
    整体流程：
    快指针遍历整个字符串；慢指针记录新字符串。
    快指针识别到“非空格”元素，认为这时是一个单词的开始。将此刻的slow赋值为空格（为保证，每个单词开头有空格；需注意：起始单词不要空格，故单独处理）并将后续连续的非空格元素赋值给后续连续slow指针。
  - 原地反转所有的字符串
  - 以识别空格为单词分界线，分别原地反转单词。
    反转正常reverse就行，那么参数如何设置呢？
    识别空格是能够得到空格的索引，那么我们就在识别到空格的时候更新参数上下限就可以。
## 注意事项
- 编写删除空格的函数，需要传入原字符串的引用，才能实现原地修改。
  删除空格后，需要更新新的字符串长度。
  因为要处理连续的字符，故在识别到空格后，需要while循环，而while循环的判断条件是为了识别该单词处理完毕。那么对于一般单词来讲，这个条件就是”识别到空格“, 即```while (s[i] != ' ')```而对于末尾没有空格的最后一个单词，这个条件会一直成立，所以需要额外加一个条件是```while (s[i] != ' ' && i < s.size())```。

## 代码实现
```
class Solution {
public:
    string reverseWords(string s) {
        removeSpace(s);
        ranges::reverse(s);
        int indexS = 0;
        int indexE;
        // cout << "s = " << s << endl;
        for (int i = 0; i <= s.size(); i++) {
            if (s[i] == ' ' || i == s.size()) {     // s.size()指向末尾元素的下一位
                indexE = i;
                reverse(s.begin() + indexS, s.begin() + indexE);
                indexS = i + 1;
            }
        }
        return s;
    }
  
private:
    void removeSpace(string& s) {
        int slow = 0;
        for (int i = 0; i < s.size(); i++) {
            // 遇见单词的开头开始处理
            if (s[i] != ' ') {
                if (slow != 0) s[slow++] = ' ';        // 单词的开头留有一个空格(除了首单词)
                // 连续处理一个单词的字符
                // 当s[i]为空格/到末尾，表示该单词处理完了
                while (s[i] != ' ' && i < s.size()) {
                    s[slow++] = s[i++];
                }
            }
        }
        s.resize(slow);
        return;
    }
};
```

## 二刷总结
反转字符串的整体做法：
1. 删除多余空格（移除元素法，双指针）
2. 整体反转
3. 以空格为单位，分隔单词，局部反转
删除多余空格：`for`循环遍历后，找到首字母不为空的位置，单独使用一个`while`循环处理一个单词。
注意事项：
（1）`while (fastI < s.size() && s[fastI] != ' ')`的顺序不要反，否则可能会造成越界问题。

# [55. 右旋字符串（第八期模拟笔试)](https://kamacoder.com/problempage.php?pid=1065)
## 我的写法（不推荐）
```
#include <iostream>
#include <string>
using namespace std;
int main() {
    int k;
    string s;
    string sExt;
    cin >> k;
    cin >> s;
    sExt = s.substr(s.size() - k, k);
    s.erase(s.size() - k, k);
    s.insert(0, sExt);
    cout << s << endl;
  
    return 0;
}
```
分析：
- 删除+插入；时间复杂度O(n)；开辟了新的空间，拷贝了末尾字符串，空间复杂度O(k)

## 算法优化
- 如何实现原地操作呢？有没有发现，这个其实和上一题交换单词很像！不需要上一题目中的删除单词，仅仅需要交换单词即可。那么思路也一样！
  先反转整个字符串
  再挨个单词反转
- 代码实现
	```
	#include <iostream>
	#include <string>
	#include <algorithm>
	using namespace std;
	  
	int main() {
	    int k;
	    string s;
	    cin >> k;
	    cin >> s;
	    reverse(s.begin(), s.end());
	    reverse(s.begin(), s.begin() + k);
	    reverse(s.begin() + k, s.end());
	    cout << s << endl;
	
	    return 0;
	
	}
	```
# [28. 实现 strStr()](https://leetcode.cn/problems/find-the-index-of-the-first-occurrence-in-a-string/description/)
## 算法思路
KMP算法
[KMP算法利用对称性通俗理解前缀next数组（C++代码实现），拒绝乱七八糟的公式！-CSDN博客](https://blog.csdn.net/m0_58712320/article/details/149369946?sharetype=blogdetail&sharerId=149369946&sharerefer=PC&sharesource=m0_58712320&spm=1011.2480.3001.8118)
# [459. 重复的子字符串](https://leetcode.cn/problems/repeated-substring-pattern/description/)
## 算法思路
KMP算法或者拼接查找（不太推荐）
- 我们要利用一个结论：如何找到重复字符串的最小循环体？
  设字符串长度为len，我们只看```next[len-1]的值```。
  若len能被```len - next[len - 1]```整除且```next[len - 1] > 0```，就说明原字符串由一个大小为```len - next[len - 1]的循环结构```。
- 疑难点
  - 为什么只看len-1处呢？因为只有len-1处能代表整个字符串的全局对称结构。
  - 为什么要整除呢？
    不要太纠结，就是仅仅表示周期而已。
  - 为什么周期是`len-next[len-1]`？
	`next[len-1]`表示前后的重合长度，后缀的七点是`len - next[len-1]`。当前缀和后缀相等时，说明字符串从位置`len - next[len-1]`开始又重复了开头的模式，所以重复单元长度候选是`len - next[len-1]`
## 代码实现
```
class Solution {
public:
	bool repeatedSubstringPattern(string s) {
		vector<int> next = getNext(s);
		int len = s.size();
		int m = next[len - 1];
		return (m && len % (len - m) == 0) ? true : false;
	}

private:
	vector<int> getNext(string s) {
		vector<int> next(s.size(), 0);
		int j = 0;
		for (int i = 1; i < s.size(); i++) {
			while (j && s[i] != s[j]) j = next[j - 1];
			if (s[i] == s[j]) j++;
			next[i] = j;
		}
		return next;
	}
};
```
注意点：
- 判断是否有循环结构，只看```next[len - 1]```且这个值一定要大于0
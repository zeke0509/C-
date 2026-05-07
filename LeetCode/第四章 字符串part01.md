# [541. 反转字符串 II](https://leetcode.cn/problems/reverse-string-ii/)
## 我的第一次解法（能运行但麻烦）
```
class Solution {
public:
    string reverseStr(string s, int k) {
        int len = s.size();
        int count = len/2/k;        // count个完整2k
        int n = len % (2 * k);      // 最后一组剩余字符个数
        for (int i = 0; i < count; i++) {
            for (int j = 0; j < k/2; j++) {
                // 0,1,....,2k-1; 2k, 2k+1,...,4k-1;...
                swap(s[2 * k * i + j], s[k * (2 * i + 1) - 1 - j]);
            }
        }
        if (n < k ) {
            for (int j = 0; j < n/2; j++) {
                swap(s[2 * k * count + j], s[len - 1 -j]);
            }
        } else if (n >= k) {
            for (int j = 0; j < k/2; j++) {
                swap(s[2 * k * count + j], s[k * (2 * count + 1) - 1 - j]);
            }
        }
        return s;
    }
};
```
## 算法思路
- 看透本质，即翻转哪个区间，不翻转哪个区间？（下面用索引来表示）
  翻转```[0,k)、[2k,3k)、[4k,5k)、....```
  最后一个区间的翻转略有不同，最后一个区间起点不变，区间长度取k和（长度-起点）的最小值
- 那么如果在每段reverse中加入一个取最小值为获取终点的条件即可。

## 代码实现
```
class Solution {
public:
    string reverseStr(string s, int k) {
        int len = s.size();
        if (k < 1) return s;
        for (int i = 0; i < len; i += 2 * k) {
            int indexE = min(len, i + k);
            reverse(s.begin() + i, s.begin() + indexE);     // 左闭右开
        }
        return s;
    }
};
```
## 注意事项
- reverse函数的参数可以是迭代器、指针类型，这里是迭代器类型。
- 因为reverse函数交换的时候是左闭右开，所以reverse中的第二个参数，要接收```k、3k、5k、...，而不是k-1、3k-1、...```
- s.begin()就是索引为0，指向第一个元素的迭代器。

# [54. 替换数字（第八期模拟笔试）](https://kamacoder.com/problempage.php?pid=1064))
## 我第一次的思路
- 遍历字符串，判断是否为数字。若为数组，执行erase+insert操作
- 这样遍历的for循环就不能用i < s.size()了，因为执行替换操作后，我的下一次遍历不希望从i+1开始，而是从加过新字符串后的位置开始。所以循环次数由一个变量在while循环中额外控制。代码如下
	```
	#include <iostream>
	#include <string>
	using namespace std;
	int main() {
	    string s;
	    cin >> s;
	    int len = s.size();
	    string insStr = "number";
	    int n = insStr.size();
	    int i = 0;
	    while (len--) {
	        char c = s[i];
	        if (c <= '9' && c >= '0') {
	            s.erase(i, 1);
	            s.insert(i, insStr);
	            i += n;
	            len += (n - 1);
	        } else i += 1;
	    }
	    cout << s << endl;	  
	    return 0;	
	}
	```
	问题：时间复杂度On^2 （因为erase和insert的复杂度是On）

## 算法优化
- 前面的算法中，是因为insert的操作导致时间复杂度很高。如果我们能再创建一个字符串，额外开辟辅助空间，来记录我们新的字符串，就不需要insert操作了，仅仅用拼接操作就行（O1）
- 代码如下：
	```
	#include <iostream>
	#include <string>
	using namespace std;
	int main() {
	    string s;
	    cin >> s;
	    int len = s.size();
	    string results;
	    for (char c : s) {
	        if (c >= 'a' && c <= 'z') results += c;
	        else if (c >= '0' && c <= '9') results += "number";
	    }
	    cout << results << endl;
	
	    return 0;
	}
	```
	分析：时间复杂度为On，但空间复杂度On
	那能否不开辟额外的空间，直接在原来的字符串上操作呢？
## 算法再优化
- 在原字符串上修改的难题是：每次替换会导致字符串长度发生变化。那我们可以先确定长度！遍历找出字符串中数字出现的次数，进而计算出新字符串的长度。
- 让一个指针（old）来从原字符串的尾部开始遍历；同时让另一个指针（new）指向扩容后的新字符串尾部
  若该元素为字母，令新指针的元素 = 该元素
  若该元素为数字，令新指针的元素 = number，这里需要从后往前依次赋值'r'、'e'、...
- 代码如下：
	```
	#include <iostream>
	#include <string>
	using namespace std;
	int main() {
	    string s;
	    string iStr = "number";
	    cin >> s;
	    int len = s.size();
	    int lenI = iStr.size();
	    int count = 0;
	    for (char c : s) {
	        if (c >= '0' && c <= '9') count++;
	    }
	    s.resize(len + count * (lenI - 1));
	    int indexOld = len - 1;
	    int indexNew = s.size() - 1;
	    while (indexOld >= 0) {     // 易错
	        char ch = s[indexOld];
	        if (ch >= '0' && ch <= '9') {
	            // cout << "ch = " << ch << endl;
	            for (int i = lenI; i; i--) {
	                s[indexNew] = iStr[i - 1];
	                indexNew--;
	            }
	        } else if (ch >= 'a' && ch <= 'z') {
	            s[indexNew--] = s[indexOld];
	        }
	        indexOld--;
	    }
	    cout << s << endl;
	    return 0;
	}
	```
	
## 算法总结：替换字符串，可以先扩容，再从尾部赋值
- 注意事项
  - while循环判断完条件后，立刻会执行判断条件中的语句，这就导致了while (indexOld >= 0)不适合用while (indexOld-- >= 0)，因为这样使索引变了。所以我们采用再循环体内部自减。
  - 使用resize的时间复杂度为O1，可以为数组扩容

思考：那如果替换字符串的长度 < 被替换字符串的长度呢？
# （二刷已会）1. A+B问题1

## C++函数基础
1. 1个C++程序必须有且只有一个main函数
2. 返回类型
   main函数的返回类型一定是int
## 输出输出流
1. 内置库iostream
   - 包含两个基础类型，分别是istream（输入流）和ostream（输出流）：允许开发者从键盘读取输入并在屏幕上输出结果。
   - iostream库中，可以使用两个对象，分别是cin和cout
     cin：标准输入流对象，用于从键盘读取输入。
     cout：标准输出流对象，用于向屏幕输出结果。
   - 命名空间std：
     std是C++标准库的缩写。当使用标准库的功能时，需要使用std::前缀（命名空间限定符）来指明我要使用的内容位于C++标准库中。
     C++中所有标准库成员都在同一个std命名空间中，即使来自不同的头文件，也不会产生冲突。
     e.g. 我们要使用cin的话，就需要先找到命名空间，然后再使用cin。即```std::cin```
   - 输入运算符>>
     作用：将数据从输入流（如键盘、文件等）读取到变量中
     链式输入操作：在连续读取多个值时，可以用空格分开
     注：自动跳过空格、回车、制表符
     e.g.
		``` int a, b; std::cin >> a >> b;```
   - 输出运算符<<
     作用：将数据送入输出流

# 10. 平均绩点
## 一刷错误
- 不知道使用getline
- 错把line.size()当作成绩个数。正确做法：定义一个num变量记录成绩个数，判断当成绩有效的时候，num++
- int/int 得到的还是int。正确做法：```1.0 * int / int 得到的是float```
- 可以引入```<stdio.h>头文件，调用printf控制格式输出```
- 引入 bool flag 来判断成绩是否发生异常
- 可以考虑引入字符流对象'stringstream`
# 8. 奇怪的信
## 一刷错误
- 错误使用字符串 -> 整型。如果想使用这种方法，需要```int a = line(1) - '0'```
- 利用```n % 10 获得最后一位，再利用 n /= 10 迭代```
# 11. 句子缩写
## 一刷错误
- 可以用cin.get()来清除缓冲区残留的换行符
  关于为什么会残留换行符：
  首先 cin 能读取内容，并自动跳过空格/换行符，但这并不意味着换行符消失。换行符会留在缓冲区中。
  而getline()是读取本行内容直到换行符停止，并清除换行符。
  这就导致：cin读取完n，留下了一个换行符，而第一个getline会认为这个换行符是一行内容，会先读取掉这个空字符，第二个getline才会读取下一行内容。
- 小写字符==-32==得到大写字符
- 检测词组中单词的方法：当前字符是空格 && 下一字符不是空格
## 改进
- 可以使用`stringstream`来处理每一行的字符串

# 15. 链表的基础操作III
## 一刷错误
- 初始化列表结尾不需要加分号
- struct和class定义的结尾}要加分号
- 构造函数初始化是创建了一个虚拟头节点，这个过程不要忘记了==new==。
- list.insert_tail(cin) 这是不被允许的。必须先声明一个 int x，再把cin赋值给x。
- 结构体定义中，不要忘记对next也要定义。这里默认定义为nullptr就可以
- 删除元素的的循环，只能处理pos>=2，所以不仅要考虑 pos<1的情况，还要考虑pos = 1的情况。pos=1，链表长为0的情况，应该返回1
- 指定位置插入，设计`new`新节点，所以应该先判断插入位置是否合法，再`new`新节点，避免发生内存泄漏。
- 完整代码如下：
	```
	#include <iostream>
	using namespace std;
	
	struct listNode{
	    int val;
	    listNode *next;
	    listNode(int x): val(x), next(nullptr) {}
	};
	  
	class linkedList{
	private:
	    listNode *head;
	    listNode *tail;
	  
	public:
	    linkedList(): head(new listNode(0)), tail(head) {}
	  
	    // 尾插法
	    void insert_Tail(int x) {
	        listNode *new_node = new listNode(x);
	        tail->next = new_node;
	        tail = new_node;
	    }
	  
	    // 指定位置插入元素
	    int insert_value_At(int pos, int x) {
	        listNode *p = head;
	        listNode *new_node = new listNode(x);
	        if (pos < 1) return 1;
	  
	        // 找到插入位置的前驱节点
	        while(--pos) {  // 循环pos-1次，
	            p = p->next;
	            if (p == nullptr) return 1;   // 前驱节点为尾节点
	        }
	        new_node->next = p->next;   // 新节点next = 前驱节点的next
	        p->next = new_node;  
	
	        return 0;
	    }
	  
	    // 指定位置删除元素
	    int delete_value_At(int pos) {
	        listNode *p = head;
	        if (pos < 1) return 1;
	        if (p->next == nullptr) return 1;
	        while(--pos){       // 循环pos-1次，找到前驱节点
	            p = p->next;
	            if (p->next == nullptr) return 1;   // p是前驱节点，那么p->next=nullptr 就说明前驱节点为尾节点
	        }
	
	        listNode *temp = p->next;   // 要删除位置的节点
	        p->next = temp->next;       // 修改前驱节点的指针域
	        delete temp;                // 释放要删除节点
	
	        return 0;
	    }
	
	    // 遍历打印输出（最后一个元素无空格）
	    int print_Out() {
	        listNode *p = head->next;
	        if (p == nullptr) return 1;
	  
	        while (p != nullptr) {
	            cout << p->val;
	            p = p->next;
	            if (p != nullptr) cout << ' ';  // 当p不为尾节点
	        }
	  
	        cout << endl;
	        
	        return 0;
	    }
	};
	
	int main() {
	    int k, val, S, n, x, L, m, error;
	    cin >> k;
	    linkedList list;
	    while(k--) {
	        cin >> val;
	        list.insert_Tail(val);
	    }
	  
	    cin >> S;
	    while(S--) {
	        cin >> n >> x;
	        error = list.insert_value_At(n, x);
	        if (error) cout << "Insertion position is invalid.\n";
	        else list.print_Out();
	    }
	
	    cin >> L;
	    while (L--) {
	        cin >> m;
	        error = list.delete_value_At (m);
	        if (error) cout << "Deletion position is invalid.\n";
	        else list.print_Out();
	    }
	  
	    return 0;
	}
	```

# 16. 出现频率最高的字母
## 一刷总结
1. 本质：是一个较小范围的计数问题。那么可以使用数组哈希+寻找数组最大元素的方法
2. 解题思路：
   哈希函数（即小写字母到```count[index]中index```的映射）：```index = s[i] - 'a'```
3. 错误点
   - ```int count[26] = {0}```可以初始化数组中所有元素的值。
   - 数组没有.size()方法，计算数组长度的方法：```n = sizeof(arr)/sizeof(arr[0])```
4. string 是自动管理内存机制，比如说第一次循环s=abb，第二次循环s = cd，那么第二次循环会自动清除掉第一次s的abb，然后s=cd。而不会造成s=cdb的情况
# 58. 给定一个整数数组 Array，请计算该数组在每个指定区间内元素的总和
1. 题目链接：https://kamacoder.com/problempage.php?pid=1070
2. 算法思路：使用前缀和可以降低时间复杂度
# 44. 开发商购买土地
1. 题目链接：[44. 开发商购买土地（第五期模拟笔试）](https://kamacoder.com/problempage.php?pid=1044)
2. 暴力运算方法：O(n2)
	```
	#include <iostream>
	#include <algorithm>
	#include <climits>
	#include <cstdlib>
	#include <vector>
	using namespace std;
	  
	int main() {
	    int m, n;
	    cin >> n >> m;
	    vector<vector<int>> arr(n, vector<int>(m, 0));  // n行m列
	    vector<int> prerow(n, 0);
	    vector<int> precol(m, 0);
	    int rowsum = 0;
	    int colsum = 0;
	    int value = INT_MAX;
	  
	    for (int i = 0; i < n; i++) {
	        for (int j = 0; j < m; j++) {
	            cin >> arr[i][j];
	            rowsum += arr[i][j];
	        }
	        prerow[i] = rowsum;
	        // cout << "prerow_" << i << "=" << prerow[i] << endl;
	    }
	  
	    for (int j = 0; j < m; j++) {
	        for(int i = 0; i < n; i++) {
	            colsum += arr[i][j];
	        }
	        precol[j] = colsum;
	        // cout << "precol_" << j << "=" << precol[j] << endl;
	    }
	
	    for (int i = 0; i < n - 1; i++) {   // i为0表示：第一行是一部分，其它是另一部分
	        value = min(abs((prerow[n-1] - prerow[i] -  prerow[i])), value);
	    }
	    for (int j = 0; j < m - 1; j++) {
	        value = min(abs((precol[m-1] - precol[j] -  precol[j])), value);
	    }
	  
	    cout << value;
	    return 0;
	}
		
	```


# 17. 判断集合成员
1. 算法思路：
   - 这是一个不重复元素的查找：使用集合数据结构的哈希表
   - 不要求有顺序，所以用unordered_set就可以
2. 错误注意：
   - 集合一旦定义不支持修改，想修改就只能删除再添加。所以如下是错误的：
     ```for (auto &x : mySet) x = value;```

# 18. 开房门
1. 算法思路：
   - 每个键对应一个值，而且键特别大，不适合用数组下标索引；所以考虑用无序的map
   - 这里可以让key-value为：房门-钥匙 或 钥匙-房门
     若已知房门找钥匙，可以让房门为key，这样可以用find()方法；反之，用遍历的方法，查找value
1. 错误点
   - find()函数用于查找key，返回类型为迭代器，想得到具体value需要解引用
   - 遍历是用于查找value（也可查找key），返回的类型是 ```pair<int, int>```，想得到具体key不需要解引用
	```
	// for (const pair<int, int> & it : myMap) {  // 推荐 或者
	for (auto it : myMap) {    // 不推荐，因为会拷贝每一个myMap键值对进it，效率低
		it.first = ...;
		it.second = ...;
	}
	```
   - 当觉得退出循环的条件有点不合适的时候，可以考虑引入一个bool类型flag
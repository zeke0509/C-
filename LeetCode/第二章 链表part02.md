# 24. 两两交换链表中的节点
题面：给你一个链表，两两交换其中相邻的节点，并返回交换后链表的头节点。你必须在不修改节点内部的值的情况下完成本题（即，只能进行节点交换）。
题目链接：[24. 两两交换链表中的节点]https://leetcode.cn/problems/swap-nodes-in-pairs/description/
## 模拟指针法
### 算法思路
我们要找到操作节点的前驱节点，这就需要我们定义一个虚拟头节点。
### 代码实现
```
public:
	ListNode* swapPairs(ListNode* head) {
		ListNode* dummyHead = new ListNode(0,head);     // 定义虚拟头节点
		ListNode* cur = dummyHead;     // 用于遍历链表
		while(cur->next != nullptr && cur->next->next != nullptr) {
			ListNode* temp = cur->next->next;     // 保存交换节点2指针为temp
			cur->next->next = cur->next->next->next;     // 修改交换节点1的指针域
			temp->next = cur->next;               // 修改交换节点2的指针域
			cur->next = temp;                    // 修改虚拟头节点的指针域
			cur = temp->next;                     // 更新遍历指针
		}
		return dummyHead->next;
		delete dummyHead;
	}
```
注意：
- 我们修改节点指针域的顺序，先构建我们想要结果的后方。这样可以把证构造前方的时候不破坏后边，而且能够额外定义很少的变量。以这题为例，我们按照顺序让1->4，2->1, dummy->2
- 当我们按照上述顺序发现，先修改了1的指针域会导致找不到2了，那就说明我们要在修改1的指针域前先保存交换节点2指针。

## 递归法
[Leetcode 24.两两交换链表中节点 递归法（与C++代码结合）-CSDN博客](https://blog.csdn.net/m0_58712320/article/details/149259251?spm=1001.2014.3001.5501)
1. 算法思路：
   - 原问题拆解成子问题

# 面试题 02.07. 链表相交
题目链接：https://leetcode.cn/problems/intersection-of-two-linked-lists-lcci/solutions/1190240/mian-shi-ti-0207-lian-biao-xiang-jiao-sh-b8hn/
## 追击问题
1. 算法思路：让链表在相遇位置处遍历一样多的次数。
   ![[Pasted image 20250711131518.png]]
   定义两个指针：指针1，从a1出发，到链表末尾，再从b1出发；指针2，从b1出发，到链表末尾，再从a1出发。这样当遍历的节点指针相等时，两个链表相遇。
2. 注意点
   - 下面代码可以用三目运算符简化
	```
	while (a != b) {
		if (a != nullptr) a = a->next;
		else a = headB;
		if (b != nullptr) b = b->next;
		else b = headA;
	}
	```
	简化后的代码：
	```
	while (a != b) {
		a = a != nullptr ? a->next : headB;
		b = b != nullptr ? b->next : headA;
	}
	```

## 快慢指针（要实现获得A、B链表的长度）

# 142. 环形链表II
题目链接：https://leetcode.cn/problems/linked-list-cycle-ii/
## 我的代码（思路很差）
```
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* dummyHead = new ListNode(0, head);
        if (head == nullptr || head->next == nullptr) return nullptr;
        ListNode* fastP = dummyHead->next->next;        // 以防不会进入while循环
        ListNode* slowP = dummyHead->next;
        while (fastP != slowP) {
            if (fastP == nullptr || fastP->next == nullptr || fastP->next->next == nullptr) return nullptr;
            fastP = fastP->next->next;
            slowP = slowP->next;
        }
        slowP = dummyHead;
        while (slowP != fastP) {
            slowP = slowP->next;
            fastP = fastP->next;
        }
        return slowP;
    }
};
```
1. 关键问题：
   - 我定义了一个虚拟头节点，其实是不需要的。虚拟头节点适用于插入或删除元素，换句话说，当我需要操作链表的时候才定义一个虚拟头节点。
   - 选择了```fastP != slowP```作为判断条件，这个很差！因为初始化后，快慢指针就都是从头节点出发的，这一刻他俩就是相等的；而且，循环不应该在他俩相等的时候就停住，相等只是可以找到是否有环，循环仍需往下执行。
   - 对于退出while循环条件的考量：要能找到表征系统不存在环时，退出while循环的条件，即快指针走到了链表的末尾！

## 追及问题
1. 算法思路：先判断是否存在环，再找到环的入口。
   ![[Pasted image 20250711145654.png]]
   - 判断是否存在环：以相对速度进行考量，让快指针每次走两步，慢指针每次走一步，同时从头节点开始出发。那么，两指针的相对速度为1，这说明，如果存在环，快指针一定会追上慢指针（即相遇）。
   - 找到环的入口（脑子里始终围绕y+z这个一圈的整体进行变形）
     相遇时：慢指针走过x+y；快指针走过x+y+n(y+z), n >= 1。由快指针走过的路程是慢指针走过路程的二倍，得：x = n(y+z) - y = (n-1)(y+z) + z 。推出：
     x-z = (n-1)(y+z)，这说明如果慢指针从x-z出发，快指针从入口出发，这下让两指针的速度都为1，一定可以在入口处相遇。
     换个角度，如果慢指针从头节点出发，快指针从相遇点出发，过程和上面是一样的，所以一定也可以在入口处相遇。
2. 代码实现
	```
	class Solution {
	public:
	    ListNode *detectCycle(ListNode *head) {
	        ListNode* fastP = head;
	        ListNode* slowP = head;
	        while (fastP != nullptr && fastP->next != nullptr) {
	            fastP = fastP->next->next;
	            slowP = slowP->next;
	            if (fastP == slowP) {
	                slowP = head;
	                while (slowP != fastP) {
	                    slowP = slowP->next;
	                    fastP = fastP->next;
	                }
	                return slowP;
	            }
	        }
	        return nullptr;
	    }
	};
	```
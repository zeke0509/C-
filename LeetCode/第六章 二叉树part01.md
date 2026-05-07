# 递归遍历
## [前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/submissions/652113661/)
### 算法思路
递归写法三要素：
- 确定递归函数的参数和返回值
  传入一个根节点，传入一个数组来记录我们的结果。返回值为void，因为结果存在参数里了
- 确定终止条件
  深度优先搜索的过程，那么就是遇到节点为空就return到上一次调用
- 确定单层递归的逻辑
  实现根左右。根：把结果放在数组里；左：遍历左孩子；右：遍历右孩子
完整代码
```
class Solution {

private:

    void preOrder(TreeNode* T, vector<int>& v) {

        if (T == nullptr) return;

        v.push_back(T->val);

        preOrder(T->left, v);

        preOrder(T->right, v);

    }

public:

    vector<int> preorderTraversal(TreeNode* root) {

        vector<int> res;

        preOrder(root, res);

        return res;

    }

};
```

# 迭代遍历
用迭代法实现递归，是用栈来模拟
## [前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/submissions/652113661/)
### 算法思路
- 遍历顺序：中左右
  处理顺序：先处理中间节点，再分别处理左右孩子
  我们发现遍历顺序与处理顺序是一样的
- 遍历的元素放入栈里，要处理的元素弹出栈
- 每次循环
  先弹出栈顶元素（要处理的元素），把值放入结果数组
  再依次将弹栈节点的右孩子和左孩子入栈（因为后进先出，这样下次弹栈是左孩子）
  直到栈为空，循环结束
### 代码实现
class Solution {

public:

    vector<int> preorderTraversal(TreeNode* root) {

        stack<TreeNode*> st;

        vector<int> res;

        if (root == nullptr) return res;

        st.push(root);

        while (!st.empty()) {

            TreeNode* cur = st.top();

            st.pop();

            if (cur == nullptr) continue;

            res.push_back(cur->val);

            st.push(cur->right);

            st.push(cur->left);

        }

        return res;

    }

};

### 二刷注意
1. `if (cur == nullptr) continue;`有什么作用？
	答：因为如果某个节点的左右节点为空，我也把它压入栈中了，只不过是`nullptr`，所以不加这句会导致访问空指针的`val`
2. 可以不用`if (cur == nullptr) continue;`吗？
	答：可以，这样就要求不能把`nullptr`加入栈中，所以在加入`left / right`之前，先判断节点是否为`nullptr`

## [后序遍历](https://leetcode.cn/problems/binary-tree-postorder-traversal/solutions/)
### 算法思路一
按照前序遍历的方法实现中右左，让后将答案数组反转，即可得到左右中
代码实现：
```
class Solution {

public:

    vector<int> postorderTraversal(TreeNode* root) {

        vector<int> res;

        stack<TreeNode*> st;

        if (root == nullptr) return res;

        st.push(root);

        while (!st.empty()) {

            TreeNode* cur = st.top();

            st.pop();

            if (cur == nullptr) continue;

            res.push_back(cur->val);

            st.push(cur->left);

            st.push(cur->right);

        }

        ranges::reverse(res);

        return res;

    }

};
```
### 算法思路二
利用类似中序遍历的迭代法。先理清算法逻辑：
遍历左孩子节点，依次入栈，直到最左侧节点（后续以“该节点”称呼），然后判断该节点是否拥有右侧节点。若没有右侧节点或者有已被输出的右侧结点，则可以输出该节点，该节点出栈，后续循环处理新栈顶结点；若有未处理的右侧结点，则后续循环处理该节点的右侧节点。
根据上述内容，我们可以设计一个结点r来记录上一次输出的结点（用于判断该节点的右侧节点是否已被处理过）
代码实现
```
class Solution {

public:

    vector<int> postorderTraversal(TreeNode* root) {

        vector<int> res;

        stack<TreeNode*> st;

        if (root == nullptr) return res;

        TreeNode* cur = root;

        TreeNode* r = nullptr;          // r用于记录上一次弹出的元素。若某个根节点有右节点，若r为右节点，才能弹出根节点

        while (!st.empty() || cur != nullptr) {

            while (cur != nullptr) {    // 找到当前节点的最左侧节点，入栈

                st.push(cur);

                cur = cur->left;

            }

            TreeNode* p = st.top();

            if (r == p->right || p->right == nullptr) {  // 若该节点没有右孩子节点，或者右孩子节点已经遍历输出过，则输出该节点

                res.push_back(p->val);

                r = p;

                st.pop();

                cur = nullptr;      // 不push新元素

            } else cur = p->right;

        }

        return res;

    }

};
```
### 二刷总结（迭代法）
1. 思路
	后序：左 -> 右 -> 中（当前节点没有左子树，且没有右子树才能进行输出）
	找到一个节点的最左面（`while`循环） -> 输出该节点的`val` -> 判断该节点有无右孩子 -> 若有右孩子，则把右孩子入栈；若无右孩子，不进行处理，下一步继续弹出栈顶元素。
2. 易错点
	上述中，判断该节点有无右孩子时，其中“无右孩子，不进行处理”会有两种情况：真的没有右孩子 + 有右孩子，但是已经被处理过了。

## [中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/description/)
### 算法思路
- 首先找到最左侧节点：不断寻找```cur = cur->left```，直到cur为空说明找到最左侧。并在这个过程中，把访问过的元素全都入栈，做个记录
- 弹出栈顶节点并输出
- 将弹栈节点右孩子入栈，若没有右孩子则继续弹出下一个栈顶元素，如何通过代码实现？
  可以利用我们上面的入栈循环，先令```cur = 栈顶元素->right```，之间通过循环入口条件，判断cur是否为空。
- 重复上述过程
### 代码实现
```
class Solution {

public:

    vector<int> inorderTraversal(TreeNode* root) {

        vector<int> res;

        stack<TreeNode*> st;

        TreeNode* cur = root;

        while (!st.empty() || cur != nullptr) {

            while (cur != nullptr) {

                st.push(cur);

                cur = cur->left;

            }

            TreeNode* p = st.top();

            st.pop();

            res.push_back(p->val);

            cur = p->right;

        }

        return res;

    }

};
```
### 注意事项
循环的结束条件有两个：栈为空且curr为空（与原来：栈为空，不同）
想象一种情况，当遍历cur 为节点2处，那么此时进入第一次while循环
第一次while循环：栈内有5，进入while循环；cur为节点2，不为空，把节点2压入栈，此时栈内为```[5,2```，cur = cur->left，cur为空；p = 节点2，节点2被弹出，cur = 节点2->right 为空。
进入第二次while循环：栈内有5，cur为空；p = 节点5，节点5被弹出，cur = 节点5->right。此时，栈为空，但是cur 为节点6不为空。
所以，由于左中右遍历顺序，不能仅仅看栈为空退出循环。因为栈为空说明之前遍历过的节点已经都被记录到了，但是遍历还没有结束，还要继续遍历上一次弹出节点（最后一个弹栈节点）的右孩子
![[Pasted image 20250812172321.png]]

# [统一迭代（未掌握）](https://programmercarl.com/%E4%BA%8C%E5%8F%89%E6%A0%91%E7%9A%84%E7%BB%9F%E4%B8%80%E8%BF%AD%E4%BB%A3%E6%B3%95.html)

# 层序遍历
下面没什么二刷的必要，看情况吧
## 题目清单
- [102.二叉树的层序遍历(opens new window)](https://leetcode.cn/problems/binary-tree-level-order-traversal/)
- [107.二叉树的层次遍历II(opens new window)](https://leetcode.cn/problems/binary-tree-level-order-traversal-ii/)
- [199.二叉树的右视图(opens new window)](https://leetcode.cn/problems/binary-tree-right-side-view/)
- [637.二叉树的层平均值(opens new window)](https://leetcode.cn/problems/average-of-levels-in-binary-tree/)
- [429.N叉树的层序遍历(opens new window)](https://leetcode.cn/problems/n-ary-tree-level-order-traversal/)
- [515.在每个树行中找最大值(opens new window)](https://leetcode.cn/problems/find-largest-value-in-each-tree-row/)
- [116.填充每个节点的下一个右侧节点指针(opens new window)](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node/)
- [117.填充每个节点的下一个右侧节点指针II(opens new window)](https://leetcode.cn/problems/populating-next-right-pointers-in-each-node-ii/)
- [104.二叉树的最大深度(opens new window)](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)
- [111.二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/)
## [102.二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)
### 代码实现
```
class Solution {

public:

    vector<vector<int>> levelOrder(TreeNode* root) {

        queue<TreeNode*> q;

        vector<vector<int>> res;

        vector<int> ans;

        if (root == nullptr) return res;

        q.push(root);

        while (!q.empty()) {

            int count = q.size();

            ans.clear();

            while (count--) {

                TreeNode* cur = q.front();

                q.pop();

                ans.push_back(cur->val);

                if (cur->left) q.push(cur->left);

                if (cur->right) q.push(cur->right);

            }

            res.push_back(ans);

        }

        return res;

    }

};
```

# [513. 找树左下角的值](https://leetcode.cn/problems/find-bottom-left-tree-value/description/)
## 迭代法
### 算法思路
层序遍历，记录每一层第一个节点。当队列为空时，输出答案
### 代码实现
```
class Solution {

public:

    int findBottomLeftValue(TreeNode* root) {

        queue<TreeNode*> q;

        q.push(root);

        int temp;

        while (!q.empty()) {

            int count = q.size();

            temp = q.front()->val;

            while (count--) {

                TreeNode* node = q.front();

                q.pop();

                if (node->left) q.push(node->left);

                if (node->right) q.push(node->right);

            }

        }

        return temp;

    }

};
```
## 递归法
### 算法思路
- 递归函数：获得当前节点的深度，并更新最大深度，更新最大深度对应根节点的值
- 递归终止条件：当前节点为空时，深度为0，最大深度为0，答案为空
- 单层递归逻辑：
  比较当前节点深度与最大深度，并更新最大深度，更新最大深度对应根节点的值。
  一直向左遍历，每次深度+1；当遇见递归终止条件的时候，回溯（深度-1）并递归遍历右节点
### 代码实现
```
class Solution {

public:

    int findBottomLeftValue(TreeNode* root) {

        int maxDepth = INT_MIN;

        int ans = 0;

        tranversal(root, 1, maxDepth, ans);

        return ans;

    }

  

private:

    void tranversal(TreeNode* root, int depth, int& maxDepth, int& ans) {

        if (!root) return;

        if (depth > maxDepth) {

            maxDepth = depth;

            ans = root->val;

        }

        depth++;

        tranversal(root->left, depth, maxDepth, ans);

        depth--;

        depth++;

        tranversal(root->right, depth, maxDepth, ans);

        depth--;

    }

};
```

### 代码优化（回溯部分）
```
class Solution {

public:

    int findBottomLeftValue(TreeNode* root) {

        int maxDepth = INT_MIN;

        int ans = 0;

        tranversal(root, 1, maxDepth, ans);

        return ans;

    }

  

private:

    void tranversal(TreeNode* root, int depth, int& maxDepth, int& ans) {

        if (!root) return;

        if (depth > maxDepth) {

            maxDepth = depth;

            ans = root->val;

        }

        tranversal(root->left, depth + 1, maxDepth, ans);

        tranversal(root->right, depth + 1, maxDepth, ans);

    }

};
```

# [112. 路径总和](https://programmercarl.com/0112.%E8%B7%AF%E5%BE%84%E6%80%BB%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
## 递归
### 算法思路
可以将目标值递减，判断最后是否为0
- 递归函数：一定要有返回值，因为遇到符合条件的路径就要及时返回true
- 递归终止条件：当前节点为叶子节点，且count=0，返回true；当前节点为叶子节点，但count不为0，返回false
- 单层递归逻辑
  遍历到当前节点，先减去当前节点的值。
  然后去递归遍历左侧节点，如果左侧递归函数为true，则直接返回true，后面不用再遍历了
  左侧遍历完，回溯到中节点root，遍历右侧。这里的回溯不用在改target，因为回溯回来的target就是原来中节点递归遍历左孩子时的root节点
### 代码实现
```
class Solution {

public:

    bool hasPathSum(TreeNode* root, int targetSum) {

        if (!root) return false;

        targetSum -= root->val;

        if (!root->left && !root->right && targetSum == 0) return true;

        if (!root->left && !root->right && targetSum != 0) return false;

        if (hasPathSum(root->left, targetSum)) return true;

        if (hasPathSum(root->right, targetSum)) return true;

        return false;

    }

};
```

## 迭代
### 算法思路
不必按层序遍历访问，仍然是一个前序遍历，所以可以用栈实现。
但是栈内不光要存放当前节点，还要存放当前节点的路径和
### 代码实现
```
class Solution {

public:

    bool hasPathSum(TreeNode* root, int targetSum) {

        stack<pair<TreeNode*, int>> pathSt;

        if (!root) return false;

        pathSt.push(pair<TreeNode*, int> (root, root->val));

        while (!pathSt.empty()) {

            TreeNode* p = pathSt.top().first;

            int pSum = pathSt.top().second;

            if (!p->left && !p->right && pSum == targetSum) return true;

            pathSt.pop();

            if (p->right) pathSt.push(pair<TreeNode*, int> (p->right, pSum + p->right->val));

            if (p->left) pathSt.push(pair<TreeNode*, int> (p->left, pSum + p->left->val));

        }

        return false;

    }

};
```

# [113. 路径总和II](https://leetcode.cn/problems/path-sum-ii/description/)
## 递归法
### 算法思路
- 终止条件：
  若当前节点为叶节点，且满足路径总和，就把该节点对应的路径path加入结果数组res中
  若当前节点为叶节点，且不满足路径总和，就直接返回原来的res数组
- 递归函数
  根据终止条件我们知道，
  需要记录当前节点的path，当前序遍历的时候还需要回溯path的路径，所以传递path引用；
  而结果数组res，需要保留之前的结果，也是传递res引用；
  再传入参数root节点和目标值int，返回类型为void
### 代码实现
```
class Solution {

public:

    vector<vector<int>> pathSum(TreeNode* root, int targetSum) {

        vector<vector<int>> res;

        vector<int> path;

        traversal(root, targetSum, res, path);

        return res;

    }

  

private:

    void traversal(TreeNode* root, int targetSum, vector<vector<int>>& res, vector<int>& path) {

        if (!root) return;

        path.push_back(root->val);

        if (!root->left && !root->right && root->val == targetSum) {

            res.push_back(path);

            return;

        }

        if (!root->left && !root->right && root->val != targetSum) return;

        targetSum -= root->val;

        if (root->left) {

            traversal(root->left, targetSum, res, path);

            path.pop_back();

        }

        if (root->right) {

            traversal(root->right, targetSum, res, path);

            path.pop_back();

        }

    }

};
```

# [106.从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/description/)
## 问题
如果有多个数据相同呢？
## 算法思路
- 递归函数实现功能：参数为中序遍历、后序遍历，返回根节点，并确定根节点的左右指针
- 递归终止条件：当遍历数组的长度为1，那么根节点就是当前数组中的结点
- 确定单层递归逻辑：
  - 根据当前参数找到根节点
  - 确定根节点的左右指针，如何获取左右指针？
    将原参数中序和后序分别分为两部分，分别为根节点的左半部分和右半部分。
  - 分别递归调用两部分的函数，返回值是两个新的根节点，分别对应当前根节点的左右子孩子
## 注意事项
- 获取`vector`的末尾元素：`myVector.back()`
- 在`vector`中查找特定值的索引：
  利用`<algorithm>`库中的`find()`函数：
  `auto it = find(inorder.begin(), inorder.end(), rootVal);  // 左闭右开, 返回迭代器`
## 代码实现
```
class Solution {

public:

    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {

        if (postorder.size() == 0) return nullptr;

        int rootVal = postorder.back();

        TreeNode* root = new TreeNode(rootVal);

        auto it = find(inorder.begin(), inorder.end(), rootVal);

        vector<int> inorderLeft(inorder.begin(), it);

        vector<int> inorderRight(it + 1, inorder.end());

        int indexRoot = inorderLeft.size();

        vector<int> postorderLeft(postorder.begin(), postorder.begin() + indexRoot);

        vector<int> postorderRight(postorder.begin() + indexRoot, postorder.end() - 1);

        root->left = buildTree(inorderLeft, postorderLeft);

        root->right = buildTree(inorderRight, postorderRight);

        return root;

    }

};
```

## 算法改进
上面的思路中，每次都要创建新的`vector`，显然这是没有太大必要的。
- 改进1：递归函数的参数分别传入`inOrder`、`postOrder`数组的左闭右开区间，这样直接就能在原数组上直接操作，而避免频繁的创建`vector`
- 改进2：使用哈希`map`实现查找，复杂度为`O(1)`
### 代码实现
```
class Solution {
public:
    TreeNode* buildTree(vector<int>& inorder, vector<int>& postorder) {
        for (int i = 0; i < inorder.size(); i++) {
            mp[inorder[i]] = i;
        }
        return traversal(inorder, postorder, 0, inorder.size(), 0, postorder.size());
    }

private:
    unordered_map<int, int> mp;
    TreeNode* traversal(vector<int>& inorder, vector<int>& postorder, int inL, int inR, int postL, int postR) {
        int lenPost = postR - postL;
        if (lenPost == 0) return nullptr;
        TreeNode* root = new TreeNode(postorder[postR - 1]);
        if (lenPost == 1) return root;
        int rootIndex = (mp.find(root->val))->second;
        int lenLeft = rootIndex - inL;      // 当前根节点左孩子size
        // 左闭右开
        // inorder: inL -> rootIndex -> inR - 1
        // postorder: postL -> postR - 1 - 1 -> postR - 1 (左孩子size为lenLeft)
        root->left = traversal(inorder, postorder, inL, rootIndex, postL, postL + lenLeft);
        root->right = traversal(inorder, postorder, rootIndex + 1, inR, postL + lenLeft, postR - 1);
        return root;
    }
};

// 中序：左中右
// 后序：左右中
```
### 注意事项
- 关于`unordered_map<int, int> mp;`的定义位置
  不能写在`public的buildTree`下面，因为这样表示的是`buildTree`的局部变量，这在`traversal`函数并不可见；
  所以应该把这个定义写在`private`中，然后在`buildTree`没进入递归`traversal`前，处理好`mp`的值就没问题


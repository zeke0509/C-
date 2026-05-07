# [110. 平衡二叉树](https://leetcode.cn/problems/balanced-binary-tree/)
## 递归法
### 算法思路
某节点的高度：该节点到叶子节点的距离。这说明：若要求一个节点的高度，一定要找到叶子节点。所以很自然的就想到后序遍历。
通过设计一个求最大深度，即高度的函数递归。这里有一个关键设计：当左右子树高度差大于1，就让函数的返回值为-1，表示不是平衡二叉树。
- 确定函数功能、返回值、输入：获取当前节点的高度，返回值为int类型
- 终止条件：当访问到空节点，返回0
- 单层递归逻辑：分别记录当前节点的左、右子树的高度。若为-1，表示后面的节点已经不满足平衡二叉树了，那么整棵树也不是平衡二叉树了，继续返回-1；若abs>1也返回-1；若abs满足条件，则返回左右子树的最大高度+1，即为当前节点的高度。
### 代码实现
```
class Solution {

public:

    bool isBalanced(TreeNode* root) {

        return (getHeight(root) != -1);

    }

  

private:

    int getHeight(TreeNode* node) {

        if (node == nullptr) return 0;

        int left = getHeight(node->left);

        int right = getHeight(node->right);

        if (left == -1 || right == -1 || abs(left - right) > 1) return -1;

        return max(left, right) + 1;

    }

};
```
# [257. 二叉树的所有路径](https://leetcode.cn/problems/binary-tree-paths/description/)
## 递归法
### 算法思路
- 确定函数作用、返回值、参数
  找到当前节点所有路径。需要传入参数：当前节点指针、记录所有路径的```vector<string> res数组```
  同时还需要一个path数组，用于记录一次路径。然后可以通过回溯来作为下次递归的路径起点
- 确定终止条件：当前节点为叶节点时，把路径path转换成string类型存入res数组中
- 每一层递归逻辑：（中左右）
  把当前节点值加入路径path中
  若当前节点的左孩子非空，则递归函数处理该节点左孩子，处理完毕需要回退path路径到该节点
	```
	if (root->left) {
		traversal(root->left, path, res);
		path.pop_back();    // 回溯路径
	}
	```
  理解：当调用完traversal(root->left)，即有返回值的时候，会自动回溯到traversal(root)的状态，但是这个时候，path已经被更改了，需要我们退回path，然后递归遍历traversal(root->right)。
### 代码实现
```
class Solution {

public:

    vector<string> binaryTreePaths(TreeNode* root) {

        vector<string> res;

        vector<int> path;

        if (root == nullptr) return res;

        traversal(root, path, res);

        return res;

    }

  

private:

  void traversal(TreeNode* root, vector<int>& path, vector<string>& res) {

    path.push_back(root->val);

    if (!root->left && !root->right) {

      string s;

      s += (to_string(path[0]));

      for (int i = 1; i < path.size(); i++) s += ("->" + to_string(path[i]));

      res.push_back(s);

      return;

    }

    if (root->left) {

      traversal(root->left, path, res);

      path.pop_back();

    }

    if (root->right) {

      traversal(root->right, path, res);

      path.pop_back();

    }

  }

};
```

## 迭代法
### 算法思路
这里的迭代法，不是层序遍历（用队列维护），而是前序遍历（用栈维护）
- 一个栈treeSt：用于存放遍历的节点
  每次处理节点root时，让root出栈，让左孩子入栈直至左孩子为空，让右孩子入栈
- 一个栈pathSt：用于保存当前出栈节点的遍历路径
  每次处理出栈节点root时，让pathSt也出栈，并把这个值赋值给string，用于输出结果。
  右孩子入栈treeSt时，让带有右孩子的路径也入栈pathSt
  左孩子入栈treeSt时，让带有左孩子的路径也入栈pathSt
  注：保证栈treeSt和栈pathSt内部元素一一对应
- 一个数组result：当遇到叶节点的时候，输出string（即pathSt的栈顶元素）

### 代码实现
```
class Solution {

public:

    vector<string> binaryTreePaths(TreeNode* root) {

        vector<string> res;

        stack<TreeNode*> treeSt;

        stack<string> pathSt;

        if (root == nullptr) return res;

        treeSt.push(root);

        pathSt.push(to_string(root->val));

        while (!treeSt.empty()) {

          TreeNode* node = treeSt.top();

          treeSt.pop();

          string sPath = pathSt.top();

          pathSt.pop();

          if (!node->left && !node->right) res.push_back(sPath);

          if (node->right) {

            treeSt.push(node->right);

            pathSt.push(sPath + "->" + to_string(node->right->val));

          }

          if (node->left) {

            treeSt.push(node->left);

            pathSt.push(sPath + "->" + to_string(node->left->val));

          }

        }

        return res;

    }

};
```

# [404. 左叶子之和](https://leetcode.cn/problems/sum-of-left-leaves/)
## 递归法
### 算法思路
仅根据当前节点无法判断是否为左叶子节点，必须找到当前节点的前驱节点，才能判断该当前节点是否为左叶子节点。
- 函数功能：返回当前节点的左叶子之和
- 递归终止条件：当前节点为空或当前节点为叶子节点（这是因为我们要找的是前驱），返回0
- 单层递归逻辑：
  计算当前节点的左子树的左叶子和（若当前节点的左子树为左叶子节点，那么左子树的叶子和为当前值）
  计算当前节点的右子树的左叶子和
  相加两者，得到当前节点的左叶子和
### 代码实现
```
class Solution {

public:

    int sumOfLeftLeaves(TreeNode* root) {

        if (root == nullptr) return 0;

        if (!root->left && !root->right) return 0;

        int leftNum = sumOfLeftLeaves(root->left);

        if (root->left && !root->left->left && !root->left->right) leftNum += root->left->val;

        int rightNum = sumOfLeftLeaves(root->right);

        return leftNum + rightNum;

    }

};
```

## 迭代法
### 算法思路
就是按照前序（或中、后序）把节点依次入栈，然后判断当前节点是否拥有左叶子节点
### 代码实现
```
class Solution {

public:

    int sumOfLeftLeaves(TreeNode* root) {

        stack<TreeNode*> treeSt;

        if (root == nullptr) return 0;

        treeSt.push(root);

        int sum = 0;

        while (!treeSt.empty()) {

            TreeNode* node = treeSt.top();

            treeSt.pop();

            if (node->left && !node->left->left && !node->left->right) sum += node->left->val;

            if (node->right) treeSt.push(node->right);

            if (node->left) treeSt.push(node->left);

        }

        return sum;

    }

};
```

# [222. 完全二叉树的节点个数](https://leetcode.cn/problems/count-complete-tree-nodes/description/)
## 递归法
### 算法思路
利用满二叉树的节点数=2^depth - 1的性质，让递归的终止条件为满二叉树
### 代码实现
```
class Solution {

public:

    int countNodes(TreeNode* root) {

        if (root == nullptr) return 0;

        int lDepth = 0;

        int rDepth = 0;

        TreeNode* lNode = root;

        TreeNode* rNode = root;

        while (lNode->left) {

            lDepth++;

            lNode = lNode->left;

        }

        while (rNode->right) {

            rDepth++;

            rNode = rNode->right;

        }

        if (lDepth == rDepth) return (2 << lDepth) - 1;

        return countNodes(root->left) + countNodes(root->right) + 1;

    }

};
```

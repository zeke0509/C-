# [226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)
## 递归法
可以用先序或者后序，下面以后序为例。
代码实现
```
class Solution {

public:

    TreeNode* invertTree(TreeNode* root) {

        if (root == nullptr) return root;

        invertTree(root->left);

        invertTree(root->right);

        swap(root->left, root->right);

        return root;

    }

};
```

## 层序遍历法
层序遍历有一个小小的改进：这里不用记录当前是第几层，即省去了while(count--)的循环
代码实现
```
class Solution {

public:

    TreeNode* invertTree(TreeNode* root) {

        queue<TreeNode*> q;

        if (root == nullptr) return root;

        q.push(root);

        while (!q.empty()) {

            TreeNode* node = q.front();

            q.pop();

            swap(node->left, node->right);

            if (node->right) q.push(node->right);

            if (node->left) q.push(node->left);

        }

        return root;

    }

};
```

# [101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/solutions/)
## 递归法
### 算法思路
首先要判断使用什么遍历方法？其实是对根节点的左子树和右子树同时遍历。使用后序遍历
- 先找到左子树的最左侧节点，和右子树的最右侧节点，判等。若相等，按照左右中的顺序，判断左子树的右是否等于右子树的左，若相等满足条件
- 注意：要提前判断left和right是否为空，避免后序操作空指针
### 代码实现
```
class Solution {

public:

    bool isSymmetric(TreeNode* root) {

        if (root == nullptr) return false;

        return compare(root->left, root->right);

    }

  

private:

    bool compare(TreeNode* left, TreeNode* right) {

        if (!left || !right) return left == right;

        else if (left->val != right->val) return false;

        else return compare(left->left, right->right) && compare(left->right, right->left);

    }

};
```


### 注意事项
- 对判断left和right非空的巧妙实现```if (!left && !right) return left == right;```

## 迭代法
### 算法思路
不需要记录层数，就直接把根节点的左子树的外侧放入队列，再把根节点右子树的外侧放入队列。把镜像的两个节点连着放在一起，然后允许队列中进入空节点。
### 代码实现
```
class Solution {

public:

    bool isSymmetric(TreeNode* root) {

        queue<TreeNode*> q;

        if (root == nullptr) return true;

        q.push(root->left);

        q.push(root->right);

        while (!q.empty()) {

            TreeNode* left = q.front();

            q.pop();

            TreeNode* right = q.front();

            q.pop();

            if (!left && !right) continue;

            if (!left || !right || left->val != right->val) return false;

            q.push(left->left);

            q.push(right->right);

            q.push(left->right);

            q.push(right->left);

        }

        return true;

    }

};
```

# [104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/description/)
## 层序遍历方法
## 递归法
### 算法思路
求最大深度，即求根节点的高度。以下图为例，说明递归法。
要求3的深度，就求9和20的最大高度。先递归去求9的的高度，再递归去求20的高度，去最大值。
![[Pasted image 20250814021837.png]]
- 确定函数作用、返回类型和输入参数：函数的作用就是获得当前节点的高度，返回为高度int类型，输入参数为根节点
- 确定终止条件：当输入参数为空姐点的时候，高度为0，返回0
- 确定单层递归的逻辑：先求该节点左子树的深度，再求该节点的右子树的深度，那么该节点的深度就是二者最大值+1

# [111. 二叉树的最小深度](https://leetcode.cn/problems/minimum-depth-of-binary-tree/description/)
## 层序遍历法
## 递归法
### 算法思路
- 确定函数作用：返回当前节点的最小深度，那么返回类型为int
- 函数终止条件：如果当前节点为空，那么返回0
- 单层递归的逻辑：通过函数返回值分别得到当前节点左、右孩子的最小深度。若某一个孩子的深度为0，说明没有叶节点，那么当前节点的最小深度=另一侧孩子的深度 + 1
### 代码实现
```
class Solution {

public:

    int minDepth(TreeNode* root) {

        if (root == nullptr) return 0;

        int left = minDepth(root->left);

        int right = minDepth(root->right);

        if (left == 0) return right + 1;

        if (right == 0) return left + 1;

        return min(left, right) + 1;

    }

};
```
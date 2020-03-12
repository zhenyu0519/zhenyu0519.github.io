---
layout: post
title: 树型结构的基本类型以及遍历方法
categories: Tree
description: 树型结构的基本类型以及遍历方法,二叉树，平衡二叉树，完全二叉树，满二叉树，二叉搜索树，前序遍历，中序遍历，后续遍历
keywords: 树型结构的基本类型以及遍历方法,二叉树，平衡二叉树，完全二叉树，满二叉树，二叉搜索树，前序遍历，中序遍历，后续遍历，遍历方法，树型结构，理解
---

# 树型结构的基本类型以及遍历方法

![Tree](/images/blog/tree.jpg)

## 前言 

最近在复习树型结构，就顺便总结一下树型结构的基本种类和遍历树的基本方法。

## 树的种类

我们常用的树是二叉树（binary tree）很好理解，二叉树就是一棵树**最多**有两个分枝。当然树也可以是三个分枝，四个分枝，这里不做介绍。

二叉树里面有哪些种类？

### 完全二叉树(complete binary tree)

对于一颗二叉树，假设其深度为d（d>1）。**除了第d层外，其它各层的节点数目均已达最大值，且第d层所有节点从左向右连续地紧密排列**，这样的二叉树被称为完全二叉树

### 满二叉树(full binary tree)

这个由国内国外两种定义，国内认为满二叉树是完全二叉树的一种，**即除最后一层结点均无任何子节点外，每一层的所有结点都有两个子结点的树**

而国际的定义则不相同，国际上认为满二叉树的**每一个节点结点都恰好有零或两个孩子的树**

个人认为以国际为准

### 平衡二叉树(balanced binary tree) AVL tree

**当且仅当任何节点的两棵子树的高度差不大于1的二叉树**, 平衡二叉树也叫做AVL tree，其中红黑树是平衡二叉树的一种

### 二叉搜索树(binary search tree)

* 若任意节点的左子树不空，则左子树上所有节点的值均小于它的根节点的值；
* 若任意节点的右子树不空，则右子树上所有节点的值均大于或等于它的根节点的值；
* 任意节点的左、右子树也分别为二叉查找树；
二叉搜索树通常用于查找

## 树的遍历

树的遍历方式有三种分别是前序遍历，中序遍历，后续遍历

### 前序遍历(preorder traversal)

根结点 ---> 左子树 ---> 右子树

### 中序遍历(inorder traversal)

左子树 ---> 根结点 ---> 右子树

### 后序遍历(postorder traversal)

左子树 ---> 右子树 ---> 根结点 


以上遍历实现的方法通常有两种，递归方法(recursive solution)和迭代方法(iterative solution)，递归方法就是平时说的深度优先搜索算法(Depth-First-Search)而迭代方法就是广度优先搜索算法(Breadth-First-Search)

具体实现是这样的：

### 前序遍历

#### 递归方法(深度优先)
```python
def preorderTraversal(self, root: TreeNode) -> List[int]:
        def dfs(root, res):
            if not root: return
            res.append(root.val)
            dfs(root.left, res)
            dfs(root.right, res)
            return res
        if not root: return []
        return dfs(root,[])
```

#### 迭代方法(广度优先)
```python
def preorderTraversal(self, root: TreeNode) -> List[int]:
        stack = [root]
        res = []
        while stack:
            node = stack.pop()
            if node:
                res.append(node.val)
                stack.append(node.right)
                stack.append(node.left)
        return res
```

### 中序遍历

#### 递归方法(深度优先)
```python
def inorderTraversal(self, root: TreeNode) -> List[int]:
        if root is None: return []
        def dfs(root,res):
            if root is None: return
            dfs(root.left,res)
            res.append(root.val)
            dfs(root.right,res)
            return res
        return dfs(root,[])
```

#### 迭代方法(广度优先)
```python
def inorderTraversal(self, root: TreeNode) -> List[int]:
        stack = [root]
        res = []
        while stack:
            node = stack.pop()
            if node:
                stack.append(node.right)
                stack.append(node)
                stack.append(node.left)
            else:
                if stack:
                    node = stack.pop()
                    res.append(node.val)
```

### 后序遍历

#### 递归方法(深度优先)
```python
def preorderTraversal(self, root: TreeNode) -> List[int]:
        def dfs(root, res):
            if not root: return
            res.append(root.val)
            dfs(root.left, res)
            dfs(root.right, res)
            return res
        if not root: return []
        return dfs(root,[])
```

#### 迭代方法(广度优先)
```python
def preorderTraversal(self, root: TreeNode) -> List[int]:
        stack = [root]
        res = []
        while stack:
            node = stack.pop()
            if node:
                res.append(node.val)
                stack.append(node.right)
                stack.append(node.left)
        return res
```
### 根据前序中序后序遍历后的结果创造对应的二叉树

1. 这里有个小技巧，前序遍历结果中的第一个node和后序遍历中的最后一个node一定是root node。
2. 而中序遍历结果中的root node一定是左子树和右子树的分界线。
3. 前序遍历的结果组成一定是[root,left,right]
4. 后续遍历的结果组成一定是[left,right,root]
根据以上这些性质，只要得到前序中序的结果或者后序中序的结果，那么我们一定可以创造对应的二叉树。

## 判断树型结构的具体问题

### 判断一个树是否是完全二叉树

根据完全二叉树的定义知道，判断它是否是完全二叉树主要看两个条件是否满足。一是这个树除了最后一层以外，其他层是否是满二叉树，也就是每层都被节点填满。第二就是最后一层的节点是否从左向右依次紧密相连。既然要看层是否被填满，那么用广度优先是最好的方法。按照每一次的顺序把每个出现的节点依次放到数组里面，看一下数组的长度和最后一个节点所代表的index是否一致。

### 判断一个树是满二叉树

**国内的满二叉树**
因为满二叉树是一种特殊的完全二叉树，与其不同的是满二叉树每一层都必须被节点填满，所以我们只需要看一下最后一个节点所代表的index是否和满二叉树该有的节点数量一致即可。那么满二叉树该有的节点数量怎么得到呢？答案是根据树的高度得到。一个二叉树最多节点的数量是=2^n-1这里n是树的高度(height)从n=1开始。此外还有个公式也很有用，那就是求层节点最大数量=2^(n-1)这里n是树的层数(level)从n=1开始。

**国际的满二叉树**
国际定义的满二叉树相对国内定义要复杂的多，首先满二叉树的节点个树一定是奇数。其次一旦一个节点有左子节点，那么这个节点必然有右子节点。最后一个一旦一个节点没有左子节点，那么它必然也没有右子节点。一定用到深度优先算法。

### 判断一个树是平衡二叉树

这个相对复杂一些，根据定义，平衡二叉树的任何节点的两颗子树高度差不大于1。那么我们就要得到任何节点以及它的两棵子树。这里选择用深度优先搜索比较容易得到每个节点的子树。然后递归的比较两棵子树的高度差是否小于1

关于树先总结到这里，下次有机会说一下红黑树。
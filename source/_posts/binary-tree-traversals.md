---
title: 二叉树——几种遍历的非递归模板
date: 2020-07-08 09:48:47
tags: [二叉树]
categories: 'leetcode'
---

二叉树遍历的递归代码比较精简，非递归的迭代思路也常常在leetcode中遇到，本文整理了几种非递归的java代码作为模板。

<!--more-->

##  前序遍历

[二叉树的前序遍历-leetcode-144]( https://leetcode-cn.com/problems/binary-tree-preorder-traversal/ )

```java
class Solution {
    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int x) {
            val = x;
        }
    }
    public List<Integer> preorderTraversal(TreeNode root) {
        List<Integer> list = new LinkedList<>();
        Stack<TreeNode> stack = new Stack<>();
        if (root == null) return list;
        stack.push(root); //根结点入栈
        TreeNode p;
        while (!stack.empty()) {
            p = stack.pop();
            list.add(p.val);
            if (p.right != null) { //右节点先入栈 后出
                stack.push(p.right);
            }
            if (p.left != null) {
                stack.push(p.left);
            }
        }
        return list;
    }
}
```

##  中序遍历

[二叉树的中序遍历-leetcode-94]( https://leetcode-cn.com/problems/binary-tree-inorder-traversal/ )

```java
class Solution {
    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int x) {
            val = x;
        }
    }
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> ans = new ArrayList<>();
        Stack<TreeNode> stack = new Stack<>();
        TreeNode p = root;
        while (p != null || !stack.isEmpty()) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            }
            p = stack.pop();
            ans.add(p.val);
            p = p.right;
        }
        return ans;
    }
}
```



##  后序遍历

[二叉树的后序遍历-leetcode-145]( https://leetcode-cn.com/problems/binary-tree-postorder-traversal/ )

```java
class Solution{
    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int x) {
            val = x;
        }
    }
    // 利用一个指针标记访问过的右子树结点 当访问过右子树在访问根结点
    public List<Integer> postorderTraversal(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> list = new LinkedList<>();
        if (root == null) return list;
        TreeNode p = root, r = null; //r结点用来表示当前的左孩子是否被访问
        while (!stack.empty() || p != null) {
            while (p != null) {
                stack.push(p);
                p = p.left;
            } //走到最左边的结点
            p = stack.peek();
            if (p.right != null && p.right != r) { 
                //存在右子树并且没有被访问则先访问右子树，否则访问该节点
                stack.push(p.right);
                p = p.right;
                p = p.left;
            } else {
                p = stack.pop();
                list.add(p.val);
                r = p;
                p = null; //让p指针不要乱指
            }
        }
        return list;
    }
    /*前序：根->左->右 
    后序：左->右->根 
    后序翻转  根->右->左 把后序当作前序然后在翻转一次 （不是严格意义的后序遍历）*/
    public List<Integer> postorderTraversal_1(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> list = new LinkedList<>();
        if (root == null) return list;
        stack.push(root);
        while (!stack.empty()) {
            TreeNode p = stack.pop();
            list.add(0, p.val);
            if (p.left != null) {
                stack.push(p.left);
            }
            if (p.right != null) {
                stack.push(p.right);
            }
        }
        return list;
    }
    
    //阿里面试题
    /*仅利用栈去判断该节点是否为父结点，创新性思路是每次在栈中压入父节点后压入null节点，
    之后再依次压入右子节点和左子节点。*/
    public List<Integer> postorderTraversal_2(TreeNode root) {
        Stack<TreeNode> stack = new Stack<>();
        List<Integer> list = new ArrayList<>();
        if (root == null) return list;
        stack.push(root);
        while (!stack.empty()) {
            TreeNode p = stack.peek();
            if (p == null) {
                stack.pop();
                list.add(stack.pop().val);
            } else {
                stack.push(null);
                if (p.right != null) {
                    stack.push(p.right);
                }
                if (p.left != null) {
                    stack.push(p.left);
                }
            }
        }
        return list;
    }
}
```

##  层序遍历

[二叉树的层序遍历 (leetcode-102)]()

```java
class Solution {
    public class TreeNode {
        int val;
        TreeNode left;
        TreeNode right;
        TreeNode(int x) {
            val = x;
        }
    }
	//1.借用辅助栈
    public List<List<Integer>> levelOrder(TreeNode root) {
        List<List<Integer>> res = new ArrayList<>();
        Queue<TreeNode> queue = new LinkedList<>();
        if (root == null) return res;
        queue.add(root);
        while (!queue.isEmpty()) {
            int count = queue.size(); //记录当前层的个数
            List<Integer> list = new ArrayList<>();
            while (count > 0) {
                TreeNode node = queue.poll();
                list.add(node.val); //当前层的节点加入该list
                if (node.left != null)
                    queue.add(node.left);
                if (node.right != null)
                    queue.add(node.right);
                count--;
            }
            res.add(list);
        }
        return res;
    }
       
    //2.递归思路
   public List<List<Integer>> levelOrder_2(TreeNode root) {
        List<List<Integer>> ans = new ArrayList<>();
        DFS(root, 0, ans);
        return ans;
    }
    //通过列表的长度来确认是否添加子列表  列表的长度就是二叉树的高度
    public void DFS(TreeNode p, int level, List<List<Integer>> ans) {
        if (p == null) {
            return;
        }
        if (ans.size() <= level) {
            List<Integer> list = new ArrayList<>();
            ans.add(list);
        }
        ans.get(level).add(p.val);
        DFS(p.left, level + 1, ans);
        DFS(p.right, level + 1, ans);
    } 
}
```


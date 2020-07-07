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
    public List<Integer> postorderTraversal(TreeNode root) {

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


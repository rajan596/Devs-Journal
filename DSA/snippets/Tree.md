

## Iterative traversal

```java
function Inorder(head)
   if head==NULL 
        return
   stack s
   while(1)
        if head!=NULL
             s.push(head)
             head=head->left
       else
            if s.empty()
                break
            head=s.top()
            print head->data
            head=head->right

function Preorder(head)
    if head==NULL
        return
    stack s
    s.push(head)
    while(!s.empty())
         head=s.top()
         s.pop()
         print head->data
         if head->right!=NULL
              s.push(head->right)
         if head->left !=NULL
              s.push(head->left) 

function postOrder(head)
     if head==NULL
          return
     stack s1,s2
     s1.push(head)
     while(!s1.empty())
         root=s1.top()
         s1.pop()
         s2.push(root)
         if root->left!=NULL
            s1.push(head->left)
         if root->right!=NULL
            s1.push(head->right)
    while(!s2.empty())
       root=s2.top()
       print root->data
       s2.pop()
```


## Lowest Common Ancestor of a Binary Tree

https://leetcode.com/problems/lowest-common-ancestor-of-a-binary-tree/

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root==null) return root;
        if(root == p || root == q) return root;

        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);  

        if(left != null && right != null) return root;
        if(left!=null) return left;
        if(right!=null) return right;
        return null;
    }
}
```

##  Lowest Common Ancestor of a Binary Search Tree

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null)
            return null;
        if (p.val > q.val) {
            return lowestCommonAncestor(root, q, p);
        }
        if (p.val <= root.val && q.val >= root.val)
            return root;
        else if (q.val <= root.val)
            return lowestCommonAncestor(root.left, p, q);
        else
            return lowestCommonAncestor(root.right, p, q);
    }
}
```
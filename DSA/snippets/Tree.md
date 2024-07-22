

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

## Zigzag Order traversal

```java
    public List<List<Integer>> zigzagLevelOrder(TreeNode root) {
        Stack<TreeNode> st = new Stack<>();
        Stack<TreeNode> st2 = new Stack<>();
        List<List<Integer>> ans = new ArrayList<>();
        if (root == null)
            return ans;
        st.push(root);
        int dir = 1;
        List<Integer> temp = new ArrayList<>();
        while(true) {
            TreeNode node=null;
            if (dir == 1) {
                if (st.empty()) {
                    dir = -1;
                    ans.add(temp);
                    temp = new ArrayList<>();

                    if(st2.empty()) break;
                } else {
                    node = st.pop();
                    temp.add(node.val);
                    if (node.left != null)
                        st2.push(node.left);
                    if (node.right != null)
                        st2.push(node.right);
                }
            } else {
                if (st2.empty()) {
                    dir = 1;
                    ans.add(temp);
                    temp = new ArrayList<>();

                    if(st.empty()) break;
                } else {
                    node = st2.pop();
                    temp.add(node.val);
                    if (node.right != null)
                        st.push(node.right);
                    if (node.left != null)
                        st.push(node.left);
                }
            }
        }

        return ans;
    }
```

## Top view of a Binary tree

```java
    static ArrayList<Integer> topView(Node root) {
        // add your code
        ArrayList<Integer> ans = new ArrayList<>();
        if(root==null) return ans;
        
        int left=0,right=0;
        Map<Integer, Integer> mp = new HashMap<>();
        Queue<Tuple> q = new LinkedList<>();
        q.add(new Tuple(root,0));
        
        while(!q.isEmpty()) {
            Tuple tuple = q.peek(); q.remove();
            Node node = tuple.node;
            int x=tuple.x;

            left=Math.min(left,x);
            right=Math.max(right,x);

            // Do Processing
            if(!mp.containsKey(x)) {
                mp.put(x,node.data);
            }
            
            if(node.left!=null) q.add(new Tuple(node.left, x-1));
            if(node.right!=null) q.add(new Tuple(node.right, x+1));
        }
        
        for(int i=left;i<=right;i++){
            if(mp.containsKey(i))
                ans.add(mp.get(i));
        }
        
        return ans;
    }
```

## Bottom view of Binary Tree

Bottom view problem is similar to Top view problem with difference of mapping. Here if any node is found again at given node then previous node will be overriden.

```java
if(!mp.containsKey(x)) {
    mp.put(x,node.data);
}
```
to
```java
    mp.put(x,node.data);
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

## Right view of Binary Tree

```java
    public List<Integer> rightSideView(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        List<Integer> ans = new ArrayList<>();
        if(root==null) return ans;

        queue.add(root);

        while(!queue.isEmpty()){
            int size = queue.size();
            ans.add(queue.peek().val);
            for(int i=0;i<size;i++){
                TreeNode node = queue.peek(); queue.remove();
                if(node.right!=null) queue.add(node.right);
                if(node.left!=null) queue.add(node.left);
            }
        }

        return ans;
    }
```

## Level Order traversal of Binary Tree (From Right to Left Dif)

```java
    public List<Integer> rightSideView(TreeNode root) {
        Queue<TreeNode> queue = new LinkedList<>();
        if(root==null) return ans;
        queue.add(root);
        while(!queue.isEmpty()){
            int size = queue.size();
            for(int i=0;i<size;i++){
                TreeNode node = queue.peek(); queue.remove();
                if(node.right!=null) queue.add(node.right);
                if(node.left!=null) queue.add(node.left);
            }
        }

        return ans;
    }
```

## Morris Traversal in Binary Tree



## Flatten Binary tree to Linked List in Pre-order
Need to use similar concept of Morris traversal

https://leetcode.com/problems/flatten-binary-tree-to-linked-list/description/

```java
    public void flatten(TreeNode root) {
        TreeNode curr=root;
        while(curr != null){
            TreeNode currLeft = curr.left;
            if(currLeft == null) {
                curr = curr.right;
            } else {
                TreeNode temp = currLeft;
                while(temp.right != null){
                    temp = temp.right;
                }
                temp.right = curr.right;
                curr.right=curr.left;
                curr.left=null;
                curr=currLeft;
            }
        }
    }
```

## Ceil in BST

```java

```
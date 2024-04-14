# Java

Noting down important syntax in Java to solve DSA problems

## String

```java
String s = "abcd";
s.length();

```

## Arrays

```java
// This will initialize an array of size = len
int arr[] = new int[len];
int arrSize = arr.length;

// initialise array with 2 elements...
int[] arr = new int[]({0,i});

// Sort an array
Arrays.sort(arr);

// Sort an array if arr is ArrayList
List<Integer> arr = new ArrayList();
Collections.sort(arr);
```

## String operations in Java
```java
String s = new Strng();
s="abcd"; // assing string 
s.charAt(i); // get ith character from string
for(char c: a){} // iterate over string
s.substr(startIndex,len); // find substring from startIndex having length=len
for (char c : s.toCharArray()) {}
```
## Matric

```java
int dp[][] = new int[m][n];

// Fill data in matrix
Arrays.fill(visited, false);
```

## Sort in Java

```java
// Sorting of 1D array. ASC order
Arrays.sort(intervals, (left, right) -> Integer.compare(left,right));
 Arrays.sort(intervals, ((a,b)-> a[0]-b[0]));

// Sorting of 2d array. ASC order
Arrays.sort(intervals, (left, right) -> Integer.compare(left[0],right[0]));

// Sorting in priority queue based on Object.F value
PriorityQueue<Node> pq = new PriorityQueue<>((left,right)-> left.f-right.f);

```

## LinkedList in Java

```java
// Linked list of array
LinkedList<int[]> result = new LinkedList<>();
result.add(new int[] {1, 2});
result.toArray(new int[result.size()][]);


```
## Map

```java
Map<Integer, Integer> mp = new HashMap<>();
mp.put(1, 100);
boolean exists = mp.containsKey(1);
mp.getOrDefault(15, -1); // default value in second argument

for (Map.Entry<String,String> entry : mp.entrySet())  {} // Iterator
for (Integer key : gfg.keySet()) {}   // iterate over keys
for (Integer value : mp.values()){ } // iterate over values

```

## Stack in Java

```java
Stack<E> stack = new Stack<E>();
stack.push(12);
Integer topElement =  stack.pop();
Integer element = (Integer) stack.peek(); // It does not delete element
```

## Queue in Java

```java
Queue<Integer> queue = new LinkedList<>();
queue.add(1);
Integer pickedElement = queue.peek(); // returns the front element in queue
Integer xx = queue.remove(); // removed the front element from queue and returns it
queue.size(); // returns size of a queue
queue.clear(); // removes all elements from queue
queue.isEmpty() // tells is queue is empty

// Priority queue
Queue<String> queue = new PriorityQueue<Obj> ();  
queue.add("abce");
queue.remove("abcd"); // removed given element
String topElementInQueue = queue.peek();
String topElementInQueueAndRemoveIt = queue.poll();

Iterator iterator = queue.iterator();
while(iterator.hasNext()){
    iterator.next();  // gives element at given iteration position
}

PriorityQueue<Node> pq = new PriorityQueue<>((left,right)-> left.f-right.f);

// Thread safe priority queue
Queue<Integer> threadSafeQueque= new PriorityBlockingQueue<Integer>();

```

## DFS
Sample code for DFS in matrix from (i,j)
```java
void dfs(int i, int j, char[][] grid,boolean visited[][]){
    int m=grid.length;
    int n=grid[0].length;
    if(i>=m || i<0 || j<0 || j>=n || visited[i][j] || grid[i][j] != '1') return;
    visited[i][j]=true;
    dfs(i+1,j,grid,visited);
    dfs(i-1,j,grid,visited);
    dfs(i,j+1,grid,visited);
    dfs(i,j-1,grid,visited);
}
```

## Heap in Java

## Trie in Java

## Graph
- https://amortizedminds.wordpress.com/2015/06/30/djakstras-shortest-path-algorithm/
- 

## Bitwise operators

## Topological Sort

```java
    public boolean canFinish(int numCourses, int[][] prerequisites) {
        Map<Integer, List<Integer>> graph = new HashMap<>();
        int inedge[] = new int[numCourses];
        int N = prerequisites.length;
        for(int i=0;i<N;i++){
            if(!graph.containsKey(prerequisites[i][1])) {
                graph.put(prerequisites[i][1],new ArrayList<>());
            }
            graph.get(prerequisites[i][1]).add(prerequisites[i][0]);
            inedge[prerequisites[i][0]]++;
        }
        Stack<Integer> st = new Stack<>();
        for(int i=0;i<numCourses;i++){
            if(inedge[i] == 0) st.push(i);
        }
        while(!st.empty()) {
            Integer top = st.pop();
            List<Integer> nbrs = graph.get(top);
            if(nbrs!=null) {
                for(Integer nbr: nbrs){
                    inedge[nbr]--;
                    if(inedge[nbr]==0) st.push(nbr);
                }
            }
        }
        for(int i=0;i<numCourses;i++) {
            if(inedge[i] > 0) return false;
        }
        return true;
    }
```

## Heap
- Complete binary tree
- Heap Operations
    - Insert.
    - Extract min/max
    - Heapify

```java

```

## Tree

### Iterative traversal

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

## Disjoint Set Union
https://amortizedminds.wordpress.com/2015/07/07/disjoint-set-union-data-structure/
https://amortizedminds.wordpress.com/2015/08/01/detect-cycle-in-an-undirected-graph-using-disjoint-set-union/

## Minimum spanning tree

https://amortizedminds.wordpress.com/2015/07/23/minimum-spaning-tree-kruskals-algorithm/

## Sorting Algorithms

### QuickSort algorithm

https://amortizedminds.wordpress.com/2015/07/21/quick-sort-algorithm/


### Merge Sort

https://amortizedminds.wordpress.com/2015/07/18/merge-sort-algorithm/
https://amortizedminds.wordpress.com/2015/07/19/counting-inversion-spoj-invcnt/

## nCr % m

https://amortizedminds.wordpress.com/2015/07/02/ncr-m/
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

### Binary Search in Array
```java
public class BinarySearchUsingLib {
    public static void main(String[] args) {

        /**
         *
         index of the search key, if it is contained in the array; otherwise, (-(insertion point) - 1).
         The insertion point is defined as the point at which the key would be inserted into the array:
         the index of the first element greater than the key,
         * */

        System.out.println(">> " + Arrays.binarySearch(new int[]{1,100,200,500},1));        // 0
        System.out.println(">> " + Arrays.binarySearch(new int[]{1,100,200,500},100));      // 1
        System.out.println(">> " + Arrays.binarySearch(new int[]{1,100,200,500},150));      // -3
        System.out.println(">> " + Arrays.binarySearch(new int[]{1,100,200,500},600));      // -5
    }
}

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

## Double ended queue in Java

```java
Deque<String> deque = new ArrayDeque<>();
deque.addFirst(1); // adds to the head side of queue
deque.addLast(2);  // adds to the tail side of queue

int first = deque.removeFirst();
int last = deque.removeLast();


```

## Map in Java

```java
Map<Integer, Integer> mp = new HashMap<>();
mp.put(1, 100);
boolean exists = mp.containsKey(1);
mp.getOrDefault(15, -1); // default value in second argument

for (Map.Entry<String,String> entry : mp.entrySet())  {} // Iterator
for (Integer key : gfg.keySet()) {}   // iterate over keys
for (Integer value : mp.values()){ } // iterate over values

```

## TreeMap in Java
- Implemented using self balanced Red black tree
- log(N) operations for add/remove/get

```java

```

## Set in Java

### TreeSet
- Implementation of self balanced Binary search tree - Red black Tree
- Add/Delete/Remove -> O(LogN)
- Set <--(Extends)--- SortedSet <--(Extends)--- TreeSet

```java
public class TreeSetExample {
    public static void main(String[] args) {
        TreeSet<Integer> ts = new TreeSet<>();
        ts.add(100);
        ts.add(350);
        ts.add(10);

        // Finds element <= given element
        System.out.println(ts.lower(80)); // 10
        System.out.println(ts.lower(500)); // 350
        System.out.println(ts.lower(350)); // 100

        // Finds element > given element
        System.out.println(ts.higher(6)); // 10
        System.out.println(ts.higher(10)); // 100
        System.out.println(ts.higher(200)); // 350
        System.out.println(ts.higher(350)); // null
        System.out.println(ts.higher(500)); // null

        // Removed the element from Set
        ts.remove(100);
        ts.pollFirst(); // Removes the first element
        ts.pollLast(); // Removes the last element

        ts.add(19);

        for(Integer value: ts) {
            System.out.println("--> " + value);
        }

        TreeSet<Integer> ts2 = new TreeSet<>((l,r)->r-l);
        ts2.add(100);
        ts2.add(200);
        for(Integer value: ts2) {
            System.out.println("-->> " + value); // Prints 200,100
        }
    }
}
```

## Stack in Java

```java
Stack<E> stack = new Stack<E>();
stack.push(12);
Integer topElement =  stack.pop();
Integer element = (Integer) stack.peek(); // It does not delete element
```

### Monotonic stack 

- Monitonic stack presers array element sequence either in asc or desc order.
- Many questions can be solved using monotonic stack 

```java
class Solution {
    public int[] nextGreaterElement(int[] nums1, int[] nums2) {
        Stack<Integer> st = new Stack<>();
        int len = nums2.length;
        int[] mp = new int[10001]; 
        Arrays.fill(mp,-1);
        for(int i=len-1;i>=0;i--){
            int nextGreater = -1;
            while(!st.empty() && st.peek() < nums2[i]) {
                st.pop();
            }
            if(!st.empty())  { 
                System.out.println(st.peek());
            };
            st.push(nums2[i]);
        }
    }
}
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

/// Double ended queue / Deque
Deque<Integer> deq = new LinkedList<>(); // new ArrayDeque<>();
deq.addFirst(1);
deq.addLast(2); // OR deq.tail();
deq.removeFirst();
deq.removeLast();
deq.peekFirst();
deq.peekLast();
deq.size();


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

## Trie in Java

```java
class Trie {

    class TrieNode {
        public boolean ends = false;
        // mp to store 'a' -> TrieNode mapping... 
        public Map<Character, TrieNode> mp = new HashMap<>();
    }

    TrieNode root;

    public Trie() {
        root = new TrieNode();
    }
    
    public void insert(String word) {
        TrieNode curr = root;
        for(char c: word.toCharArray()){
            if(!curr.mp.containsKey(c)) {
                curr.mp.put(c, new TrieNode());
            }
            curr = curr.mp.get(c);
        }
        curr.ends = true;
    }
    
    public boolean search(String word) {
        TrieNode curr = root;
        for(char c: word.toCharArray()){
            if(curr.mp.containsKey(c)) {
                curr = curr.mp.get(c);
            } else {
                return false;
            }
        }
        return curr.ends;
    }
    
    public boolean startsWith(String prefix) {
        TrieNode curr = root;
        for(Character c: prefix.toCharArray()){
            if(curr.mp.containsKey(c)) {
                curr = curr.mp.get(c);
            } else {
                return false;
            }
        }
        return true;
    }
}
```

## 0/1 Knapsack Problem

In this problem there will be N items with its characteristics like Weight, Value and we will be given some constraint like Max weight carrying capacity. We will need to maximize the amount of values.

- The possible solution will be to check both options, pick the element or do not pick which can be done using recursive approach which checks all possible combinations.
- Recursive approach generated too many branches but Memoization can cut down on number of branches when stored repeated results.

```java
public class Solution{

    public static int dfs(int[] weight, int[] value, int n, int currIndex , int remWeight, int dp[][]){
        int ans=0;

        if(currIndex < n && dp[currIndex][remWeight]!=-1) {
            return dp[currIndex][remWeight];
        } 
        if(n - 1 == currIndex) {
            if(remWeight >= weight[currIndex]) {
                ans=value[currIndex];
            }
        } else {
            ans=dfs(weight, value, n , currIndex + 1 , remWeight , dp);
            if(remWeight - weight[currIndex] >=0){
                ans= Math.max(ans, value[currIndex] + dfs(weight, value, n , currIndex + 1 , remWeight - weight[currIndex], dp));
            }
        }
        dp[currIndex][remWeight] = ans;
        return ans;
    }

    static int knapsack(int[] weight, int[] value, int n, int maxWeight) {
        int dp[][] = new int[n+1][maxWeight+1];
        for(int i=0;i<=n;i++)
            Arrays.fill(dp[i], -1);

        return dfs(weight, value, n, 0 ,maxWeight, dp);
    }
}
```

## Graph

### BFS
```java
// int[][] grid
class Solution {
    public void doSomeStuff(Queue<int[]> q,int[][] grid,int[] node){}

    public int orangesRotting(int[][] grid) {
        Queue<int[]> q = new LinkedList<>(); // Create a Queue
        int m=grid.length;
        int n=grid[0].length;
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                if(grid[i][j] == 2) {
                    q.add(new int[]{i,j}); // Add to queue. Initial nodes
                }
            }
        }
        int time=0;
        while(!q.isEmpty()){
            int size = q.size();        // Find size of existing nodes in Queue
            for(int i=0;i<size;i++){    // Remove N nodes from queue
                int[] node = q.remove();
                doSomeStuff(q,grid,node); // Do processing and add next level nodes again in queue
            }
            if(q.size()>0) {
                time++;
            }
        }
        // ..... other code ....
    }
}

```

### DFS
```java
```

### Cycle Check in Graph
```java
class UnDirectedGraphCycleDetectUsingDFS {
    
    public boolean dfsCheckCycle(int V, ArrayList<ArrayList<Integer>> adj,int node,int parent,boolean visited[]){
        if(visited[node]) return true;
        visited[node] = true; // If already visited -> means there is cycle
        for(int adjNode: adj.get(node)){
            if(adjNode!=parent && dfsCheckCycle(V,adj,adjNode,node,visited)) return true;
        }
        return false;
    }
    
    // Function to detect cycle in an undirected graph.
    public boolean isCycle(int V, ArrayList<ArrayList<Integer>> adj) {
        // Code here
        boolean visited[] = new boolean[V];
        for(int i=0;i<V;i++){
            if(!visited[i]) {
                if(dfsCheckCycle(V,adj,i,i,visited)) return true;
            }
        }
        return false;
    }
}
```

### Cycle detection in Directed graph
- Using DFS: 
    - Need to maintain pathVisited and overall visited checks.
    - If path visited contains node we are traversing next that means there is cycle
    - make sure to de mark node from pathVisited after going back from visited path.
- Using BFS: Topological Sort - Indegree approach

### Shortest Path Algorithms

#### Weighed Undirected Graph | Dijkstra | PriorityQueue

- This will not work for -ve edge graphs.
- useful to find shortest distance from src node to all other nodes in graph.
- Algorithm:
    - Start with 
- Complexities
    - Time: $O(E*log(V))$
    - Space: $O(E)$

```java
class DijsktaUsingPQ
{
    //Function to find the shortest distance of all the vertices
    //from the source vertex S.
    static int[] dijkstra(int V, ArrayList<ArrayList<ArrayList<Integer>>> adj, int S)
    {
        // Write your code here
        PriorityQueue<Pair> pq = new PriorityQueue<>((x,y)->x.dist-y.dist);
        pq.add(new Pair(0,S));
        int dist[]= new int[V];
        Arrays.fill(dist, Integer.MAX_VALUE);
        dist[S]=0;
        
        while(!pq.isEmpty()){
            Pair top = pq.peek();
            pq.remove();
            int node = top.node;
            int distance = top.dist;
            
            for(ArrayList<Integer> nbr: adj.get(node)){
                int nbrNode = nbr.get(0);
                int nbrEdgeWt = nbr.get(1);
                int newDist = distance +nbrEdgeWt;
                if(newDist < dist[nbrNode]) {
                    dist[nbrNode]=newDist;
                    pq.add(new Pair(newDist, nbrNode));
                }
            }
        }
        return dist;
    }
}
```

#### Shortest path in Directed Acyclic graph - DAG

**Algorithm**
- Do topological sort and get the sequence
- Start with initial node... Go neighbour dist[nbr] = min(dist[nbr],dist[node] + W(node,nbr))
- Intuition: If we are at Node $node$ and we are able to find shortest path for all nodes which will come before $node$ then we can safely say that $node$'s shortest path will be shortest path for all its preceeding node + dist between preceeding node and current node 
- This will not work for Cyclic graph

```java
class ShortestPathUsingTopoSortDAG {

	public int[] shortestPath(int N,int M, int[][] edges) {
		//Code here
		List<List<int[]>> graph = new ArrayList<>();
		int[] ans = new int[N];
		Arrays.fill(ans, Integer.MAX_VALUE);
		int[] indegree = new int[N];
		
		// build graph
		for(int i=0;i<N;i++) {
		    graph.add(new ArrayList<>());
		}
		for(int i=0;i<M;i++){
		    int from = edges[i][0];
		    int to = edges[i][1];
		    int dist = edges[i][2];
		    graph.get(from).add(new int[]{to, dist});
		    indegree[to]++;
		}
		// Do topo Sort
		List<Integer> topoSortList = new ArrayList<>();
		Queue<Integer> q = new LinkedList<>();
		for(int i=0;i<N;i++){
		    if(indegree[i]==0) {
		        q.add(i);
		    }
		}
		while(!q.isEmpty()){
		    int no = q.peek();
		    q.remove();
		    topoSortList.add(no);
		       
		    for(int[] nbr: graph.get(no)){
		        int nbrnode= nbr[0];
		        indegree[nbrnode]--;
		        if(indegree[nbrnode]==0) {
		            q.add(nbrnode);
		        }
		    }
		}
		
		// Algorithm to calculate shortest path
		ans[0]=0;
		for(int i=0;i<N;i++){
		    int no = topoSortList.get(i);
		    int dist = ans[no];
		    for(int[] nbr: graph.get(no)){
		        int nbrnode = nbr[0];
		        if(dist != Integer.MAX_VALUE) // If this node is reachable then only update nbr node dist
		            ans[nbrnode] = Math.min(ans[nbrnode], dist + nbr[1]);
		    }
		    if(dist==Integer.MAX_VALUE) {
		        ans[no]=-1;
		    }
		}
		return ans;
	}
}
```

### Bellman-Ford Algorithm
- Dijkstra fails when negative edge or negative cycle is there
- Works with directed graph. So change undirected graph -> directed graph
- Finds shortest path from src to all other nodes
- Algorithm
    - Relax edges V-1 times
    - itrate all edges V-1 times
    - If dist[v] > dist[u] + wt then update distance of v
- Why V-1 times relaxation is required ?
    - In worst case in single iteration only 1 step shortest distance will be calculated
    - In best case in a single iteration only shortest distance till dest node will be calculated
    - This depends on how edges are iterated or given in input
- How to detect negative cycle ?
    - If on V'th iteration still esge gets relaxed that means its having cycle.

```java
/*
*   edges: vector of vectors which represents the graph
*   S: source vertex to start traversing graph with
*   V: number of vertices
*/
class Solution {
    static int[] bellman_ford(int V, ArrayList<ArrayList<Integer>> edges, int S) {
        int dist[]=new int[V];
        Arrays.fill(dist,(int)1e8);
        dist[S]=0;
        for(int i=0;i<V-1;i++){ // Relax edges V-1 times
            for(ArrayList<Integer> edge: edges){
                int u=edge.get(0);
                int v=edge.get(1);
                int wt=edge.get(2);
                if(dist[u]!=1e8 && dist[v] > dist[u] + wt){
                    dist[v]=dist[u] + wt;
                }
            }
        }
        // If the graph contains negative cycle then V'th iteration will give even shorter distance
        for(ArrayList<Integer> edge: edges){
            int u=edge.get(0);
            int v=edge.get(1);
            int wt=edge.get(2);
            if(dist[u]!=1e8 && dist[v] > dist[u] + wt){
                return new int[]{-1};
            }
        }
        return dist;
    }
}
```

### Floyd Warshall Algorithm

- Multi source shortest path algorithm
- Finds shortest distance from all nodes to all other nodes
- Algorithm
    - Fix via from 0 to N-1
    - Select each distinct u and v and go $via$ i.e, dist[u][v]=Math.min(dist[u][v], dist[u][via] + dist[via][v]);
- Can also detect negative cycle
- Complexity: $O(V^3)$

```java
class FloydWarshallShortestPath
{
    public void shortest_distance(int[][] matrix)
    {
        int n=matrix.length;
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                if(matrix[i][j]==-1) {
                    matrix[i][j] = (int)1e9;
                }
                if(i==j) matrix[i][j]=0;
            }
        }
        
        for(int via=0;via<n;via++){
            for(int i=0;i<n;i++){
                for(int j=0;j<n;j++){
                    matrix[i][j]=Math.min(matrix[i][j], matrix[i][via] + matrix[via][j]);
                }
            }
        }
        
        for(int i=0;i<n;i++){
            for(int j=0;j<n;j++){
                if(matrix[i][j]==1e9) {
                    matrix[i][j] = -1;
                }
            }
        }

        for(int i=0;i<n;i++){
            if(i==j && matrix[i][i] < 0) {
                System.out.printl("Cycle detected...");
            }
        }
    }
}
```

### Disjoint Set Union [IMP]
- Can answer queries like Does x and y part of the same component/set/graph segment
- Maintain leader node in each segment using size / height.
- Rank can not be reduced
- Time Complexity: 
    - Union: $O(4*alpha)$ ~ $O(1)$
    - findParent:

```java
class DisjointSetUnion {
    int[] parent;
    int[] rank;
    int[] size;

    public DisjointSetUnion(int _size){
        parent = new int[_size+1];
        rank = new int[_size+1];
        size = new int[_size+1];
        for(int i=0;i<=_size;i++) {
            parent[i]=i;
        }
    }

    public int findParent(int node){
        int leader = node;
        while(parent[leader] != leader){
            leader = parent[leader];
        }
        while(parent[node] != node){
            node = parent[node];
            parent[node]=leader; // this is leader node for this segment
        }
        return leader;
    }

    public boolean isSameComponent(int x,int y){
        return findParent(x) == findParent(y);
    }

    /* rank is distoreted in this approach */
    public void unionByRank(int x,int y){
        int xParent = findParent(x);
        int yParent = findParent(y);

        if(xParent == yParent) return;

        int xRank = rank[xParent];
        int yRank = rank[yParent];

        if(xRank >= yRank) {
            parent[yParent] = xParent; // Path compression
            rank[xParent]=Math.max(rank[xParent],1+rank[yParent]); // Rank by height
        } else {
            parent[xParent] = yParent;
            rank[yParent]=Math.max(rank[xParent],1+rank[yParent]); // Rank by height
        }
    }

    // This is union by size
    public void union(int x,int y){
        int xParent = findParent(x);
        int yParent = findParent(y);

        if(xParent == yParent) return;
        int xSize = size[xParent];
        int ySize = size[yParent];
        if(xSize >= ySize) {
            parent[yParent] = xParent;    // Path compression
            size[xParent]+=size[yParent]; // Rank by size
        } else {
            parent[xParent] = yParent;
            size[yParent]+=size[xParent]; // Rank by size
        }
    }
}
```

Mini version
```java
class DSU {
    int parent[];
    public DSU(int size){
        parent = new int[size];
    }

    public int findParent(int x){
        if(parent[x] == x) return x;
        return parent[x] = findParent(parent[x]);
    }

    public void union(int x, int y){
        int px = findParent(x);
        int py = findParent(y);

        parent[py] = px;
    }
}
```

https://amortizedminds.wordpress.com/2015/07/07/disjoint-set-union-data-structure/
https://amortizedminds.wordpress.com/2015/08/01/detect-cycle-in-an-undirected-graph-using-disjoint-set-union/

### Minimum spanning tree
- Undirected Weighted graph 
- MST: Tree in which we have N nodes and N-1 edges & all nodes are reachable to each other

#### Prim's Algorithm
- start with any given node
- add start node in Min PQ along with weight
- Remove top element each time and mark it as visited. 
    - Add adjacent elements {adj, weight}
- Repeat until PQ is not empty
- Intuition: In this approach there are two sets of vertices. one that is in growing spanning tree and the rest. The approach is simple. Just add cheapest vertices in growing spanning tree. and meanwhile check if there is cycle or not. Starting vertices can be chosen randomly. Maintain cheapest vertices data in Priority Queue.
- Time Complexity: O(E log(E))

```java
class PrimsAlgorithmMST {
    static int spanningTree(int V, int E, List<List<int[]>> adj) {
        // Code Here.
        int visited[] = new int[V];
        int ans=0;
        PriorityQueue<Node> pq = new PriorityQueue<>((l,r)->l.w-r.w);
        pq.add(new Node(0,0));
        while(!pq.isEmpty()){
            Node top = pq.peek();
            pq.remove();
            
            int node = top.node;
            int wt = top.w;
            
            if(visited[node] == 1) continue;
            
            visited[node]=1; // Add node to MST
            ans+=wt;
            for(int[] nbr: adj.get(node)){
                int end = nbr[0];
                int w = nbr[1];
                
                if(visited[end] == 0) {
                    pq.add(new Node(end,w));
                } 
            }
        }
        return ans;
    }
}
```

#### Kruskal's Algorithm
- Greedy approach
- Start with Empty MST ( Only nodes , no edges )
- Sort all edges in non descending order
- start with min. weighted edge to max. weighted edge
    - If this edge is creating cycle then ignore it
    - Else Add this edge in MST.

https://amortizedminds.wordpress.com/2015/07/23/minimum-spaning-tree-kruskals-algorithm/

```java
class Solution {
    static int spanningTree(int V, int E, List<List<int[]>> adj) {
        // Code Here.
        int visited[] = new int[V];
        List<Node> edges = new ArrayList<>();
        DisjointSetUnion dsu=new DisjointSetUnion(V);
        int ans=0;
        
        for(int i=0;i<adj.size();i++){
            for(int[] e : adj.get(i)){
                int start = i;
                int end = e[0];
                int wt = e[1];
                
                if(start < end)
                    edges.add(new Node(start,end,wt));
            }
        }
        Collections.sort(edges, (l,r)->l.w-r.w);
        
        for(Node edge: edges){
            int start = edge.start;
            int end = edge.end;
            int wt = edge.w;
            
            if(!dsu.isSameComponent(start,end)) {
                ans+=wt;
                dsu.unionBySize(start,end);
            }
        }
        
        return ans;
    }
}
```

## Topological Sort

```java
/**** USING BFS *****/
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

/**** USING DFS *****/
class TopologicalSortUsingDFS
{
    
    static public void dfs(int node, Stack<Integer> st, boolean visited[], 
        ArrayList<ArrayList<Integer>> adj
    ){
        if(visited[node]) return;
        visited[node]=true;
        for(int nbr: adj.get(node)) {
            dfs(nbr,st,visited,adj);
        }
        st.push(node);
    }
    
    //Function to return list containing vertices in Topological order. 
    static int[] topoSort(int V, ArrayList<ArrayList<Integer>> adj) 
    {
        // add your code here
        boolean visited[] = new boolean[V];
        Stack<Integer> st = new Stack<>();
        for(int i=0;i<V;i++){
            if(!visited[i]) {
                dfs(i,st,visited,adj);
            }
        }
        int ans[]=new int[V];
        int index=0;
        while(!st.isEmpty()){
            ans[index++]=st.pop();
        }
        return ans;
    }
}

```

## Heap
- Complete binary tree
- Heap Operations
    - Insert
        - new elements gets inserted at the end of list and then heapify is called from bottom to top to maintain correct order
    - Extract min/max
        - Required emement will present at root node i.e, 0th index. 
        - We then need to fill emply space at root node with the last element of list and then call heapify downwards to maintain heap property
    - Heapify
        - Upward: compare currentElement = i and parent element = (i-1)/2. Swap both if heap property is not maintained
        - Downward: 
            - Check root node(index), left node$(2*i+1), right node (2i+1)
            - Maintain heap property by swapping min/max element to parent node i.e, i..
    - Delete element at index
        - Find Maximum element from heap say its X
        - Create dummy value higher than X -> lets say X+1
        - assign value higher than max from heap i.e, heap[index] = heap[0] + 1
        - heapify till Root element
        - Noe X+1 node will reach at root node.
        - Delete root node and heapify again till leaf nodes 
- Types of heap
    - Min Heap: Element at top -> MIN among all elements
    - Max Heap: Element at top -> MAX among all elements

```java
class MaxHeap { // So, root node will have min element among all heap data
    int size; 
    int heap[];
    int lastIndex;

    public MaxHeap(int maxSize){
        this.size = maxSize;
        this.heap = new int[maxSize];
        this.lastIndex=-1;
    }

    void insert(int value){
        this.lastIndex++;
        heap[this.lastIndex] = value;

        int curr = this.lastIndex;
        // Maintain heap property from bottom to top
        while(curr > 0 && heap[parent(curr)] < heap[curr]){
            // Needs swapping with parent node
            int parentIndex= parent(curr);
            swap(curr, parentIndex);
            curr = parentIndex;
        }
    }

    void int removeMax(){
        int minElement = heap[0];
        heap[0]=heap[this.lastIndex];
        this.lastIndex--;
        
        heapify(0);
    }

    void heapify(int currIndex){
        if(currIndex >= lastIndex) return;

        // Heapify from top to bottom
        int leftIndex   = left(currIndex);
        int rightIndex  = right(currIndex);
        int maxIndex    = currIndex;

        if(leftIndex <= lastIndex && heap[leftIndex] > heap[currIndex] ) {
            maxIndex = leftIndex;
        }
        if(rightIndex <= lastIndex && heap[rightIndex] > heap[maxIndex] ) {
            maxIndex = rightIndex;
        }
        if(maxIndex != currIndex) {
            swap(maxIndex, currIndex);
            heapify(maxIndex);
        }
    }

    void int findMax(){
        return heap[0];
    }

    public int parent(int i){
        return (i-1)/2;
    }

    public int left(int i){
        return 2*i+1;
    }

    public int right(int i){
        return 2*i+2;
    }

    public void swap(int x, int y){
        int temp = heap[x];
        heap[x] = heap[y];
        heap[y] = temp;
    }
}

// Create heap from arbitary array
public void createMaxHeap(int[] a, int n) {
    for(int i=N/2-2;i>=0;i--){
        heapify(a,i);
    }
}

```

References: [Digital Ocean](https://www.digitalocean.com/community/tutorials/min-heap-binary-tree)

## Sorting Algorithms

### QuickSort algorithm

https://amortizedminds.wordpress.com/2015/07/21/quick-sort-algorithm/

### Quick Select - to Find kth highest value from Arr

- The idea is to use QuickSort technique with random pivot and only sort required partition.
- Complexity: $O(k)$

```java
class Solution {
    public int findKthLargest(int[] arr, int k) {
        int l = 0, r = arr.length - 1;
        k = arr.length - k;
        while (l < r) {
            int partitionIndex = partition(arr, l, r);
            if (partitionIndex == k)
                return arr[k];
            else if (partitionIndex < k)
                l = 1 + partitionIndex;
            else
                r = partitionIndex - 1;
            
        }

        return arr[k];
    }

    public int partition(int[] arr, int l,int r) {
        int size = r-l+1;
        int randIndex = new Random().nextInt(size) + l;
        swap(arr, randIndex, l);
        int L=l+1,R=r, pivot = arr[l];
        while(L<R){
            while(L<R && arr[L] <= pivot) L++;
            while(L<R && arr[R] > pivot) R--;
            swap(arr,L,R);
        }
        if(arr[L] > arr[l]) L--;
        swap(arr,L,l);
        return L;
    }

    public void swap(int a[], int l, int r){
        int temp = a[l];
        a[l]=a[r];
        a[r]=temp;
    }
}
```

### Merge Sort

https://amortizedminds.wordpress.com/2015/07/18/merge-sort-algorithm/
https://amortizedminds.wordpress.com/2015/07/19/counting-inversion-spoj-invcnt/

## nCr % m

https://amortizedminds.wordpress.com/2015/07/02/ncr-m/
# Java

Noting down important syntax in Java to solve DSA problems

## Arrays

### Initialize an array

```java
// This will initialize an array of size = len
int arr[] = new int[len];

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
```
## Matric

```java
int dp[][] = new int[m][n];
```

## Sort in Java

## Stack in Java

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

// Thread safe priority queue
Queue<Integer> threadSafeQueque= new PriorityBlockingQueue<Integer>();

```

## Heap in Java

## Trie in Java

## Graph

## Bitwise operators
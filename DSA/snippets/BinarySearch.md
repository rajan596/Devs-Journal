# Binary Search Code Snippets

## Search element in sorted array

```java
    public int search(int[] nums, int target) {
        int mid,L=0,H=nums.length-1;

        while(L<=H){
            mid = (L+H)/2;
            if(nums[mid] == target) return mid;
            else if(nums[mid] > target){
                H=mid-1;
            } else {
                L= mid+1;
            }
        }

        return -1;
    }
```

## Lower Bound/ Floor

Lower bound of x is an element k which is <= X

```java
    // Function to find floor of x
    // arr: input array
    // n is the size of array
    static int findFloor(long arr[], int n, long x) {
        int mid,low=0,high=n-1;
        int ans=-1;
        
        while(low<=high){
            mid = (low+high)/2;
            
            if(arr[mid] == x) return mid;
            else if(arr[mid] > x){
                high=mid-1;
            } else {
                low=mid+1;
                ans=mid; // can be possible ans
            }
        }
        
        return ans;
    }
```

## Upper Bound

Upper bound of x is an element k which is >= X

```java
    // Function to find floor of x
    // arr: input array
    // n is the size of array
    static int findCeil(long arr[], int n, long x) {
        int mid,low=0,high=n-1;
        int ans=-1;
        
        while(low<=high){
            mid = (low+high)/2;
            
            if(arr[mid] == x) return mid;
            else if(arr[mid] > x){
                high=mid-1;
                ans=mid; // can be possible ans
            } else {
                low=mid+1;
            }
        }
        
        return ans;
    }
```
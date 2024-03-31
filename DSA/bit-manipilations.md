

# Snippets

### Sum of 2 numbers without using + operator

**Concept**: 
 If x and y don’t have set bits at same position(s), then bitwise XOR (^) of x and y gives the sum of x and y. To incorporate common set bits also, bitwise AND (&) is used. Bitwise AND of x and y gives all carry bits. We calculate (x & y) << 1 and add it to x ^ y to get the required result. 
 Source: [GeeksforGeeks] (https://www.geeksforgeeks.org/add-two-numbers-without-using-arithmetic-operators/)

```java
class Solution {
    public int getSum(int a, int b) {
        while(b!=0){
            int sumWithoutConsidetingCarry = a^b;
            int carry = (a&b) << 1; // actual 1 needs to be added 1 left position where 1,1 were ancountered.
            
            a = sumWithoutConsidetingCarry;
            b = carry;
        }
        return a;
    }
}
```

### Subtract 2 numbers without - operator
https://www.geeksforgeeks.org/subtract-two-numbers-without-using-arithmetic-operators/

*Concept*: Try doing subtraction using all 4 possible bit positions and see the pattern

### Turn off the rightmost set bit
Concept:
Let the input number be n. n-1 would have all the bits flipped after the rightmost set bit (including the set bit). So, doing n&(n-1) would give us the required result. 
So, now let us see how n – 1 is flipping all the bits to the right (including the rightmost set bit also) of the n. 
Taking n = 12, so (n – 1) = 11

```java
requiredNumber = n & (n-1)
```
https://www.geeksforgeeks.org/turn-off-the-rightmost-set-bit/

###  Number of 1 Bits
Using above conncept of "Turn off the rightmost set bit" we can iteratively count till all rightmost bits are turned off.

```java
    public int hammingWeight(int n) {
        int ans=0;
        while(n>0){
            n=n&(n-1);
            ans++;
        }
        return ans;
    }
```

### Extract last bit (left most)

```java
int requiredNumber = x & -x;

if x = 1 0 1 1 0 then
requiredNumber = 1 0 0 0 0
```

### Find missing number in an array

Given an array nums containing n distinct numbers in the range [0, n], return the only number in the range that is missing from the array.

Problem: https://leetcode.com/problems/missing-number/description/
Video Explanation: https://www.youtube.com/watch?v=bYWLJb3vCWY&t=12s

Concept:
1. If we XOR number with itself it will become Zero.
2. So, if we do XOR of all the elements in an array along with XOR of all numbers from 1....N then all duplicate numbers will be eliminated. and missing number (having freq=1) will remain in our XOR result

```java
    public int missingNumber(int[] nums) {
        int allXor = 0;

        for(int i=0;i<nums.length;i++){
            allXor=allXor^nums[i]; // xor all array elements
            allXor=allXor^(i+1); // xor all numbers between 1....N
        }

        return allXor;
    }
```


# References
[LeetCode Bit Manipulations Wiki Guide](https://leetcode.com/problems/sum-of-two-integers/solutions/84278/a-summary-how-to-use-bit-manipulation-to-solve-problems-easily-and-efficiently/)

# Junk Data
Bitwise OR: A | B --> Union of A & B
Bitwise intersection:  A & B
Complement operator : ~A : Flips all bits. i.e, 0->1 and 1->0 
subtraction: A & ~B
Set negation ALL_BITS ^ A or ~A
Set bit A |= 1 << bit
Clear bit A &= ~(1 << bit)
Test bit (A & 1 << bit) != 0
Extract last bit A&-A or A&~(A-1) or x^(x&(x-1))
Remove last bit A&(A-1)
Get all 1-bits ~0
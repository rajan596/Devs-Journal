# Good Questions 

## Straight forward logic
- [Roman to Integer](https://leetcode.com/problems/roman-to-integer/description/)
    - Logical thinking. No algorithm

## Sorting
- [Rank Teams by Vote](https://leetcode.com/problems/rank-teams-by-votes/description/)|Emphasis on Sorting technique | Medium | Sorting

## Stack / Queue
- [Basic Calculator I,II,III](https://leetcode.com/problems/basic-calculator/description/)|Test cases | Medium | Stack |

## Binary Search
- [Meeting Schedule or My Calender](https://leetcode.com/problems/my-calendar-i)
    - This teaches a way to keep data in sorted order and do binary search when required using TreeMap
    - Editorial [Solution](https://leetcode.com/problems/my-calendar-i/editorial/) with TreeMap is worth exploring
    - [MyCalender II](https://leetcode.com/problems/my-calendar-ii/description/) | [Solution](https://algo.monster/liteproblems/731)
- [Median of two sorted arrays](https://leetcode.com/problems/median-of-two-sorted-arrays)
    - Arrays are sorted so Binary search comes to mind. But on what basis we need to do binary search ?
    - We need to pick some elements from arr1 and some from arr2 such that their element count is ~ n/2
    - If we pick x elements from arr1 then from second arr2 n/2-x elements can be picked. We need to find a point where we can form a valid first half based on partition point in arr1 and arr2
    - Solution: Follow this [Link](./assets/median-of-two-sorted-arr.png)
## Sliding Window
- https://leetcode.com/problems/count-subarrays-with-fixed-bounds/
    - Good use of slidinw window technique
    - Hard : Difficulty level 

## Greedy
- Minimise & Reach farthest: [Video Stitching](https://leetcode.com/problems/video-stitching/description/) | [Jump Game II](https://leetcode.com/problems/jump-game-ii/description/) | [Min no of tapes to water garden](https://leetcode.com/problems/minimum-number-of-taps-to-open-to-water-a-garden/description/)
    - Here we need to use greedy approach and pick suitable candidate which can take us to the farthest

## Trie
- [Word Search II](https://leetcode.com/problems/word-search-ii/description/)|Trie Usage|Hard|Trie

## Design Questions (Includes use of multiple Data structures as a whole)
- [All O`one Data Structure](https://leetcode.com/problems/all-oone-data-structure/description/)
---
layout:     post
title:      The Correct Way of Writing Binary Search
date:       2017-12-28
summary:    or how to code
categories: education informatics competitions
---

## Binary Search

If you as a software developer, a computer science student or an CS educator about binary search you'll most probably be given explanation like this:

Suppose we search for a number in a sorted array. So for example we search for the number 28 in 
```
8 14 18 19 21 22 23 32 33 34 35 40 46 47 57 59 61 64 70 71 74
```
The binary search algorithm would go as follows: 

1. Check the middle element of the array. 
  * If the element is 28 - great, we're done!
  * If the element is greater then 28 - discard all element to the left of the middle.
  * If the element is less then 28 - discard all elements to the right of the middle.
2. If there are elements left, repeat 1.

Explanations like this result into binary search implementations like the following one.
```cpp
int binarySearch(int arr[], int len, int x) {
  int l = 0, r = len - 1;
  
  while (l <= r) {
    int m = (l+r)/2;
    if (arr[m] == x) return m;  
    else if (arr[m] < x)  l = m + 1; 
    else r = m - 1; 
  }
  return -1; // element not present
}
```

Although this is the basic idea behind binary search, we still miss a lot of details:
* What happens if all elements are greater / smaller than the one we're looking for?
* What happens if the element is not in the array?
* What happens if the element occurs multiple times in the array? Which position we'll return?

Furthermore people tent to be confused

> How can we prove the algorithm works?



## References 

[GeeksforGeeks](https://www.geeksforgeeks.org/binary-search/)
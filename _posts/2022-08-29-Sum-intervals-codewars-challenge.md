---
title: Sum of intervals Codewars challenge
categories: [python, overlapping intervals]
tags: [python]
---

Sum of intervals is the Codewars <a href="https://www.codewars.com/kata/52b7ed099cdc285c300001cd/train/python" target="_blank">challenge</a> that is about counting overlapping intervals. Let’s examine the problem’s description and see if we can devise a valid solution to it.

![Figure 1: Figure 1: Screenshot of the Codewars challenge](https://sbozich.github.io/assets/29082201.jpeg) _Figure 1: Screenshot of the Codewars challenge_

<b> Description </b>

Write a function called sumIntervals/sum_intervals() that accepts an array of intervals, and returns the sum of all the interval lengths. Overlapping intervals should only be counted once.

<b> Intervals </b>

Intervals are represented by a pair of integers in the form of an array (in Python it will be a list of tuples). The first value of the interval will always be less than the second value. Interval example: [1, 5] is an interval from 1 to 5. The length of this interval is 4.

<b> Overlapping Intervals </b>

List containing overlapping intervals:

```
[[1,4], [7, 10], [3, 5]]
```

In Python: 

```python
[(1,4), (7, 10), (3, 5)]
```

The sum of the lengths of these intervals is 7. Since [1, 4] and [3, 5] overlap, we can treat the interval as [1, 5], which has a length of 4.

Examples:

```
sumIntervals( [[1,2],[6, 10],[11, 15]] ) => 9
sumIntervals( [[1,4],[7, 10],[3, 5]] ) => 7
sumIntervals( [[1,5],[10, 20],[1, 6],[16, 19],[5, 11]] ) => 19
sumIntervals( [[0, 20],[-100000000, 10],[30, 40]] ) => 100000030
```

<b>Tests with large intervals </b>

Your algorithm should be able to handle large intervals. All tested intervals are subsets of the range [-1000000000, 1000000000].

## Thinking about the problem

It seems that there are two ways to approach this problem. First one is iterating through the intervals, putting them into a suitable data structure, filtering out the duplicates (or processing it with some sort of unique-outputting function) and counting the occurrences. This is easier to write but will surely be slow to execute. Another way would be to consider given intervals’ limits, using them as a comparing points between intervals. This requires only iteration and comparison of the start and end points (instead of iterations of whole range of values), which will take a bit more code but will be much faster to run.

## Brute-force solution (slow performance)

```python
def sum_of_intervals(intervals):
 lista=[]
 for x in intervals:
   for y in range(x[0],x[1]):
     lista.append(y)
 return len(set(lista))
 ```

This iterates the input list two-folds; firstly it iterates the tuples in the list, after that it iterates the range within the tuples and puts them into a list. The code outputs the length of a set — list is converted to set to filter out the duplicate values. It is a fast solution to write but it will be slow with larger input values as the time to execute is O(n²). See here for explanation about a Big-Omega notation:

## Performance-efficient solution

In this case, enhancing performance means avoiding iteration through the whole range of intervals. Instead, using the intervals’ limit values and comparing them should solve the problem much more efficiently.

```python
def sum_of_intervals(intervals: list) -> int:
    sum=0
    intervals=sorted(intervals)
    a=intervals[0][0]
    b=intervals[0][1]
    sum += b - a
    if len(set(intervals)) == 1:
        return sum
    else:
        for j in intervals[1:]:
            if j[0] > b:
                sum += j[1] - j[0]
                a = j[0]
                b = j[1]
            elif j[0] >= a and j[1] <= b:
                continue
            else:
                sum += j[1] - b
                b=j[1]
    return sum
```

The key to devising this solution is detecting whether the input contains overlapping intervals, and if so, how to manage them. The list is sorted so that algorithm iterates through the intervals’ limits properly. After that, if the length of an input data with unique values (taking into account possible tests with repeating the same values) is one, a computation is easy and it returns the difference of the two values at the beginning and the end of interval. If that is not the case, we have three cases:

* if the second and other tuples’ first values are larger than second value of the first tuple (we split the interval using slicing): then simply subtract them and add the result to the sum variable,
* if the first value of second tuple onwards is larger than first value of first tuple and the second value of second tuple onwards is smaller than the first tuple’s second value, don’t do anything with this case as it needn’t be counted. For example, if the first tuple is (1,4) and another one is (3,2), it is enough to take into account only first one, as the second is irrelevant from the description of the problem,
* everything else represents overlapping intervals so the second value represents the boundary of the interval that needs to be compared and subtracted.

This solution is much faster as it doesn’t care whether the input data is in the range of tens, hundreds or millions as it only takes into account end values of intervals: the subtracting operation has the same speed if subtracting 1 or 1 million. On the other hand, since we iterate here only the numbers in the tuples, not their range, only possible limitation would be if we’d have lists of millions of tuples to check out, which is a non-realistic scenario anyways.
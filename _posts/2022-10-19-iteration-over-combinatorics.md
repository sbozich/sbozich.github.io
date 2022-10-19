---
title: Iterating over combinatorics
categories: [python, algorithms]
tags: [python, itertools, combinations]
---

Very often, there is a need for implementing combinatorics (permutations, combinations and variations) and iterating over the generated values in a list for example. While it is possible to create user-tailored solution to it, there is module in Python that provides solutions to those problems. 

The <i>itertools</i> module provides four methods for implementing combinatorics:
-	product()
-	itertools.permutations()
-	itertools.combinations()
-	itertools.combinations_with_replacement()

Examples:

```python
product('ABCD', repeat=2)
```

Output:
```
AA AB AC AD BA BB BC BD CA CB CC CD DA DB DC DD
```

```python
permutations('ABCD', 2)
```

Output:
```
AB AC AD BA BC BD CA CB CD DA DB DC
```

```python
combinations('ABCD', 2)
```

Output:
```
AB AC AD BC BD CD
```

```python
combinations_with_replacement('ABCD', 2)
```

Output:
```
AA AB AC AD BB BC BD CC CD DD
```

The user can choose one of those four methods depending on the type of combinatorics required. Basically, the <b>product()</b> method can be configured to output variations with repetition – basically all possible ways to combine a given set of data. The purpose of other three methods is self-explanatory. The first method <b>itertools.permutations() </b>takes a list or another collection and produces a sequence of tuples which generate all permutations’ possibilities. For example:

```python
collection=['a','b','c']
from itertools import permutations
for x in permutations(collection):
    print(x)
```

Output:

```
('a', 'b', 'c')
('a', 'c', 'b')
('b', 'a', 'c')
('b', 'c', 'a')
('c', 'a', 'b')
('c', 'b', 'a')
```

As demonstrated in the first example, it is possible to set a length of an output data:

```python
for x in permutations(collection,2):
    print(x)
```

Output:

```
('a', 'b')
('a', 'c')
('b', 'a')
('b', 'c')
('c', 'a')
('c', 'b')
```
Going further, <b>itertools.combinations()</b> produce a sequence of combinations from the input items. For example:

```python
# (using the code from the previous example)
for x in combinations(collection,3):
    print(x)
```
Output:

```
('a', 'b', 'c')
```
Those are combinations without repetition (i.e. repetitive characters are not produced as they are considered the same). 
Combinations with repetition can be generated as follows:

```python
from itertools import combinations_with_replacement
for x in combinations_with_replacement(collection,3):
    print(x)
```

Output:

```
('a', 'a', 'a')
('a', 'a', 'b')
('a', 'a', 'c')
('a', 'b', 'b')
('a', 'b', 'c')
('a', 'c', 'c')
('b', 'b', 'b')
('b', 'b', 'c')
('b', 'c', 'c')
('c', 'c', 'c')
```

The <i>itertools</i> module contains other interesting tools, like infinite iterators and terminating iterators. Certainly, when it comes to combinatorics and aforementioned other topics, it is the quickest way of implementing solutions to those problems.


---
title: List comprehensions in Python
categories: [python, list comprehension]
tags: [python, functional programming, iteration]
---

Iteration protocol in Python consists of multiple statements, of which a for loop is the usual way to iterate over corresponding data structure. For loop is not the only though. In this regard, Python borrowed from the other languages, particularly from the Haskel, providing something called comprehension iteration.

Suppose we have a following code:

```python
L=[2,4,6,8]

for x in range(len(L)):
    L[x]=L[x]**2

print(L)

# Outputs: 
[4, 16, 36, 64]
```

It certainly works as planned, but there is more concise and even faster way to do the same:

```python
L=[x**2 for x in L]

# Outputs the same
```

This one liner replaces the for loop completely; it partially resembles the syntax of the for loop, only written backwards, while bringing some novelties in the process. Let’s explore its details.  

## List comprehension basics

If we closely look at the prior statement:
```python
L=[x**2 for x in L]
```
it is clear that the statement inside constructs a new list, because it is written in square brackets. It begins with an arbitrary expression (x**2), followed by the 'for'; ending with the iterable object (L). When this statement is run, an interpreter iterates the elements in the right side, applying the code on the left side of for; end result being creation of a new list with all its elements multiplied by 2, for every x in L. 

List comprehensions are <b>optional</b>, they are not required by any feature of Python, as they can be fully represented by for loops and lists. However, they are less verbose and very common in code, therefore their understanding is an important part of comprehending Python. Moreover, when it comes to code performance, they may have a significant boost compared to executing for loop statements, because comprehension iterations are performed inside Python interpreter at C language speed.

List comprehensions apply an arbitrary expression to iterated items, rather than applying set of lined statements or a function. Therefore, similar to lambda, they can be put to places where multiple-lines statements wouldn’t be allowed. Comprehensions are a general term as they can be applied not only to lists, but to sets and dictionaries as well (albeit they are most widely used with lists). Their formal syntax is:

<i>
[expression for target1 in iterable1 if condition1 for target2 in iterable2 if condition2…for targetN in iterableN if conditionN…]
</i>

## Examples
It almost seems as they form a mini-language itself, especially when coupled with an if statement:

```python
res=[x for x in [2,3,4,5,6] if x%2==0]
print(res)

#Outputs : [2, 4, 6]
```

which is the same as:

```python
res=[]
for x in [2,3,4,5,6]:
    if x%2==0:
        res.append(x)
print(res)

# Outputs : [2, 4, 6]
```

Quasi-nesting is also supported:

```python
res=[x+y for x in [2,3,4,5,6] for y in [10,20,30]]

print(res)

# Outputs: [12, 22, 32, 13, 23, 33, 14, 24, 34, 15, 25, 35, 16, 26, 36]
```

which is the same as:

```python
res=[]
for x in [2,3,4,5,6]:
    for y in [10,20,30]:
        res.append(x+y)
```


The iterable part in comprehension can be any iterable type. For instance, it can iterate over strings as well:

```python
print([x for x in 'collection'])

# Outputs: ['c', 'o', 'l', 'l', 'e', 'c', 't', 'i', 'o', 'n']
```

Emulating concatenation:

```python
print([x+y for x in 'ab' for y in 'cd'])

# Outputs: ['ac', 'ad', 'bc', 'bd']
```

This code produces identical output (iterating over a list instead of string in the second part):

```python
print([x+y for x in 'ab' for y in ['c','d'] ] )
```

To give more complex example, let’s start backwards and first define the usual, for loop statement:

```python
res=[]
for i in range(10):
    if i%3==0:
        for j in range(4):
            if j%2==1:
                res.append((i,j))
print(res)
```

Output:

```
[(0, 1), (0, 3), (3, 1), (3, 3), (6, 1), (6, 3), (9, 1), (9, 3)]
```

Same code expressed as list comprehension with the same output:

```python
print([(i,j) for i in range(10) if i%3==0 for j in range(4) if j%2==1 ])
```
## Performance, readability and usability remarks
As we have seen, list comprehensions per se are not required syntactically, but their usage usually yields a performance advantage over the usual for loops. Due to the fact that they execute at the full C language speed (which is also true for the map iterations), they are in most cases significantly faster than for loop Python bytecode. 

Yet, there is certainly a tradeoff between list comprehensions’ conciseness, performance gain and lowered readability, because they bring less verbose but as well less understandable statements. So, when used, list comprehensions should be kept simple and neat; for more complex tasks, full statements are preferred. There is a reason why the for loop is the usual way of doing most tasks; one should be aware of other ways too which is the purpose of this article.
## Conclusion

List comprehension enables creation of effective and fast one-liners in Python which replace for loops. They are expressions, meaning they can be inserted in places where multiple lined and nested code couldn’t. Iteration through comprehension is widely used in practice, so if kept simple, it is a neat and elegant coding solution.
---
title: Garbage collection in Python 
categories: [python, garbage collection]
tags: [python, garbage collection, reference counting]
---

When it comes to memory management, the vital task of any developer is taking care of a memory requirements of given program. This assumes allocating and dislocating the memory occupied by the variables and their data. In the early days of programming, it was a manual task so developer solely was responsible for the memory management. It was laborious job, because every single variable needed to be traced from the declaration/assignation to the point where it may be no longer needed in the code. This meant tracing variables’ attributes like scope, life and their interaction within the code, which all needed to be taken into consideration when removing it from the memory. The bugs occurred often and were sometimes hard to trace. This still stands for major languages like C/C++, although there are some add-ins that offer user-configurable memory management techniques. 

Garbage collection stands for an automatized memory reclaiming process and it is a standard feature (rather than optional add-in) in the high-level languages developed in the last 30 years. This feature relieves the developer from the manual work and applies one or more algorithms, which run automatically, independently from the user. Garbage collector identifies previously allocated memory spaces (therefore called garbage) which are no more referenced by the program’s variables, and removes them, thus freeing memory.

## Python’s garbage collector specifications

Python documentation <a href="https://docs.python.org/3/glossary.html#term-garbage-collection" target="_blank">states</a> following:

<i>Garbage collection: The process of freeing memory when it is not used anymore. Python performs garbage collection via reference counting and a cyclic garbage collector that is able to detect and break reference cycles. The garbage collector can be controlled using the gc module.</i>

From the upper definition we can see that the garbage collection in Python is twofold: 
1.	Reference counting algorithm takes care of number of references that each object through its life gets. It is an integral part of the Python, it is automatic, constant process running in the background and it can’t be managed from the user side. 
2.	Cyclic garbage collector was added later up, when it was realized that the reference counting algorithm can’t detect some referencing cases such as reference cycles, where either object references itself or more objects are referencing each other. This garbage collector can be directly manipulated, it runs automatically periodically (but not constantly) in the background, and it can be manually switched off. 

## Reference counting
Python is structured in a such way that it does not “hold” any data in variables but rather use them just as names which reference to the actual objects that reserve or allocate actual memory space. Every object has a header field that records actual number of references to it. For the details about this model, see following texts:

<a href="https://sbozich.github.io/posts/dynamic-typing-pt1/" target="_blank">The joys of Dynamic Typing, Part One - Immutables</a>

<a href="https://sbozich.github.io/posts/dynamic-typing-pt2/" target="_blank">The joys of Dynamic Typing, Part Two - Mutables</a>


What reference counting algorithm does is deallocates objects which loose references to the variables (more precisely, when reference count equals zero).  

Consider following code:

### Example 1.

```python
x = {'price': 'GBP', 'quantity': 'pounds'}
y = {'price': 'GBP', 'quantity': 'pounds'}

print(x == y) # True
print(x is y)  # False

print(id(x)) # 2295298897536
print(id(y)) # 2295298897792
```

We created two dictionaries with the same values. Equality check expectedly returns True, because the values are the same. Identity check however prints out False, which tells us that the objects behind those two dictionaries are not the same, which is confirmed if we print out their memory addresses via id() function. Now if we display reference count of those dictionaries, we get following:
```python

import sys
print(sys.getrefcount(x)) # 2
print(sys.getrefcount(y)) # 2
```

Number of references is 2 for each object, respectively. One reference is created on assignation, another reference is created by getrefcount() function itself.

### Example 2.

```python
import sys

list=[]
def func(x):
    print(sys.getrefcount(x))

func(list) # returns 4

print(sys.getrefcount(list)) # returns 2
```

We created an empty list and defined a function which returns number of references for the passed argument. When we call the function and pass our empty list, the returned count of references is, maybe surprisingly, four:
-	one reference for assignation of list,
-	one reference for passing an argument,
-	one reference for using function itself,
-	one reference for getrefcount() function

On the other side, the reference count for the list itself is two:
-	one reference for assignment,
-	one reference for getrefcount() function.

### Example 3.

```python
import sys

x = []
list = []
print(sys.getrefcount(x)) # 2
list = [x]
print(sys.getrefcount(x)) # 3
```

By assigning an object to another object the reference count increases. To sum it up, there are four ways to increase the reference count:
-	assignment operator,
-	argument passing,
-	calling a function or class,
-	inserting an object to another object.

 The reference count decreases when applying opposite concepts:

-	reassigning the variable to another object,
-	deleting the variable,
-	exiting the function or class instance (Python destroys references with the variable scope local to that code block),
-	deleting an object within another object.

There are some things to be considered though. The variable’s scope is important. If the variable has the local scope (for example, lives only in the function or class), then on exiting from the code block, Python destroys variables with local scope and thus lowers the reference count of their objects in background. Objects are destroyed only when their reference count reaches zero, not on the decrementation of the reference count! The reference count decrementation of one object that is linked to other objects is mutually linked, so when complex object like list or dictionary (containing another objects) is deleted, the reference count for all containing objects is decremented as well. If, however, another object links one or more previously deleted objects, the interpreter will take that into account and update the current reference count of such objects.

Variables outside of function/class scope that reside in the program’s “root” are global variables. Their reference count is shielded from the garbage collector as they are stored inside a dictionary and live until the termination of Python’s interpreter. The user can display global variables’ dictionary by using function globals().

## Cyclic garbage collector

A reference counting algorithm has one major drawback: it can’t handle reference cycles. If one variable references itself, or two or more objects are referencing each other, such situation is called a cyclic or circular reference. The reference counts stemming from cyclic references can’t be detected using reference counting algorithm, hence, additional garbage collector’s algorithm is needed to detect those and to remove them from the memory space.

If we assign a variable to itself and delete it afterwards, we can’t access the reference count as the variable is deleted:

### Example 4.

```python
import sys
x = []
x.append(x)
print(sys.getrefcount(x)) # reference count=3
del x
```

Since variable x is deleted, we can’t access it using the getrefcount() function to determine what actually happened after deleting and what is the count of its references. Therefore, we must dig deeper and use code that can access memory address of the deleted object directly. In order to justify usage of another garbage collection algorithm, we need to prove that the reference count algorithm does not detect cyclic references.

Consider following situation depicted below.

### Example 5.

```python
import sys
import gc
import ctypes

class PyObject(ctypes.Structure):
    _fields_=[("refcount", ctypes.c_long)]

gc.disable() """ disable garbage collector to demonstrate that reference remains although we deleted variable"""

x = []
x.append(x) # x references itself
print(sys.getrefcount(x)) # reference count=3

x_address=id(x)

del x

print(PyObject.from_address(x_address).refcount) """ reference count=1"""

gc.collect()
print(PyObject.from_address(x_address).refcount) """ reference count=0 """
```


In this example we used <a href="https://docs.python.org/3/library/ctypes.html" target="_blank">ctypes</a>, a foreign function library which enables using C data types which we will need since we’ll going to access memory addresses directly. The default cyclic garbage collector was disabled, to prevent it from eventual cleaning of the reference counts. Then we assign a memory address of variable x to another variable. Afterwards, we delete the x and print out the reference count of the object that we accessed through its memory address. Since we get one reference returned following is obvious:

-	it is a proof that object still exists, regardless of the prior deletion of the variable x, 
-	it is a proof that reference count garbage collector hasn’t detected the cyclic reference.

When we manually start the cyclic garbage collector, we demonstrate that it works as it should because now the reference count that is returned equals zero.

As previously noted, the cyclic garbage collector runs automatically but not constantly opposed to the reference count collector, it activates itself occasionally. Another difference is that it is completely manageable and configurable, as it can be accessed within the code by using the gc module. 
Basically, what cyclic garbage collector does is detection of cyclic references and creation of three generations of variables which have different conditions (thresholds). We won’t go deeper in this matter here, rather we will provide some insights of how useful the understanding of the garbage collection could be.

## Usage tips

Broadly speaking, it is best leaving all as it is, because the garbage collectors certainly justified their existence through evolutionary, incremental advancement steps during the couple of decades. They apply advanced algorithms which, on average, do satisfying job within the specified parameters. Yet, on some specific cases it is possible to tune the Python’s cyclic garbage collector, or to disable it altogether. Sometimes is favorable to avoid cyclic references completely, by using <a href="https://docs.python.org/3/library/weakref.html" target="_blank">weak references</a> instead.  Another tip worth mentioning is to take a look on the global variables in the program’s code as they are untouched by the reference counting. As always, understanding the logic behind the garbage collector reveals a lot about Python’s internals and can help you to better understand the involved processes, ultimately envisioning better code for your needs.

## Conclusion
This article touches upon the memory management in Python; its automation and garbage collection process. We explained the reasons behind the both implementations of garbage collector in Python, together with valid examples. Additionally, we proved why there is a need for second garbage collector, and finally gave some tips worth considering when designing your code.

---
title: Functions and scopes in Python, Part 2
categories: [python, functional programming]
tags: [python, functions, scopes, def, return, nonlocal, global]
---


The usual way to get an output from function is a return statement. As we have seen in the <a href="https://sbozich.github.io/posts/Functions-scopes-Python-pt1/" target="_blank">part 1</a> of this article, once the interpreter reaches the return statement, it returns the value and ends the processing of the function’s body. At that point, all scope internal to the function is being destroyed and nothing after that, with an exception of return value, is being retained or remembered.
Yet, there is another way to code functions that would return values in a consecutive way: those are generator functions and are available in many programming languages, Python included. Such generator function’s distinction is an output type: instead of single value, they return a generator object which can be iterated. 
Let’s examine more closely how they function.

## Yield statement
While usual functions return value and are done with any computation or memorization afterwards, generator functions can remember their state (scope and variables’ values); thus, they can be stopped and later resumed. Therefore, when being suspended, they yield the value, temporarily stop the execution which can be resumed because the state is saved. Yield can be seen as a consecutive return because it returns the value in a similar manner, but it can be resumed. This allows the function to output a series of values throughout the function’s run-time, in a contrast to returning all the work immediately and exiting like return functions do. There are particular usage-cases for generator functions which we’ll later more closely examine.

We mentioned that generator functions output generators which can be iterated. It turns out that they share common traits with the iteration protocol. In Python, iterator objects implement a __next__ method which iterates one step forward until the end of iteration range is reached, raising an StopIteration exception to mark the end of iteration. All iteration methods use this iteration protocol which is also used by generator functions, making them in a sense a variation of iterators. The generator object which is the output of generator function supports the iteration protocol and aforementioned __next__ method, which is being run until either the next yield value or StopIteration exception. 
To illustrate this, consider following example:

```python
def gen_func(a):
    print("First iteration")
    yield a

    a+=1
    print("Second iteration")
    yield a

    a+=1
    print("Last iteration")
    yield a

b=gen_func(3)
next(b)
next(b)
next(b)
```

Output:

```
First iteration
Second iteration
Last iteration
```

As we can see, by using yield returns, the local scope is remembered, so when the next method is used on a generator function object, it actually iterates one step further, as if it would in any iterative process (in order to output yield value to the console print method should be used). When next reaches end of iterative range, it finishes and it can’t be called again using the same function’s call. 

If we try to print out the object:

```python
print(b)
```

The output we’d get is:

```python
<generator object gen_func at 0x000002595D926420>
```


To get and display the yielded values from the object we need to iterate them and use print:

```python
for i in gen_func(3):
    print(i)
```

Output:

```
First iteration
3
Second iteration
4
Last iteration
5
```

We can put them in a list as well:

```python
print(list(gen_func(3)))
Output:
First iteration
Second iteration
Last iteration
[3, 4, 5]
```
We could get the same results as in the previous example by using a standard function with returned list of generated values. However, when it comes to processing larger data sets, they may have an advantage in terms of memory use and performance since they don’t execute in the moment and completely but rather in consecutive steps. By doing so they actually distribute the resources (computational power which equals time as well as the memory space) over a number of iterations. 

Consider this example:

```python
import timeit

def sq(n):  
    lista=[]
    for i in range(n):
        lista.append(i**2)
    return lista

def gen_sq(n):
    for i in range(n):
        yield i**2

normft=timeit.timeit('sq(1000000)', globals=globals(), number=5)
genf=timeit.timeit('gen_sq(1000000)', globals=globals(), number=5)

print('Exec time of a normal function: ', normft)
print('Exec time of a generator function: ', genf)
print("Exec time of a generator function is faster on average ", normft//genf, " times")
```

Output:

```
Exec time of a normal function:  1.7032738999696448
Exec time of a generator function:  4.499917849898338e-06
Exec time of a generator function is faster on average 378512.0 times
```

Functions sq(n) and gen_sq(n) do the same thing, which is computing a range of numbers’ squares. The first function makes all the computation and saves the results in the list while another creates generator which does the same computing but without using memory, hence the enormous execution time difference. Therefore, generators really shine when processing large data input. 

Another possible usage is infinite streams. As generator functions don’t create any data structure it is possible to generate infinite iteration stream within a function. In the following example we demonstrate the way to generate infinite stream outside and inside the function, using generators. When executed, both codes will loop infinitively, but calling the function gives more flexibility, for instance using it with if branching, or inside another function or class, etc.

```python
while True:
    a=0
    a+=1
    print(a)

def inc_nums():
    n = 0
    while True:
        yield n
        n += 1
```

Another example of using generator functions would be reading a very large textual file (.txt, .csv, .json and similar formats where data is separated via a newline) to extract a number of rows. We could use a normal function to do so:

```python
def text_reader(file_name):
    file = open(file_name)
    result = file.read().split("\n")
    return result

row_count = 0
text_gen = text_reader("some_file.txt")

for row in text_gen:
    row_count += 1

print("Row count is ", row_count)
```

This will function fine as long as the input data’s size is modest at most because the program processes whole input file at once. If we convert it to generator function, it will be able to process input files regardless of the file size, as the generator would process the data in a sequence manner sending back only specific value:

```python
def text_reader(file_name):
    file = open(file_name)
    for row in file:
        yield row

row_count = 0
text_gen = text_reader("some_file.txt")

for row in text_gen:
    row_count += 1

print("Row count is", row_count)
```

## Lambda

Many popular programming languages, Python included, provide another way to create functions. That is lambda expression. It is used to create small, inline functions which are unnamed (although a name could be assigned but it’s not mandatory) and as such, executed without a call. Lambda is a single-liner, opposed to a block of statements when using def. Lambda is not a statement but an expression and as such is possible to appear inside places where def couldn’t (as an argument in function call, for instance).

This code returns the same values:

```python
a=lambda x,y: x*y
print(a(5, 10))

def mt(x,y):
    return x*y

print(mt(5,10))
```

Output:
```
50
```

General form of lambda is:

```
lambda [arg1, arg2,… argN]: expression 
```

Names assigned inside lambda retain their scope, as seen from the following example:

```python
def names():
    first_name='John'
    full_name=lambda x: first_name+' ' + x
    return full_name

first_client=names()
name_surname=first_client('Smith')
print(first_client)
print(name_surname)
```

Output:

```
<function names.<locals>.<lambda> at 0x0000023F61CCA4D0>
John Smith
```

As a shortened version of def, lambda expression is mostly used where statement can't be inserted but expression can, as in jump tables:

```python
# this code uses lambda as a list literal, where def can't go
lista=[lambda x:  x**2, lambda x: x**3, lambda x: x**4] 

for a in lista:
    print(a(3))

# code below outputs the same yet uses def
# def square(x): return x**2
# def cube(x): return x**3
# def quad(x): return x**4

# lista2=[square,cube,quad]

# for a in lista2:
#     print(a(3))
```

Output:

```
9
27
81
```

In Python, lambda is often used in connection to higher-order functions; more precisely lambda is used as an argument along with functions like filter() and map().

<b>Using filter():</b>
```python
lista=[-10, -9, -8, -7, -6, 6,7,8,9,10]
lista2=list(filter(lambda x: (x<0), lista))

print(lista2)
```

Output:

```
[-10, -9, -8, -7, -6]
```
<b>Using map(): </b>
```python
lista=[-10, -9, -8, -7, -6, 6,7,8,9,10]
lista2=list(map(lambda x: (x**2), lista))

print(lista2)
```

Output:

```
[100, 81, 64, 49, 36, 36, 49, 64, 81, 100]
```

## Conclusion
In this article series we’ve touched upon the basic premises of functional programming in general, as well as some of the basic functional elements in Python. Generator and anonymous functions are more advanced concepts with which a programmer should be at least familiar, because they can be used either to shorten, or in some cases to optimize the code and as such, it is useful to get acknowledged about their existence, structure and common usage cases. 

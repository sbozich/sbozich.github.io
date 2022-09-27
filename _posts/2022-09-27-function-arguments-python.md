---
title: Functions' arguments in Python
categories: [python, functional programming]
tags: [python, functions, arguments, parameters]
---


Arguments, as pieces of information passed to a function provide a way to insert values into function’s mechanism. In Python, function arguments directly depend on object reference model, as there is a stark difference in behavior of immutable and mutable arguments. At this point, a linguistical remark is needed to differentiate between argument and parameter: although those two terms are usually used interchangeably, strictly speaking, when declaring function we use parameters, while upon a function’s call we pass arguments. 

Python uses number of matching rules when differentiating passed arguments. Those rules are, as follows:

-	positional arguments are matched from left to right. This is the most widely used case, for example:
    ```python
    def func(a,b,c):
        print((a+c)/b)

    func(3,9,12) # 1.6666666666666667
    func(3,12,9) # 1.0
    ```
-	keyword arguments are matched by given name, instead of position. For instance:
    ```python
    def func(b=20,a=5,c=22):
        print(a,b,c)
        
    func() # 5 20 22
    func(3,12,9) # 12 3 9
    ```
-	arguments with defaults specify default values if the function’s call doesn’t contain all specified arguments (parameters)
    ```python
    def func(a, b=10, c=20):
        print(a,b,c)

    func(12) # 12 10 20
    func(12, 15, 22) # 12 15 22
    ```
-	star expressions (varargs): arguments preceeded with * or ** characters to collect an arbitrary number of arguments
    ```python
    def func(*args):
        print(args)

    func(10) # 10
    func(1,2,3,4) # 1,2,3,4
    ```
    ** feature works for keywords and collect them into a new dictionary:
    ```python
    def func(**args):
        print(args)

    func(x=5, y=10) # {'x': 5, 'y': 10}
-	varargs unpacking. Passing arbitrarily number of positional or keyword arguments

    ```python
    def func(a,b,c,d):
        print(a,b,c,d)

    args =(0,1)
    args+=(3,5)

    func(*args) # 0 1 3 5
    ```
    Using the same function but providing a dictionary instead and **:
    ```python
    def func(a,b,c,d):
        print(a,b,c,d)

    args={'a':0, 'b':5, 'c':10, 'd':15}

    func(**args) # 0 1 3 5
    ```
-	keyword only: arguments that must be passed by name
    ```python
        def func(a, *b, c):
            print(a, b, c)

    func(1,2,3) """ TypeError: func() missing 1 required keyword-only argument: 'c'"""
    func(1,2,c=3) #1 (2,) 3
    ```

Now let’s explain difference between immutable and mutable arguments. Consider the following code:
```python
def func(a):
    a=10

b=20
func(b)
print(b) # 20
```
Here the variable <i>a</i> is assigned the object with the value od 20 on the function’s call. Although inside a function’s definition the object <i>a</i> changes to 10, changes don’t have effect on function’s call as they only reset the local variable to another object.  In contrast with C/C++, Python lacks aliasing; meaning that assignment to a parameter name defined in function (a=10) does not change variable <i>b</i> upon the function’s call. Since arguments are, in fact, pointers to the objects, they can’t share same objects permanently – when it comes to immutables (see <a href="https://sbozich.github.io/posts/dynamic-typing-pt1/" target="_blank">this article</a>). 

However, when mutable objects (lists, dictionaries, class objects) are passed as arguments, in-place changes will probably occur, impacting the callers as well. For instance:
```python
def func(x,y):
    x=5
    y[0]="test"

X=1
C=[2,4]
func(X,C)
print(X,C) # 1 ['test', 4]
```
In the upper code, the <i>func</i> function assigns values to a immutable and mutable variables. Since <i>x</i> is a local variable in the function’s code scope, x=5 has no effect upon function’s call as previously explained – all it does is changes the local variable a to another’s object reference. Y is a local variable but since the list is the mutable object (<a href="https://sbozich.github.io/posts/dynamic-typing-pt2/" target="_blank">see explanation</a>),  statement y[0]="test" is the in-place change which impacts the value of the corresponding list’s element. This is the reason why this change outlives the function’s call and impacts the list <i>C</i>. If those in-place changes are not wanted, we could copy the list so the original one isn’t affected:
```python
def func(x,y):
    x=5
    y=y[:]
    y[0]="test"
```
Summarizing Python’s argument matching rules, here is the complete list of possible cases:

* <b>def func(name):</b> matches arguments by position or name
* <b>def func(name=value)</b>: defines default argument value (if not passed in function’s call)
* <b>def func(*name)</b>: matches and collects remaining, possibly multiple, positional arguments in a tuple
* <b>def func(**name)</b>: matches and collects remaining, possibly multiple, keyword arguments in a dictionary
* <b>def func(*other, name)</b>: defines arguments that must be passed in calls by keywords
* <b>func(value)</b>: matching by position in function’s call
* <b>func(name=value)</b>: matching by name in function’s call
* <b>func(*iterable)</b>: pass all objects in iterable as individual positional arguments
* <b>func(**dict)</b>: pass all key/value pairs in dict as individual keyword arguments


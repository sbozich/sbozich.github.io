---
title: Functions and scopes in Python, Part 1
categories: [python, functional programming]
tags: [python, functions, scopes, def, return, nonlocal, global]
---

Functions are integral part of most programming languages. Together with classes in OOP-supported languages, they make the code more readable, concise, usable, maintainable thus greatly enhancing the program design. Programming without functions today would be impossible: developers would have to copy and paste code, to repeat it everywhere, creating so-called spaghetti code which would be unreadable and impossible to maintain. In fact, in Python, understanding how functions work is probably more important than classes comprehension, or at least it is a prerequisite to understanding OOP. 

## Basic theory behind functions
A function is way to group statements into one coherent notation defined by its name, which can then be used and run multiple times later. It can actually do more than being run; it could return a value based on a processing of given inputs in some particular way. Furthermore, functions can be nested and called within themselves, which we’ll explore a bit later. 

Benefits of grouping the code into one statement are:
-	Increase of readability and conciseness, as the code is written once and may be easily implemented (called upon) multiple times, 
-	Enhanced usability and maintainability, as changing the function in the one place produces changes everywhere else where is called,
-	Minimizing redundancy thus shortening the code,
-	Splitting complex code into manageable, well-defined parts leads to a better program design, achieving procedural decomposition.

## Diving into code
In Python, functions are created using def statement:

```python
def function(par1, par2,… parN):
	some code
	return value # optional
```

The def statement creates a function with dedicated, user-chosen name with one or more parameters that are optional. Function can have a return value, if it computes/produces something when being run. When the interpreter reaches a return statement inside a function, it ends the function’s call. If there is no return value, the interpreter supposes None and returns it. Other than return, the function can have yield statement, which will be discussed in pt. 2 of this article. 
When function is being created, it is not being run (called): function assignment does not imply its execution, which is a separate statement. On the function’s call, arguments are being passed to it (note that the parameter and argument are the same thing, with a commonly accepted linguistic distinction - parameter is what is defined on function’s declaration, while argument is being passed to the function when it is run):

```python
function(a,b):
	return a+b
```

In Python, the def can be seen as assignment (=) as it assigns a name to the code block within. So, it can be used everywhere where it is possible to assign a variable (more precisely a name, because Python uses names referenced to underlying objects). Therefore, functions can be assigned within the particular module, or inside an if statement (but not inside an iteration statement: for that purposes a lambda can be utilized which will be explained in the <a href="https://sbozich.github.io/posts/functions-scopes-python-pt1/" target="_blank">part 2</a> of the article as well). 

A function can be assigned to a separate variable, for instance:

```python
def printing(x):
	print(x)

prnt=printing(“Hello World”)
```

In this case function is being called through the assigned name.
Consider this function and its calls:

```python
function(a,b):
	return a*b

print(function(3, 4)) # outputs 12 (integer)
print(function(3, 4.3)) # outputs 12.89999 (float)
print(function(3,’a’)) # outputs ‘aaa’ (string)
```

Since Python is dynamically typed, it does not care about the types but about interfaces. In the example above, multiplication means either mathematical multiplication, or repeating. The obvious question arises here: how Python knows which operation to apply? The answer is wide-spread polymorphism, which is everywhere in Python. The underlying object’s interfaces decide how to react on a given operation: if the object has the expected interface (or protocol), it will run. That is the reason why the multiplication runs without problems with various data types (integers, floats, strings), as they internally can manage it. On the other hand, if they don’t support it, the exception will be raised:

```python
def function(x,y):
    return x+y
print(function(3,'a'))
```

Output:

```
TypeError: unsupported operand type(s) for +: 'int' and 'str'
```

## Scopes
Let’s consider a simple example:

```python
a=22
def number():
    a=23
    print(a)

number(a)
```

Output: 

```
23
```

Another one:

```python
a=22
def number():
a=23
print(a)
number()
print(a)
```

Output:

```
23
22
```

In order to understand the output results of the previous examples, the concept of scope needs to be discussed. It is obvious that the declared (or in Python assigned) variables (names) have their ‘space’ of validity, that is, there exists an internal mechanism which tells the interpreter when to consider which variable. A certain hierarchy can be seen from the provided examples: it seems that the variables have their defined places of validity. That is scope: it is a system which prevents variables with same names interfering with one another. Scope is directly linked with the functions, as we may have the same-named variable in the root of our program (module) and in the function. Furthermore, as previously mentioned, we can nest multiple functions.

Python approaches this problem by defining a namespace – literally a place in memory where names reside. The determining factor of how Python treat a name is the place of its assignation in program’s code. According to the places of assignation, variables in Python adhere a hierarchical order according to which they can be local, nonlocal (or enclosing), global and built-in. 

![Figure 1: Scope hierarchy in Python](https://sbozich.github.io/assets/26082201.jpeg) _Figure 1: Scope hierarchy in Python_

This scheme gives the answer to the question on how Python knows what variable to use:

1.	If the variable (or rather name) is used (but not declared/assigned) within the function, Python will first seek the local scope, then local scope(s) of any enclosing function (nested functions), then global scope, and finally, built-in scope. The interpreter stops at the first place the name is found (if the name isn’t found at all, it returns an error back to the user). 
2.	If the name is assigned within a function, Python creates the name in the local scope of that function, with exception if the name is declared global or nonlocal. Additionally, every class creates local scope so the names living there are considered local to that class.
3.	If the name is assigned outside of function, it is considered as a global (valid within the file/module), essentially local scope becomes global one. 
4.	If the name does not exist in the first three categories, Python finally searches built-in names module (builtins). In the current version, there are 156 names inside it and they refer to some errors/exceptions as well as variable/data structures assignment names.

## Local and global scope
From the previous discussion it is clear that the local scope is already defined as name space inside either a function or a class. It is separated from other scopes and as such it is a narrow, limited one. Global scope is however native to the module, that is, to the root of the file itself:

```python
a=22
def number():
    a=23
    return a
    
print(number())
print(a)
```

Output: 

```
23
22
```

Here we assigned two names, one is global to the file (a=22), other is local to the function (a=23). When the function is called, it considers the first name which is found in the hierarchy, which here is a local one, hence output 23. Name a within the module itself remains unchanged so it outputs 22 as a second value.
Now let’s introduce the global statement and slightly alter the previous code:

```python
a=22
def number():
    global a
    a=23
    return a
    
print(number())
print(a)
```

Output:

```
23
23
```

The global statement overrides the name defined inside the module: here we implicitly tell Python to consider only name which is declared global, regardless of the fact that it has been already declared within the function. Names declared global are global within the module/file, which means their value is the same everywhere – bringing advantages but some disadvantages as well. Consider another variant of this code:

```python
def number():
    global a
    a=23
    return a
    
print(number())
print(a)
```

Now we assigned name only once – but it is clear that we can access the name inside or outside the function, that is, anywhere in the module itself. Clear advantage is that we can use global statement when we want local scope (function) to be preserved and used throughout the module. Disadvantages may include unpredicted behavior and hard-to-trace errors, as global statement could be used multiple times:

```python
def number():
    global a
    a=23
    return a
    
print(number())

global a
a=22
print(a)
```

Output:

```
23
22
```

Therefore, there is a reason names inside functions have local scope, and names within module have global scope. Additionally, there is a good reason why global statement is implicit, as it produces the global consequences users should be aware of. One last remark about the global statement is that global name always is global inside one module: they can’t be global in more modules at the same time – ‘truly’ global variables in the C/C++ sense do not exist in Python.

## Enclosing (nested) scope and nonlocal
It is possible to define functions within functions, in that case multiple local scopes are being created:

```python
a=22
def n1():
    a=23
    def n2():
        a=24
        print(a)
    n2()

n1()
```

Output:

```
24
```

In the example above, there are three variables with the same name, all within separate, different respective scopes. More precisely, we have one global scope (where a=22) and two enclosing ones (a=23 and 24, respectively). The output is 24 because second local scope is taken into account only, as the interpreter finds the name in that space first. The nonlocal statement allows scope to become global, but only one step in the upper hierarchy:

```python
a=22
def n1():
    a=23
    def n2():
        nonlocal a
        a=24
    n2()
    return a

print(n1())
```

Output:

```
24
```

Without nonlocal statement, this code would output 23, as return considers n1 function space. With nonlocal, it raises the scope one level up. So, it rather acts as a limited global statement, when we want to access/manipulate the value of the name declared within one function, calling it from a function that is one level higher.

## Summing it all up
Taking aforementioned discussion into consideration, we will portray an example of functional design taking into account multiple scopes. Consider a task of finding multiple prime numbers between the given numbers’ interval. The task consists of choosing the start number, an end number and the advancement step, printing out the Boolean values and counting the number of primes found. For example, in the number range of 9 and 20 every number needs to be checked against a prime test. The algorithms here are not important (other than they function properly) nor the output formatting; it is the program design using functions that we want to depict here:

```python
import math

def is_prime(num):
    if num>1:
        for i in range(2,int(math.sqrt(num))+1):
            if(num%i)==0:
                return False
        else: return True
    else: return False

def choose_nos(start,end,step):
    global lista
    lista=[]
    for i in range(start, end, step):
        lista.append(i)

def print_primes(lista):
    global lista2
    lista2=[]
    for i in lista:
        print(is_prime(i))
        lista2.append(is_prime(i))

def count(lista):
    global countTrue
    countTrue=0
    for i in lista2:
        if i==True:
            countTrue+=1
    return countTrue

def run(start, end, step):
    choose_nos(start, end, step)
    print_primes(lista)
    print(count(lista))


run(9,20,1)
```

Output:

```
False
False
True
False
True
False
False
False
True
False
True
4
```

Without using functions, the code would be very messy, redundant, probably at least twice longer and pretty much hard to read. Instead, the code was grouped into 5 functions; every function relates to the segment of program that is dealing with particular sub-problem. The last function’s purpose calls previously assigned functions so the user must only write one line of code to execute it, passing the parameters and getting the result. Notice further how we used global statement on two occasions, enabling the name inside function to be visible to the rest of the code. If we were to change objectives of the code (what the code should output), it would be enough to change it on one place within the particular function and to use it as much times as needed, as the function calls needn’t be changed. Once set-up, we could import this module into another one and reuse the defined functions outside of the module where they’ve been assigned, which would bring us to the field of modular programming.

## Conclusion
This rather lengthy article outlines basics of functional programming in Python; discusses the elementary syntax and touches upon the rudimentary issues of program design. As successful implementation of functions implies understanding of scopes, the discourse had to be broadened with the explanations of name spaces, scopes and their hierarchy in Python. Rounding up the theory with appropriate examples of program code summarizes this article: its second <a href="https://sbozich.github.io/posts/functions-scopes-python-pt1/" target="_blank">part</a> will review the yield and lambda statements.
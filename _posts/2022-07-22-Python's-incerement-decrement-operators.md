---
title: Python’s (non-existing) increment/decrement operators
categories: [python, coding]
tags: [python]
---
# Python’s (non-existing) increment/decrement operators

Have you tried using very basic ++/-- operators in Python? Incrementing/decrementing of numeric variables should be routine, right? Actually, it turns out they do not exist, and that they are not necessary. In C(ish) languages, unary operators are commonly used in flow control as iterators, providing the ability of loop’s progression within specified limits (pre- and postfix difference will be discarded here). So simple, basic for loop in C# looks something like this:

(general format)
```
for (initialization; condition; iteration)
some code
```

(actual example)
```c#
int a=5;
for (int i=10; i>=a;i--)
{
    Console.WriteLine(i);
}
```
Output:
```
10
9
8
7
6
5
```

Of course, while loop is the same as for loop, just slightly reconstructed:

```c#
int a=5;
int i=10;

while (i>=a)
{
    Console.WriteLine(i);
    i--;
}
```

Output:

same as previous example.

So unary operator ‘--’ decrements the integer variable by one. Attempting to use same in Python gives an error because unary operators don’t exist in Python which may be surprising to some. Sure, instead of i-- in C# we could use i=i-1 or its somewhat shorter version i-=1, no problems whatsoever. Same is possible in Python as well. But, let’s go a bit deeper to see what is really going behind the scenes in Python when it comes to variables changing their values, and why the nonexistence of unary operators isn’t a big deal.

Statement a+=1 is in Python known as the Augmented Assignment Operator. It (re)assigns, rather than only increments. How is that? If we create an integer, increment it and then check the hex ids, we will realize that new object is created in memory and that the integer’s reference is changed:

```python
a=1
print(id(a)) # 2636855378160
print(hex(id(a))) # 0x265f0d400f0
a+=1
print(id(a)) # 2636855378192
print(hex(id(a))) # 0x265f0d40110
```

Notice how the both id (unique integer) and its hex address changed after incrementation. That is consequence of the fact that integers are immutable in Python – in other words, when we change the integer’s value, new object is created in the background and the reference now points to that object, while old one is or will be at some point garbage-collected. Both in C# and Java, int datatype is also immutable albeit the internal structure of those languages is a bit different. However, in Python the value is being reassigned to a modified, newly created object in memory. 

The for loop in Python somewhat differs from other languages as it does not require an iterator value because there is an option to use range() function:
```
for (initialization) in range(condition):
  some code
```

If we want to output printed integers from 10-5, as in our C# example, we would simply write:
```python
for a in range (10, 4, -1): # loop from 10 to 5 (not including 4) with -1 incrementation
  print(a)
```
Output:
```
10
9
8
7
6
5
```
Equivalent while loop in Python is, given the differences in the syntax, essentially same as in C#:
```python
i=10
while(i>4):
    print(i)
    i-=1 # 
```

## Conclusion
Due to the way of Python’s internal structure, unary operators ++/-- used as incrementors/decrementors in other languages are not existent. Instead, either we use Augmented Assigned Operators += and -=, while in some cases we can achieve the same using for loop with range() function. 

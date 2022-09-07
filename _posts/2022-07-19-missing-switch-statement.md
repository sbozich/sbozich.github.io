---
title: Missing ‘switch’ Statement? Python 3.10 gets you covered
categories: [python, coding]
tags: [python, branching]
---

Most languages support multiple flow control statements, vast majority of them is familiar with if and switch. So, someone coming from C/C++/Java/C# and alike languages might wonder about equivalent statement in Python. First of all, let’s clarify the subtle difference between those statements in order to see what exactly are we looking for.

Consider a simple structure in Python:
``` python
userInput=1
if (userInput==1):
    print("Addition")
elif (userInput==2):
   print("Subtraction")
elif (userInput==3):
    print("Multiplication")
elif(userInput==4):
    print("Division")
```
Depending of the value of the variable userInput, we want to achieve different outcomes. Sure, multiple if/elif/else statements are doing the job, but if we’d had quite a bit more of conditions, the code would look somewhat cumbersome. Instead, one might feel a need to use a switch statement, especially if coming from C(ish) language(s). Implementing switch in C# would look something like:

```python
userInput=1;
switch (userInput)
{
    case 1:
        Console.WriteLine("Discounted Price 1=40");
        break;
    case 2:
        Console.WriteLine("Discounted Price 2=35");
        break;
    default:
        Console.WriteLine("Standard price: 45")
        break;
}
```
Now, in Python community, the possibility of switch statement implementation was discussed at least in the last 15 years (see this: https://peps.python.org/pep-3103/), until finally, Python 3.10 released in October 2021. implemented something called Structural Pattern Matching (see what’s new in Python 3.10 here: https://docs.python.org/3/whatsnew/3.10.html) which introduced Match Case statement, so we can write now:

```python
match userInput:
    case 1:
        print(a+b)
    case 2:
        print(a-b)
    case 3:
        print(a*b)
    case 4:
        print(a/b)
```
Notice how the Match Case does not require a break statement – it is implicitly, internally called when the interpreter reaches the first matched case – other ones are being ignored. 

If our code has a default case option – that is the option that executes if previous cases weren’t matched – we’d expect to use a default keyword for that, however Python neatly achieving the same using underscore char as a wildcard pattern "_" which here detects any input and proceeds as a default case.  

```python
    case _:
        print("This is the default case")
```
We can use logical OR in the same line like this:

```python
    case 5 | 6 | 7 | 8 | 9:
        print("Invalid input. Please enter the correct input value.")
```
Data types to match could be usual ones like integers and strings, but also floats (!), so this is possible:

```python
test=1.36
match test:
    case 1.00:
        print("Stock conversion")
    case 1.36:
        print("Conversion kw to hp enabled")
```
Besides primitives, objects could be matched as well. Consider this code:
```python
class Car:
    def __init__(self, engineType):
        self.engineType=engineType
    def selector(self):
        match self.engineType:
            case 'petrol':
                print("Petrol engine")
            case 'diesel':
                print("Diesel engine")
            case 'EV' | 'electro':
                print("Electro engine")

car1=Car('petrol')
car1.selector()
```
We created a simple class with instance variable (object property) engineType, then we defined a method selector which uses matching of that instance variable. Lastly, we create an object car1 with an argument and call a method which executes its logic, finds matched case and outputs the respective string. This line: 
```python
match self.engineType
```
actually checks instance variable of the object of type class Car, in our example it checks an instance variable engineType of the object car1.

Further possibility is to match multiple patterns, ie. primitives/object (arguments). This code declares method musicPlayer, which is called upon with three arguments, both of which is matched because of the asterisk "*" sign:

```python
def musicPlayer(param):
    match param:
        case [*genres]:
            for genre in genres:
                print(f'We are playing {genre}.')

musicPlayer(('rockabilly','country', 'swing'))
```
## Conclusion

Switch argument was something that some Python developers sought for.  Although some disputed the very need for it, as other viable solutions do the job, Python version 3.10 introduced pattern matching, which enables switch case and much more.  In this article, we have shown basic usage patterns including simple primitives' case switching, as well as more advanced and to some developers’ unusual features like usage of wildcards, pattern recognition and object pattern matching. Therefore, case matching is only one subset of the newly introduced features - prior to 2021. it didn’t exist in Python ecosystem. It is certainly more powerful than switch statements that we commonly use in other main languages. 
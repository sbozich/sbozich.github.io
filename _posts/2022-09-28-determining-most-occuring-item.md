---
title: Determining the most frequently occuring item in a sequence
categories: [python, algorithms]
tags: [python, functions, arguments, parameters, counter]
---

A sequence is a positionally ordered collection of items. It consists of elements which  can be refered to using the index number. In the most programming languages, Python included, the elements' numbers are in the range 0-(n-1), meaning if the sequence has 10 elements (or its length is 10), starting index is 0 and the last is 9.

In Python, most common sequence types are:

* <b>lists</b> (mutable),
* <b>strings</b> (immutable),
* <b>typles</b> (immutable).

Now, very common problem is, given the sequence of items, to determine the most frequently occuring items in it.

```python
song=["Been", "spending", "most", "our", "lives", "Living", "in", "a", "gangsta's", "paradise", 
"Been", "spending", "most", "our", "lives", "Living", "in", "a", "gangsta's", "paradise", 
"Keep", "spending", "most", "our", "lives", "Living", "in", "a", "gangsta's", "paradise", 
"Keep", "spending", "most", "our", "lives", "Living", "in", "a", "gangsta's", "paradise",
"Tell", "me", "why", "are", "we", "so", "blind", "to", "see",
"That", "the", "ones", "we", "hurt", "are", "you", "and", "me",
"Tell", "me", "why", "are", "we", "so", "blind", "to", "see",
"That", "the", "ones", "we", "hurt", "are", "you", "and", "me"
]

from collections import Counter
word_counts=Counter(song)
top5=word_counts.most_common(5)
print(top5)
```
Output:
```
# [('spending', 4), ('most', 4), ('our', 4), ('lives', 4), ('Living', 4)]
```
Python has a solution in a form of embedded collections.Counter class which does exactly what we are looking for. The method most_common() returns the most frequent occurences. It can count any sequence of hashable input items, since a Counter class is a dictionary itself that maps the items to the number of occurences.

```python
print(word_counts['spending']) # outputs 4
```
For additional set of data:

```python
additional=["I", "guess", "they", "can't",
"I", "guess", "they", "won't", "I", "guess", "they", "front", "guess", "guess"]

for word in additional:
    word_counts[word]+=1

top5=word_counts.most_common(5)
print(top5)
```
Output:
```
[('guess', 5), ('spending', 4), ('most', 4), ('our', 4), ('lives', 4)]
```
There is an update() method which does the same thing:
```python
word_counts.update(additional)
```
We can use addition and subtraction operations:

```python
a=Counter(song)
b=Counter(additional)

print(a)
```

Output:

```
Counter({'spending': 4, 'most': 4, 'our': 4, 'lives': 4, 'Living': 4, 'in': 4, 'a': 4, "gangsta's": 4, 'paradise': 4, 
'me': 4, 'are': 4, 'we': 4, 'Been': 2, 'Keep': 2, 'Tell': 2, 'why': 2, 'so': 2, 'blind': 2, 'to': 2, 'see': 2, 'That': 2, 'the': 2, 'ones': 2, 'hurt': 2, 'you': 2, 'and': 2})
```

```python
print(b)
```

Output:
```
Counter({'guess': 5, 'I': 3, 'they': 3, "can't": 1, "won't": 1, 'front': 1})
```

```python
c=a+b
print(c)
```

Output:
```
Counter({'guess': 5, 'spending': 4, 'most': 4, 'our': 4, 'lives': 4, 'Living': 4, 'in': 4, 'a': 4, "gangsta's": 4, 'paradise': 4, 'me': 4, 'are': 4, 'we': 4, 'I': 3, 'they': 3, 'Been': 2, 'Keep': 2, 'Tell': 2, 'why': 2, 'so': 2, 'blind': 2, 'to': 2, 'see': 2, 'That': 2, 'the': 2, 'ones': 2, 'hurt': 2, 'you': 2, 'and': 2, "can't": 1, "won't": 1, 'front': 1})
```
```python
d=a-b
print(d)
```

Output:
```
Counter({'spending': 4, 'most': 4, 'our': 4, 'lives': 4, 'Living': 4, 'in': 4, 'a': 4, "gangsta's": 4, 'paradise': 4, 'me': 4, 'are': 4, 'we': 4, 'Been': 2, 'Keep': 2, 'Tell': 2, 'why': 2, 'so': 2, 'blind': 2, 'to': 2, 'see': 2, 'That': 2, 'the': 2, 'ones': 2, 'hurt': 2, 'you': 2, 'and': 2})
```

## Conclusion
Counter class is very useful tool when it comes to counting the data in sequences. It is an implemented solution which should be preferred to user-written, manual solutions involving dictionaries.
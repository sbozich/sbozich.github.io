---
title: Text clean-up and string sanitization with Python
categories: [python, clean-up, string sanitization]
tags: [python, text parsing]
---

Text sanitizing means cleaning-up the entered text according to the previously established parameters or rules. Those rules usually involve text parsing and handling. 

A whole set of tools could be used to clear-up such an input. For example, basic tools like string replacement method - <b>str.replace()</b> or regex – <b>re.sub()</b> can achieve removing or changing a specific character sequences. 
Additionally, there is Unicode normalization. Consider following strings:

```python
s1='Menue\u00f1o' # Menueño
s2='Menuen\u0303o' # Menueño
```

They represent the same text, and are represented by multiple characters therefore the strings itself are different:

```python
print(s1==s2) 
```
Output:

```
False
```

Obviously comparing those strings will be a problem so this needs to be fixed somehow. When considering Unicode, Python sports the <i>unicodedata</i> module with method <b>normalize()</b> which can do the trick:

```python
fix_s1=unicodedata.normalize('NFC', s1)
fix_s2=unicodedata.normalize('NFC', s2)

print (fix_s1==fix_s2) # true
```

NFC argument means fully composition of the characters, so if we apply it to the both strings, they will be comparable. There is another argument which depicts decomposition NFD, which fully decomposes the use of the underlying characters:

```python
fix_s1=unicodedata.normalize('NFD', s1)
fix_s2=unicodedata.normalize('NFD', s2)

print (fix_s1==fix_s2) # true
```

To see the difference between NFC and NFD, let’s ascii print the first string after applying NFC and NFD parameters, respectively:

```python
print(ascii(fix_s1)) # NFC applied outputs 'Menue\xf1o'
print(ascii(fix_s1)) # NFD applied outputs 'Menuen\u0303o'
```

Furthermore, when trying to clean-up the text, the various <b>strip()</b> methods could be used for processing. They can be used to get rid of whitespace and characters on the either left or right side of the string, but not in the middle:

```python
s='    python is open source  '
s=s.strip()
print(s)
```

Output:

```
python is open source
```
Here the <b>s.strip()</b> method trims leading and trailing whitespace, but it can’t remove the inside ones. To do that, we could use the <b>replace()</b> method, as follows:

```python
s=s.replace(' ','')
```

Which outputs:

```
pythonisopensource
```

Going further, there are other, more advanced options to the text clean-up. Suppose a chaotic string like this: 

&#34;ṗŕögrặmṃìng\fis\t great\r&#34;.

There is a way to process whole ranges of characters or strip diacritical marks by using <b>str.translate()</b> method. Let’s first fix the whitespace:

```python
s='ṗŕögrặmṃìng\fis\t great\r'

process={
    ord('\t'): None, # deleted
    ord('\f'): ' ', # replaced with an empty space
    ord('\r'): None # deleted

}

s=s.translate(process)

print(s) 
```

Output:
```
ṗŕögrặmṃìng is great
```

Basically, it uses a translation table which is then applied to the given string. One can build a larger table and include all the fixes inside. Adding this code to the previous block:

```python
import unicodedata
import sys
combine=dict.fromkeys(c for c in range(sys.maxunicode) if unicodedata.combining(chr(c)))
b=unicodedata.normalize('NFD', s)
b=b.translate(combine)

print(b) 
```

Output:
```
programming is great
```

In this example, a dictionary mapping every Unicode character (through comprehension iteration) is created from the <b>dict.fromkeys()</b>. The original input is then normalized into decomposed form using <b>normalize()</b> with a NFD parameter. Afterwards, <b>translate()</b> method is used to delete all the accented characters.

Ending note is that the larger data set, the more possible performance issues the complex code may have. Therefore, performance-wise, it is sometimes better to repeat simpler code than to forge out an one-solution-fits-all. In any case, a testing will reveal what is the most acceptable solution in that regard.

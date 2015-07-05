---
layout: post
title: Understanding regular expression parsing
---

I was reading a wonderfull article on [python regular expression](http://pymotw.com/2/re/). And I was playing with some example code.

```python
import re
text = "aabbbaababaab";
pattern = "abb"
re.findall(pattern, text)
````

then I got stuck with following code. I couldn't understand why the output is the way it is.

```python
s = "Python syntax highlighting"
print s
```

So I tried to come up with simple logic to understand how regular expression parsing takes place to produce the output. And here it is!

-----

### How regular expression parses the pattern(General Logic)

text - Actual text to search for(input)

pattern - What to search for

(say, in a give log file(text) find all ip address(pattern))

#### Steps
1. Start from the beginning of the 'text'
2. Start looking for 'pattern' in 'text'
3. If fails at initially, move to next char in 'text' and proceed from step 2
4. If passes, then parse character by character until fails. print the passed characters. Move to next char and proceed from step 2
5. If reached end of 'text', print the passed characters and stop
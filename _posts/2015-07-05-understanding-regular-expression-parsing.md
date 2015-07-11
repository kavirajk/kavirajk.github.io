---
layout: post
title: Understanding regular expression parsing
comments: True
twitter: True
tags: [python,regular-expression]
---

I was reading a wonderfull article on [python regular expression](http://pymotw.com/2/re/). And I was playing with some example code.

{% highlight python %}
import re

text = "aabbbaaababaab"
pattern = "abb"

re.findall(pattern, text)
# output: ['abb']
# pattern 'abb' found at index 1:3
{% endhighlight %}

then I got stuck with following code. I couldn't understand why the output is the way it is.

{% highlight python %}
import re

text = "aabbbaaababaab"
pattern = "ab+" # a followed by one or more b

re.findall(pattern, text)
# output: ['abbb', 'ab', 'ab', 'ab']
# wait.. Where is "abb"?
{% endhighlight %}

The pattern ```ab+``` means __'a' followed by one or more 'b'__. So "abb" should also be present in output. But it was not!!

So I tried to come up with simple logic to understand how regular expression parsing takes place to produce the output. And here it is!

-----

### How regular expression parses the pattern

text - Actual text to search for(input)

pattern - What to search for

(say, in a give log file(text) find all ip address(pattern))

#### Steps
1. Start from the beginning of the 'text'
2. Start looking for 'pattern' in 'text'
3. If fails at initially, move to next char in 'text' and proceed from step 2
4. If passes, then parse character by character until fails. print the passed characters. Move to next char and proceed from step 2
5. If reached end of 'text', print the passed characters and stop

#### Good news!

Now I know why the output doesn't contain "abb".

Its because of step 4 of parsing. When the parsing "abb" is done, its still __'passing'__ the pattern rules so it continous to "abbb". And now next character is 'a' and pattern ```ab+``` fails. So parsing stops and prints "abbb" then continue its parsing from next character 'a'
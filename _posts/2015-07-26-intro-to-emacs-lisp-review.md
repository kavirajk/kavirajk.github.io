---
layout: post
title: "Review: Emacs Lisp Intro Manual"
summary: Learning Emacs Lisp(a.k.a elsip) helps in understanding how Emacs works, customizing Emacs and writing extention packages for Emacs. Emacs Lisp Intro is excellent manual for getting started on Emacs Lisp
comments: True
twitter: True
tags: [emacs, elisp]
---

Once you start using emacs for most of your editing/coding tasks, then that will make you serious about using it productively

From then on, you can no longer use Emacs without customizing it. Yea, of course there are tons of elisp code snippet out there to customize it just by copy pasting without understanding what it does or how it works.

But that no longer works on the longer run. At some point of time you have get started on learning Emacs lisp.

Same thing happened to me few weeks back. I got tired of customizing emacs just by including some piece of elisp code from somewhere on the internet into my ```.emacs``` file without even understanding what the code does.

So at the end,

* My .emacs file got messy without organizing.
* Its very frustating to debug if something goes wrong.
* I started to get afraid of parenthesis 😧
     
Just then, I figured it out I just need to get started on learning Emacs lisp.

<!--break-->

## Why need to Learn Emacs Lisp, anyway?
* To understand how emacs works
* To customize emacs
* To write extension packages for emacs

## Emacs Lisp Intro Manual

Many articles in various blogs already recommending [Introduction to programming in Emacs Lisp](https://www.gnu.org/software/emacs/manual/eintr.html) manual to get started on emacs lisp. So I just did give a try

This manual comes in-built within emacs as well(Im using GNU Emacs 24.5).  To read inside emacs type ```C-h i m Emacs Lisp Intro```

Here is a topics covered in manual

Topic                     | Summary
--------------------------|-----------------------------
List Processing           |         What is Lisp?
Practicing Evaluation     |     Running several programs.
Writing Defuns            |      How to write function definitions.
Buffer Walk Through       |     Exploring a few buffer-related functions.
More Complex              |      A few, even more complex functions.
Narrowing & Widening      |      Restricting your and Emacs attention to a region
car cdr & cons            |             Fundamental functions in Lisp.
Cutting & Storing Text    |     Removing text and saving it.
List Implementation       |     How lists are implemented in the computer.
Yanking                   |    Pasting stored text.
Loops & Recursion         |   How to repeat a process.
Regexp Search             |     Regular expression searches.
Counting Words            |     A review of repetition and regexps.
Words in a defun          |     Counting words in a ‘defun’.
Readying a Graph          |           A prototype graph printing function.
Emacs Initialization      |       How to write a ‘.emacs’ file.
Debugging                 |      How to run the Emacs Lisp debuggers.


One more Good thing is it comes with exercises so that you can practice what you have learnt at the end of the chapter.


Most of the exercises are just straightforword and easy. Some exercies on Regular expressions, Recursion and Drawing Graph were bit difficult for me.

### My Favourite(and Interesting) topics

* **Buffer related functions:** Understanding how some of the basic buffer related functions works```(mark-whole-buffer)```, ```(append-to-buffer)```, ```(copy-to-buffer)``` and ```(insert-buffer)```
* **Regular expressions:** Understanding how regular expressions are used in finding end-of-sentence, end-of-paragraph, end-of-definition in functions like ```(forward-paragraph)``` and ```(forward-sentence)```
* **Graph project:** This is one of the coolest chapter. Counting and displaying the number of words in ```defun``` of many elisp files as a graph
* **Basic customizing of .emacs file:** Basic customisation including how to add 'hook', setting up 'file-local-variables', setting up key-bindings, X11 color customization etc.

### Other things you will learn:

* **How to get help within emacs easily:** 
  If you find any new function that you want to explore and know more about just type ```C-h f``` and 'function name'. You will find function's documentation along with its source code link 😃
* **You will get chance to look into some of the real elisp
code** that is part of emacs and you will understand how that works
* **No more afraid of parenthesis**.😎
* **That awesome feeling** when you understand some elisp code that you copy pasted before.💪🏽

### Things I learned accidentally while reading

* ```C-M-h``` - to select a whole function definition(any language mode)
* ```C-M-a``` and ```C-M-e``` - to goto beginning/end of definition
* ```M-x add-file-local-variable-prop-line``` - to adding any 'file-local-variable'. Say, setting 'coding system' at top of the file like
```-*- coding: utf-8 -*-```.

### Exercises and Solutions:

You can find solutions to all the exercises in the manual on my [github repo](https://github.com/kavirajk/Solutions-Emacs-Lisp-Intro). Any improvement or suggestions are welcome. If you are viewing the source file inside emacs, then you can execute any elisp expression just by typing ```C-x C-e``` at the end of the expression.

On the whole, I enjoyed reading the manual. Hope anyone who wants to start customizing the Emacs would enjoy as much as I did.

[Introduction to programming in Emacs Lisp](https://www.gnu.org/software/emacs/manual/eintr.html) covers basics of emacs lisp very well so that you can do basic emacs customizations and extensions. That being said there are many more important topics(like Macros) not touched by this manual.

The comprehensive guide for emacs lisp would be another manual called [The GNU Emacs Lisp Reference Manual](https://www.gnu.org/software/emacs/manual/html_node/elisp/index.html)

>“You don’t have to like Emacs to like it”—this seemingly paradoxical
>statement is the secret of GNU Emacs.  The plain, ‘out of the box’ Emacs
>is a generic tool.  Most people who use it, customize it to suit
>themselves.

>-- <cite>Emacs Lisp Intro Manual</cite>

You enjoyed reading any manuals or books on emacs or linux? I am glad to know in comments
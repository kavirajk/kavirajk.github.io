---
layout: post
title: Split window with different buffer in Emacs
summary: 'Everytime you split a window(both vertical and horizontal) in Emacs,
by default the same buffer appears in newly created window. We can customize
it. '
twitter: True
comments: True
tags: [emacs, elisp]
---

It's common to split windows while working in emacs. By default, whenever we
split windows in emacs the newly created window will also have the same
buffer(same buffer in two windows). So it is always needed
to change to another buffer by ``C-x b`` after splitting it.

We can customize it by little elisp code as below.

<!--break-->

put the below code in either ```~/.emacs``` file or ```init.el``` file in ```~/.emacs.d``` directory.

{% highlight elisp %}
(eval-when-compile (require 'cl))

(defun split-window-func-with-other-buffer (split-function)
  (lexical-let ((s-f split-function))
    (lambda ()
      (interactive)
      (funcall s-f)
      (set-window-buffer (next-window) (other-buffer)))))

{% endhighlight %}

All this code does is pretty straight forward. ```lexical-let``` needs common lisp libraries.
Thats what ```(eval-when-compile (require 'cl))``` does.

```'split-function'``` is an argument which will be default elisp split function.
In ```(funcall s-f)``` we call the default split function then using```(set-window-buffer)```elisp function we change the buffer of newly
created window ```(next-window)``` with different buffer ```(other-buffer)```.

Also rebind the default split keys ```C-x2```(horizontal) and ```C-x3```(vertical) to our
new custom split function.

{% highlight elisp %}
(global-set-key "\C-x2" (split-window-func-with-other-buffer 'split-window-vertically))
(global-set-key "\C-x3" (split-window-func-with-other-buffer 'split-window-horizontally))
{% endhighlight %}

here ```'split-window-vertically'``` and ```'split-window-horizontally'``` are default
elisp split functions which we pass as argument to our new custom split
function ```'split-window-func-with-other-buffer'```.

I got this code from
[here](https://github.com/purcell/emacs.d/blob/master/lisp/init-windows.el#L15-L26)
(I am not elisp master. Well! atleast not
yet 😜). Thanks to [Purcell](https://github.com/purcell). In fact his
[.emacs.d](https://github.com/purcell/emacs.d) is well organised and very good
resource to learn how to customize emacs and he is also helpful via mail 😊.

I also found [this](http://bzg.fr/learn-emacs-lisp-in-15-minutes.html) tutorial is very helpful in getting started on Emacs lisp.

Happy hacking emacs!! 👍✌.

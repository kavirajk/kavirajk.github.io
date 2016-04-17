---
layout: post
title: "Django: Internals series - Part 1"
summary: "Understanding how core components of django works under the hood"
comments: True
twitter: True
tags: [django, python]
---
Hi Everyone. Welcome to Django internal series. Goal of this entire series is to share my experience on understanding the
internals of django.

In part 1 of this series we try to understand why its important to understand
django internals.

I started using django about an year ago. It's a part of our [company's
technology stack](https://medium.com/@launchyard/our-typical-technology-stack-b52371035e4b). We love building apps with Django(of course who wouldn't?). During early
stage of using django, I really suck at debugging as I felt django is
doing lot of magic under the hood. Few basic questions started bothering me.

1. I am declaring all fields in model as **class variables**. How come I am able to
access those as **instance variables** on any of those model instances?
2. What is happening when I access any field of a model instance. For e.g take
a look at ```simple_django_model.py``` snippet below. There we have single
field called ```email``` of type ```models.EmailField()```. Now if try to get
```user.email``` it should technically return ```models.EmailField``` instance right?, but django
does some magic and return the exact email address as string ```kavirajkanagaraj@gmail.com```
3. How does "class Meta" inside any model affects the model behaviour?
4. What does really 'app' mean to django?(HINT: Every directory with
   ```__init__.py``` is not an app)
5. How django creates actual model instance from db tables?

And much more basic questions..

<!--break-->

These questions made me to dig into [source code](https://github.com/django/django) of django. While doing that
[Pro Django by Marty Alchin](http://www.amazon.com/Pro-Django-Experts-Voice-Development/dp/1430258098) helped a lot.

>Note: This Pro Django book is kind of outdated. Its written for django
>1.5 . During that time, AppCache was used instead of App Register which is one of
>the [great improvement](https://docs.djangoproject.com/en/1.9/ref/applications/#application-registry)
>introduced in django 1.7. App Register is something which deals with how django
>loads and register every app. We will cover App Register in depth in the
>upcoming series.

But most of the fundamental design and architecture of django remains same as explained in this book. Its
really a great book to read if you are interested in understanding django internals.


## Why need to understand django internals anyway?
  * Makes debugging pleasant
  * To understand some of the advanced python concepts and strategies. So that
    you can apply it in your own projects
  * Can help you in becoming better django developer
  * Can help in contributing to django open source
  * And of course its fun to know how something works under the hood !!!

## Some insights
In rest of this post, I will try to address top 2 questions metioned before.

>Note: All code examples are tested using python3.5 and django1.9

Consider the following code snippets.
First one is a simple django model with single email field. In second snippet I
tried to do the same with raw python ```object``` just to understand the
importants of ```models.Model```

{% gist da51e3762bdaeaf8413e %}

### Question 1
I am declaring all fields in model as **class variables**. How come I am able to
access those as **instance variables** on any of those model instances?

### Analysis
In both the snippets, email attribute is a class variable. But how come I couldn't
access email as class attribute in django model?. The answer is actually the
way django create ```Model``` Class using metaclass called ```ModelBase```. In
```ModelBase, __new__``` method is overriden to alter the behaviour of the
creation of ```Model``` class. In this step all the class attributes are converted to instance attribues (source code link [here](https://github.com/django/django/blob/master/django/db/models/base.py#L157-L158)). Thats the reason we could access
all attributes as instance attributes and not as class attributes in any django model.


### Question 2
What is happening when I access any field of a model instance. For e.g take
a look at ```simple_django_model.py``` snippet. There we have single
field called ```email``` of type ```models.EmailField()```. Now if try to get
```user.email``` it should technically return ```models.EmailField``` instance right?, but django
does some magic and return the exact email address as string ```kavirajkanagaraj@gmail.com```

### Analysis
In both the snippets, we tried to access the email attribute from instance of
User model. Technically ```email``` attribute is of type ```models.EmailField```. So whenever I
tried to access ```email``` field, It should give me ```EmailField``` instance like
in second snippet. But django is smart and gives us back exact email addres as
string itself. To understand how it works, consider below snippet(slightly
modified version of second snippet)

{% gist ee51a1ef11ff4f6c6ba2 %}

Now we are able to get the exact email address instead of EmailField
instance just like in ```User``` Model. This feature in python is known as getters and setters(using
```__set__``` you can set the email address directly). Django uses this feature
behind the scene everytime you set or get any model attributes.

We will of course discuss about "Model creation" and "getters, setters" in much
more depth in next part of series which completely deals with internals of only
Django model

Please let me know your thoughts in comments.

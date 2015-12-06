---
layout: post
title: "Django: Internals series - Part 1"
summary: "Understanding how core components of django works under the hood"
comments: True
twitter: True
tags: [django, python]
---
Hi Everyone. Welcome to Django internal series. Main goal of this entire series is to share my experience on understanding the
internals of django.

I started using django about an year ago. It's a part of our [company's
technology stack](https://medium.com/@launchyard/our-typical-technology-stack-b52371035e4b). We love building apps with Django(of course who wouldn't?). During early
stage of using django, I really suck at debugging as I felt like django is
doing lot of magic under the hood. Few basic questions started bothering me.

1. I am declaring all fields in model as class variables. How come I am able to
access it as instance variable on any of those model instances?
2. What is happening when I access any field of a model instance. For e.g lets
say we have a field ```email = models.EmailField()``` in model called
```User```. Assume we have ```user_obj = User(email='kavirajkanagaraj@gmail.com')``` . Now when I try to access
```user_obj.email``` it should return an instance of ```models.EmailField()``` right? But django
does some magic and return the exact email address as string (say kavirajkanagaraj@gmail.com)
3. How does "class Meta" inside any model affects the model behaviour?
4. What does 'app' means to django?(HINT: Every directory with
   ```__init__.py``` is not an app)
5. How django creates actual model instance from db tables?

And much more basic questions..

These questions made me to dig into source code of django. While doing that the book
ProDjango helped me a lot.

Note: This book is kind of outdated. Its written for django
1.x . During that time AppCache was used instead of AppRegister which is one of
the great improvement introduced in django1.5. App Register is something very important which deals with how django
loads and register every app. We will cover about App Register in depth in
upcoming series.

But most of the fundamental design remains same as explained in this book. Its
really a great book to read if you are interested in understanding django.


## Why need to understand django internals anyway?
  * Makes debugging pleasant
  * To understand some of the advanced python concepts and strategies. So that
    you can apply it in your own projects
  * Can help you in becoming better django developer
  * Can help in contributing to django open source
  * And of course its fun to know how something works under the hood !!!

## Some insights
In rest of this post. I will try to address top 2 of the questions metioned before.

Note: All code examples are tested using python3.5 and django1.9

Consider the following code snippets.
First one is a simple django model with single email field. In second snippet I
tried to do the same with raw python ```object``` just to understand the
importants of ```models.Model```

{% gist da51e3762bdaeaf8413e %}

### Question 1
I am declaring all fields in model as class variable. How come I am able to
access it as instance varialbe on any of those model instances?

### Analysis
In both the snippets, email attribute is a class variable. But how come I could
access email as class attribute in django model?. The answer is actually the
way django create Model Class. In model.Model ```__new__``` method is overriden
to alter the behaviour of the creation of class. In this step all the class
attributes are changed to instance attribues. Thats the reason we could access
all attributes as instance attributes and not as class attributes in any django model.


### Question 2
What is happening when I access any field of a model instance. Say a model
has a field ```email = models.EmailField()```. When I try to access
```model_instance.email``` it should return an instance of EmailField() right? But django
does some magic and return the exact email address as string (say
kavirajkanagaraj@gmail.com)

### Analysis
In both the snippets, I tried to access the email attribute from instance of
AModel. Technically ```email``` attribute is of type EmailField. So whenever I
tried to access ```email``` field, It should give me EmailField instance like
in second snippet. But django is smart and gives us back exact email addres as
string itself. To understand how it works, consider below snippet(slightly
modified version of second snippet)

{% gist ee51a1ef11ff4f6c6ba2 %}

Now we are able to get the exact email address instead of EmailField
instance. This feature in python is known as getter and setter(using
```__set__``` you can set the email address directly). Django uses this feature
behind the scene everytime you set or get any model attributes.

We will ofcourse discuss about "Model creation" and "getters, setters" in much
more depth in next part of series which completely deals with internals of
Django model

Please let me know your thoughts in comments.

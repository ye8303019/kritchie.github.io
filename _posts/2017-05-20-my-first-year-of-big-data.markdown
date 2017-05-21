---
layout: post
title:  "My Python workflow"
date:   2017-05-20 14:27:00 +0800
categories: jekyll update
---

Start of 2016, I moved to China to pursue a new position at a start-up company. That’s where I started to play with something called “Big Data”. I was traditionally more oriented on embedded systems and robotics, so my experience in Data Engineering was close to 0. The learning curve was steep. 

In this article I'll highlight 3 quick tips I have for people who are starting to program in Python. I believe this will help you define a workflow that works for you that will help you and make it more enjoyable to program in Python.

Something that I found pretty quickly while starting to explore Big Data, is that the Python programming languages is widely adopted and very popular. I had to learn how to program Python. I used to be somewhat versed in C/C++ and JAVA, but unfortunately not in Python. The first thing I did was to learn about different versions of Python. Then I managed to learn how to properly create and deploy Python programs.  

<br/>

---
**Disclaimer:** This article is more oriented towards people starting to use Python as a programming language and want to have some quick workflow tips.

---


<br/>

# Virtual environment is your friend

I learned pretty quickly about using virtual environments while programming Python. This as helped me in so much ways and saved a lot of time and headache. You should use virtual environments if you are programming in Python. 

So what is a virtual environment ? Well, you can think of it as a virtual machine for Python. The goal of a virtual environment is to decouple the system's Python with your projet's Python. This way, you don't clobber your system's Python with the dependencies of every single projects you are working on. Each project will have it's own separate location to store the dependencies. 

It's also very easy to use different versions of Python depending on your project's requirements, because every time you create a project, you also create a virtual environment specific to it. 

### TODO INSERT DIAGRAM HERE

Python virtual environments come in many flavors, again it's a question of which one works the best for you. Personally I really love [virtualenv](https://virtualenv.pypa.io/en/stable/) with [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/) but there's also other virtual environments tools like [PyEnv](https://github.com/pyenv/pyenv) and [Conda](https://conda.io/docs/index.html) that can even support other languages than Python.

<br/>

# Project scaffolding tool, a must

Something that helped me a lot to be up to speed with Python development was to define a workflow. This meant a way to prepare the projects and a way to properly test and deploy programs in Python. You should look for a project scaffolding tool in order to automate project creation. The ones that come to my mind are : 

* [PyScaffold](https://readthedocs.org/projects/pyscaffold/)
* [CookieCutter](https://cookiecutter.readthedocs.io/en/latest/)

Those are two very popular frameworks for creating projects. You should try them and see whatever one fits more you needs and workflow. I personally like PyScaffold because of it's simplicity. 

To give you an example, everytime I want to create a new project I follow this procedure using PyScaffold :

{% highlight bash %}
# Navigate to your workspace folder, mine is in my home folder
cd ~/workspace

# Create your virtual environment
# I use Python 3.6 because it's latest Python version as of today
mkvirtualenv name-of-project -p /usr/bin/python3.6 

# Install PyScaffold
pip install pyscaffold

# Create the project
putup project-name

# Fill in the python requirements
pip freeze > requirements.txt
{% endhighlight %}

This will create a directory structure looking like this : 

{% highlight text %}
AUTHORS.rst
CHANGES.rst
.coveragerc
docs/
.gitignore
LICENSE.txt
project_name/
README.rst
requirements.txt
setup.cfg
setup.py
test-requirements.txt
tests/
{% endhighlight %}

Most of the files are self-explanatory, but you should go read the [PyScaffold packaging documentation page](https://pyscaffold.readthedocs.io/en/latest/features.html#packaging) to get a deeper understanding of what each files does. 

<br/>

# Quick access code snippets 

I found out that defining some easy to access templates was one of the best thing I did to increase my productivity. What I did was to create an HTML where I stored those templates as plain text. The goal was to make it accessible through my Web browser, because let's face it, my Web browser is pretty much always open. Here's an example of what I've put in this template pages :

{% highlight python %}
# -*- coding: utf-8 -*-
from __future__ import (absolute_import, division, print_function, unicode_literals)

# Insert custom imports here 

import logging
# Insert remaining lib imports here

__author__ = 'Karl Ritchie <ritchie.karl@gmail.com>' 
__license__ = 'MIT'


logger_name = 'LOGGER_NAME_HERE' # FIXME : Change with correct name 
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(logger_name)

# # create a file handler
# handler = logging.FileHandler(logger_name + '.log')
# handler.setLevel(logging.INFO)
#
# # create a logging format
# formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')
# handler.setFormatter(formatter)
#
# # add the handlers to the logger
# logger.addHandler(handler)
{% endhighlight %}

This is the boiler plate that I use for most of my Python files. Having quick access to this is really helpful and it helps keep every files somewhat standardized. So basically I just translated the Python code to HTML, saved that to a file on my computer and added a bookmark to it in my browser. 

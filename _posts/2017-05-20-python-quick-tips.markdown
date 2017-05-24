---
layout: post
title:  "Starting with Python: Quick workflow tips"
date:   2017-05-20 14:27:00 +0800
categories: jekyll update
---

**Target audience:** This article is oriented towards people starting to use Python as a programming language. I present some quick workflow tips to get you started.  

---

<br/>
<br/>

In 2016, I moved to China to pursue a new position at a start-up company. Not only was I moving into a new country, that’s also where I started to play with something called “Big Data”. I was traditionally more oriented on embedded systems and robotics, so my experience in anything related to "Big Data" was pretty much null. The learning curve was kinda steep.

Needless to say, Python is widely adopted in the Big Data world. Learning how to program Python was definitely a big plus if you want to be efficient. I used to be somewhat versed in C/C++ and JAVA, but unfortunately not in Python. So I had to go through the basics of Python to get up to speed. 

Learning the language made me realize that Python handles a ton of stuff for you and does the extra work in many situations. Especially if you compare it to a language like C. This makes it very convenient and very fun to learn. 

After going through the "normal" things, like data structures, if/else, for loops, etc., I had to learn about how to properly create and deploy Python programs. This might not sound like it, but it's one the things I found the most helpful while trying to learn Python. When everything is well managed, keeping things clean is much more easy.

In this article I'll highlight 3 quick tips I have for people who are starting to program in Python. I hope this will help you define a workflow that works for you that will help you enjoy programming in Python even more.


<br/>

# Tip 1: Virtual environments are your friends

I learned pretty quickly about using virtual environments while programming Python. This as helped me in so many ways and saved a lot of time and headaches. You should use virtual environments if you are programming in Python. 

So what are virtual environments ? Well, you can think of it as virtual machines for Python. The goal of a virtual environment is to decouple the system's Python from your project's Python. This way, you don't clobber your system's Python with the dependencies of every single projects you are working on. Each project will have its own separate location to store the dependencies. 

It's also very easy to use different versions of Python depending on your project's requirements. Every time you create a project, you also create a virtual environment specific to it. 

Python virtual environments come in many flavors, again it's a question of which one works the best for you. Personally I enjoy [virtualenv](https://virtualenv.pypa.io/en/stable/) with the [virtualenvwrapper](https://virtualenvwrapper.readthedocs.io/en/latest/). There are also other virtual environments tools like [PyEnv](https://github.com/pyenv/pyenv) and [Conda](https://conda.io/docs/index.html) that can even support other languages than Python.

<img src="{{ site.github.url }}/assets/virtual_env.png"/>

<br/>

# Tip 2: Project scaffolding tool, a must

Something that helped me a lot to be up to speed with Python development was to define a workflow. This meant a way to prepare the projects and a way to properly test and deploy programs in Python. You should look for a project scaffolding tool in order to automate project creation. The ones that come to my mind are : 

* [PyScaffold](https://readthedocs.org/projects/pyscaffold/)
* [CookieCutter](https://cookiecutter.readthedocs.io/en/latest/)

Those are two very popular frameworks for creating projects. You should try them and see whatever one fits more you needs and workflow. I personally like PyScaffold because of its simplicity. 

To give you an example, every time I want to create a new project I follow this procedure using PyScaffold :

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

# Tip 3: Quick access code snippets 

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

Having quick access to this is really helpful and it helps keep every file somewhat standardized. So basically I just translated the Python code to HTML, saved that to a file on my computer and added a bookmark to it in my browser. 

<br/>

# Recap

So basically that's it for now. These are 3 small tips I have for anyone starting to program in Python that will contribute to having a better workflow. 

* Install a virtual environment tool, this will decouple each project from your system's Python.
* Use a project scaffolding tool, this will standardize all your projects folder.
* Quick access code templates, this will increase your coding experience and help you standardize your code.


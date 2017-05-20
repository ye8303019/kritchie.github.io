---
layout: post
title:  "Year one of Big Data: A review"
date:   2017-05-20 14:27:00 +0800
categories: jekyll update
---

Start of 2016, I moved to China to pursue a new position at a start-up company. I got this job through a friend I met at University. That’s where I started to play with something called “Big Data”. I was traditionally more oriented on embedded systems and robotics, so my experience in Data Engineering was close to 0. The learning curve was steep. 

I really like the format of the [“Effective ..."](https://www.google.com.hk/?gfe_rd=cr&ei=t_MfWZKdEIyl8wfanJko&gws_rd=ssl#q=effective+book+series) book series, so therefore I will try to highlight what I’ve learning over the past year in the same kind of fashion. 

## Python
So the first thing I had to learn this year was actually to program Python. I used to be somewhat versed in C/C++ and JAVA, but unfortunately not in Python. The first thing I did was to learn about different versions of Python. Needless to say, as of today you should already be coding in Python 3.6, unless you are using libraries and third party tools that don't support it. Otherwise, you have no excuse. 


**Define your workflow**

Something that helped me a lot to be up to speed with Python development was to define a workflow. This meant a way to prepare the projects and a way to properly test and deploy programs in Python. You should look for a project scaffolding tool in order to automate project creation. The ones that come to my mind are : 

* [PyScaffold](https://readthedocs.org/projects/pyscaffold/)
* [CookieCutter](https://cookiecutter.readthedocs.io/en/latest/)

Those are two very popular frameworks for creating projects. You should try them and see whatever one fits more you needs and workflow. 

**Quick access templates**

I found out that defining some easy to access templates was one of the best thing I did to increase my productivity. What I did was to create an HTML where I stored those templates as plain text. The goal was to make it accessible through my Web browser, because let's face it, my Web browser is pretty much always open. 

{% highlight python %}
if __name__ == '__main__':
    print('This is my favorite Python snipet')
{% endhighlight %}

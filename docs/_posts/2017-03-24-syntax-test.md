---
layout: post
title:  "Syntax Highlighting Test"
date:   2019-11-24 01:30:13 +0800
categories: default
tags: test syntax
---
Jekyll uses Rouge by default for syntax highlighting, here are some tests.

Python with line numbers:
{% highlight python linenos %}
def print_hi(name):
    print("Hi, {}".format(name))

print_hi('Tom')
# prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

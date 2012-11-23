---
layout: post
title: "Making Ipython Play Nice with Virtualenv"
description: ""
category: python
tags: [python, ipyton, virtualenv, EPD, OSX, Linux]
---
{% include JB/setup %}


I have recently created a virtualenv based on the Enthought Python Distribution for some startup ideas I'd like to develop futher. Until now I've been using the standard virtualenv python executable from within Sublime Text 2 with not issues at all until yesterday when I was using Ipython and could not load a package I've using the whole week.

By default Ipython is not virtualenv aware and I thought that I will have to go through tears of pain to make ipython understand virtualenvs. - luckily I've found this [post](http://blog.ufsoft.org/2009/1/29/ipython-and-virtualenv) with a clear and simple explanation.

Furthermore, If you have Ipython 0.12.1 (*not sure if earlier versions support this*) then is even easier! All you need to do is to save the following piece of code in ``~/.ipython/profile_default/startup/``. 

{% highlight python %}

import site
from os import environ
from os.path import join
from sys import version_info

if 'VIRTUAL_ENV' in environ:
    virtual_env = join(environ.get('VIRTUAL_ENV'),
                       'lib',
                       'python%d.%d' % version_info[:2],
                       'site-packages')
    site.addsitedir(virtual_env)
    print 'VIRTUAL_ENV ->', virtual_env
    del virtual_env
del site, environ, join, version_info 

{% endhighlight %}




``~/.ipython/profile_default/startup/`` is the IPython startup directory.

.py and .ipy files in this directory will be run *prior* to any code or files specified via the exec_lines or exec_files configurables whenever you load this profile.

Files will be run in lexicographical order, so you can control the execution order of files
with a prefix, e.g.

    00-first.py
    50-middle.py
    99-last.ipy  

I have saved mine as ``00-virtualenv.py`` and now Ipython is able to look at my virtualenv packages: - Happy days are back again!

{% highlight python %}
(epd)bash:~/projects/geo$ ipython
Enthought Python Distribution (free version) -- www.enthought.com
(type 'upgrade' or see www.enthought.com/epd/upgrade to get the full EPD)

Python 2.7.3 |EPD_free 7.3-2 (32-bit)| (default, Apr 12 2012, 11:28:34) 
Type "copyright", "credits" or "license" for more information.

IPython 0.12.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.
VIRTUAL_ENV -> /Users/albertonegron/.VIRTUALENV/epd/lib/python2.7/site-packages
{% endhighlight %}

 Happy days are back again! Enjoy!!
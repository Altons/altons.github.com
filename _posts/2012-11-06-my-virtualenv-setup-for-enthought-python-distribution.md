---
layout: post
title: "My virtualenv setup for Enthought Python Distribution "
description: ""
category: python
tags: [python, virtualenv, EPD, OSX]
---
{% include JB/setup %}


I bought this week  Wes McKinney's book [Python for Data Analysis](http://www.amazon.co.uk/gp/product/1449319793/ref=as_li_qf_sp_asin_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=1449319793&linkCode=as2&tag=outdat-21) and on the first chapter he recommends [Enthought Python Distribution](http://www.enthought.com/) as a complete scientific Python environment for following along with his book.

I am one of those users who have never been able to install numpy, scipy, matplotlib, pandas and Co successfully, so the EPD distribution is key for me. Next thing I needed it was to make EPD to play along with virtualenv.

I assume you already have virtualenv and virtualenvwrapper up & running. If not, there are good tutorials [here](http://iamzed.com/2009/05/07/a-primer-on-virtualenv/) and [here] (http://pythoncentral.org/setting-up-the-python-environment-with-virtualenv/).

Also my setup is for **OSX Lion** but you should be able to translate to any \*nix distro with almost no effort (Not sure about Windows though).

Here are the steps to install Enthought EPDFree in virtualenv:

* Download the EPDFree from [here] (http://www.enthought.com/products/epd_free.php).
* Install EPDFree (double click on the dmg).
* Type python on the Terminal and you should see something like:
{% highlight python %}
Enthought Python Distribution (EPD) free version -- www.enthought.com`
Version: 7.3-2 (32-bit)

(type 'upgrade' or see www.enthought.com/epd/upgrade to get the full EPD)

Python 2.7.3 |EPD_free 7.3-2 (32-bit)| (default, Apr 12 2012, 11:28:34) 
[GCC 4.0.1 (Apple Inc. build 5493)] on darwin
Type "credits", "demo" or "enthought" for more information.
>>> 
{% endhighlight %}
* Install pandas using ``easy_install pandas``
* Open python and import numpy and pandas. If there are no errors then you are almost ready. 
{% highlight python %}
Python 2.7.3 |EPD_free 7.3-2 (32-bit)| (default, Apr 12 2012, 11:28:34) 
[GCC 4.0.1 (Apple Inc. build 5493)] on darwin
Type "credits", "demo" or "enthought" for more information.
>>> import numpy
>>> import pandas
>>> 
{% endhighlight %}

* Your new EPD has been installed as current version but also is located in `/Library/Frameworks/Python.framework/Versions/7.3/bin/python2.7` so what we need to do is create a symbolic link to that interpreter in `usr/bin` or `/usr/local/bin`.
* I created my symlink as `sudo ln -s /Library/Frameworks/Python.framework/Versions/7.3/bin/python2.7 /usr/bin/epd` - (yes my python interpreter is now called epd)   
* Now to create a new virtualenv is as simple as ``mkvirtualenv epd --python=epd --system-site-packages``. 
{% highlight python %}
bash:~$ workon epd
(epd)bash:~$ ipython
Enthought Python Distribution (free version) -- www.enthought.com
(type 'upgrade' or see www.enthought.com/epd/upgrade to get the full EPD)

Python 2.7.3 |EPD_free 7.3-2 (32-bit)| (default, Apr 12 2012, 11:28:34) 
Type "copyright", "credits" or "license" for more information.

IPython 0.12.1 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import numpy, matplotlib, pandas

In [2]:
{% endhighlight %} 

* New packages should be installed in virtualenvs not in the system wide python installation.

Now you have a full scientific python environment up & running.

Enjoy!








(**Disclaimer**: I'll get a few bucks if you decide to buy the book through my link. Shame on me ;-)
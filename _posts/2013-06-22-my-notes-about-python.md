---
layout: post
title: "My notes About Python"
description: ""
category: python
tags: [python, tutorial]
---
{% include JB/setup %}



Notes I have compiled while learning Python. Haven't checked them for a while so be aware that some stuff may be out of date (use it under your own risk) - I am just moving them from Sphinx to Jekyll.
 

Python Ecosystem
-
While starting with Python, installing version 3.x will seem like a natural first step, it might not be exactly what you want.

Currently there are two actively developed version of Python - 2.7.x and 3.x(also called Python 3, Py3K and Python 3000). Python 3 is a different language from Python 2. There are some subtle and some stark semantic and syntactic differences. As of today, Python 2.6/2.7 is most installed and most used version. Many mainstream and important packages/frameworks/tools/utilities/modules are not yet 100% compatible with Python 3.

Therefore, the safest choice would be to use 2.x(2.7.x to be more specific). Choose Python 3 only if you need to and/or fully understand the implications.

Which VM to use
-

Python interpreter or the Python Virtual Machine has a number of different implementations, **CPython** being the main and most popular/installed implementation. **CPython** also acts as the reference implementation for other virtual machines.

**PyPy** is Python implemented in Python, **Jython** is implemented in Java and runs on Java VM and **IronPython** is the Python implementation for Microsoft **.Net**.  Unless it is really important to choose otherwise, **CPython** should be used to avoid any surprises. 

If all this gibber jabber about versions and virtual machines is giving you headaches, then all you need is **CPython** version 2.7.x. Trust me on this.

Installing Python
-

Most of the Linux/Unix distros and Mac OS X come with Python pre-installed. If your's does not or has an older version, you can install version 2.7.x with following commands:


- On Ubuntu/Debian and derivatives:

```bash
    sudo apt-get install python2.7
```

- On Fedora/Red Hat and similar systems:

```bash
  su yum install python2.7
```

- On Mac OSX: 

```bash
  brew install python --universal --framework
  python --version
```



Understanding Packages
-

First thing you need to understand that Python does not have any package management facilities by default. In fact, the very concept of packages in Python is very loose. As you might already know, Python code is organised into modules. A module can be a single file containing just one function or a directory containing one or more sub-modules. The difference between a package and a module is very minimal and every module can be thought of as a package.

So what is the difference (if any) between a module and a package? For that you first need to understand how python finds the modules.

As with any programming environment, some functions and classes (**str, len, Exception, etc.**) in Python are available in the global scope and other need to be imported by means of an \emph{import} statement. For example:

```python
  >>> import os
  >>> from os.path import basename, dirname
```

These packages must be somewhere in your filesystem so that they can be found by the import statement. How does Python know the location of these modules? These locations are set automatically when you install the Python virtual machine and are, almost always, dependent on the target platform.

The package path is always available for your inspection in **sys.path**. Here is what it looks like:

```python
   >>> import sys
   >>> print sys.path
   ['',
    '/usr/lib/python2.7',
    '/usr/lib/python2.7/plat-linux2',
    '/usr/lib/python2.7/lib-tk',
    '/usr/lib/python2.7/lib-old',
    '/usr/lib/python2.7/lib-dynload',
    '/usr/local/lib/python2.7/dist-packages',
    '/usr/lib/python2.7/dist-packages',
    '/usr/lib/python2.7/dist-packages/PIL',
    '/usr/lib/python2.7/dist-packages/gst-0.10',
    '/usr/lib/python2.7/dist-packages/gtk-2.0',
    '/usr/lib/pymodules/python2.7',
    '/usr/lib/python2.7/dist-packages/ubuntu-sso-client',
    '/usr/lib/python2.7/dist-packages/ubuntuone-client',
    '/usr/lib/python2.7/dist-packages/ubuntuone-control-panel',
    '/usr/lib/python2.7/dist-packages/ubuntuone-couch',
    '/usr/lib/python2.7/dist-packages/ubuntuone-installer',
    '/usr/lib/python2.7/dist-packages/ubuntuone-storage-protocol']
```

This will give you the list of directories where python will search for a given package. It starts at the top and keeps going downwards until a name match is found. This means if two different directories contain two packages with the same name, the package search will always stop at the first absolute match encountered and will never go further down the list.

You might have guessed by now, this package search path can easily be hacked to make sure that python picks your packages first. All you need to do is:

```python
  >>> sys.path.insert(0, '/path/to/my/packages')
```

While this approach comes in handy in many situations, you must always bear in mind that it is ``very easy to abuse`` it. Use it if you have to but don't abuse it.

PYTHONPATH
-

**PYTHONPATH** is a environment variable that can be used to augment the default package search paths. Think of it as a ``PATH`` variable but specifically for Python. It is simply a list(not a Python list like *sys.path*) of directories containing the Python modules separated by :. It can be simply set as follows:

```bash
  export PYTHONPATH=/path/to/some/dir:/path/to/another/dir:/path/to/yet/another/dir
```

In some situations you will not want to overwrite the existing \emph{PYTHONPATH}, just append or prepend to it.

```bash
  export PYTHONPATH=$PYTHONPATH:/path/to/some/directory    # Prepend
  export PYTHONPATH=/path/to/some/directory:$PYTHONPATH    # Append
```

Generally, you will put this inside the shell startup files like **.bashrc, .zshrc** etc. In most cases, you should not need to set **PYTHONPATH** explicitly. There are more elegant way of accomplishing the same effect, which I will elaborate on in a moment.

Now that you understand how python find the installed packages, we can revisit our original question. What is the difference between a module and a package? Package is just a module or a collection of modules/sub-modules, generally comes compressed inside a tarball, which contains:

1. information on dependencies - if any 
2. instructions to copy the files to the standard package search location and 
3. compile instructions - if it contains code that must be compiled before installation. That's it.

Third Party Packages
-

Right from the start, for any serious programming with Python you will need to install third party packages for various tasks.

On a Linux System there are at least 3 ways of installing the third party packages:

1. using the package management system of your distro(deb, rpm, etc.)
2. by means of various community developed tools pip, easy_install, etc.
3. installing from the source files.

All the three ways, almost always, accomplish the same thing viz. install dependencies, compile code if needed and copy the modules contained inside a package to the standard package search locations.

Where to find third packages
-

Before you can install third party packages, you will have to find them. There are more than a few ways of finding packages.

1. the modules packaged for your distro specific package management system.
2. Python Package Index(or PyPI)
3. Various source code hosting services like Launchpad, GitHub, BitBucket, etc.

Installing with PIP
-

pip is a tool for installing and managing Python packages (similar to easy_install), such as those found in the Python Package Index. pip is not installed with the Python virtual machine therefore we need to install it first. On Linux, I generally install it as follows or use easy_intall if is already installed:

```bash
  sudo apt-get install python-pip
```

Now to install any python package you run pip install package-name command. Therefore, to install simplejson you will run following command.

```bash
  sudo pip install simplejson
```

Removing packages is equally easy with it.

```bash
  sudo pip uninstall simplejson
```

By default, pip will install the most recent stable version as found on PyPI, but often you will face situations where you will want to install a specific version of a package, because your project depends on that specific version. To accomplish that you will use the pip install command as follows:

```bash
  sudo pip install simplejson==2.2.1
```

You will often want to upgrade, downgrade and/or reinstall packages. It can be done with following commands.

```bash
  sudo pip install simplejson --upgrade # Upgrade a package to the latest version from PyPI
  sudo pip install simplejson==2.2.1 --upgrade  # Upgrade/downgrade a package to a given version
```
All the above examples will install the packages system wide. If you use --user switch with pip install, the packages will be installed in your ~.local directory.

VirtualEnv
-

Different people like to setup their development environment in different ways, but in almost all programming communities, one(or more than one) way of setting up the development environment is more accepted than others. While there is nothing wrong in setting up your development environment differently, but generally these methods/setups are more tested and known to make some repetitive/biolerplate tasks in day to day work easy and maintainable.

The most popular method of setting up the development environment, in Python, is using the virtualenv package. Virtualenv is a tool to create isolated python environments. Now the question arises why do we need isolated python environment? To answer that I quote the virtualenv documentation itself.

"The basic problem being addressed is one of dependencies and versions, and indirectly permissions. Imagine you have an application that needs version 1 of LibFoo, but another application requires version 2. How can you use both these applications? If you install everything into `/usr/lib/python2.7/site-packages` (or whatever your platform's standard location is), it's easy to end up in a situation where you unintentionally upgrade an application that shouldn't be upgraded".

In simple words, you can have different/isolated python environments for each of your projects; you will install required packages for each of your projects into its own isolated environment.

Use pip to install virtualenv as well.

```bash
  sudo pip install virtualenv
```
Now once virtualenv is installed, run following commands to create an isolated python environment for your project.

```bash
  $ mkdir my_project_venv
  $ virtualenv - - distribute my_project_venv
  # The output will something like:
  New python executable in my_project_venv/bin/python
  Installing distribute.............................................done.
  Installing pip.....................done.
```

So what's happening here? You created a directory called my\project\venv to hold your new isolated python environment. The ``--distribute`` tell virtualenv to use new/improved packaging system based on distribute package instead of using old system based on setuptools.

All you need to understand right now is that ``--distribute`` option will install pip automatically within the new virtual environment so you do not have to. As your knowledge/experience as a python developer increase, you will start better understanding these under the hood nuts and bolts.

Now inspect the contents of ``my\project\venv`` directory, you will see a structure similar to:

```bash
  .
  -- bin
      -- activate  # <-- Activates this virtualenv
      -- pip       # <-- pip specific to this virtualenv
      -- python    # <-- A copy of python interpreter
  -- lib
  -- python2.7 # <-- This is where all new packages will go
```

Activate the virtualenv with following command:

```bash
  $ cd my_project_venv
  $ source bin/activate
```
After sourcing the activate script, your prompt should look something like this:

```bash
  (my_project_venv)$ # the virtualenv name prepended to the prompt
```

Now deactivate the virtualenv with following command:

```bash
  (my_project_venv)$ deactivate
```

Run following commands to better understand the difference between the system wide installation(deactivate the virtualenv first, if it is active).

First let's find out which python/pip executable will be used if I called python or pip from terminal.

```bash
  $ which python
  /usr/bin/python
  $ which pip
  /usr/local/bin/pip
```

Now do it again, but activate the virtualenv first and note the differences in the output. On my machine it looks like:

```bash
  $ cd my_project_venv
  $ source bin/activate
  (my_project_venv)$ which python
  /home/mir/my_project_venv/bin/python
  (my_project_venv)$ which pip
  /home/mir/my_project_venv/bin/pip
```

What virtualenv did, is make a copy python of executable, create a few utility scripts and a place to install project specific packages that you will eventually install/upgrade/remove over the life time of the project. It also did some package search path/PYTHONPATH magic to ensure that:

1. when you install packages, they are installed inside the currently active virtualenv and not the system wide python installation and 
2. when imported from code, the packages in the currently active virtualenv will take precedence over the ones installed in system wide Python installations.

An important thing to note here is that, by default all the packages installed inside the system wide python are automatically available to the virtualenv.

That means if you installed the simplejson package in your system wide python installation it automatically will be available to the all the virtualenvs. This behaviour can be altered by adding a - -no-site-packages switch at the time of creation of virtualenv, like:

```bash
  $ virtualenv my_project_venv --no-site-packages 
```

>   As I write this content the virtualenv has the option --no-site-packages as default - therefore some notes below may not be necessary anymore

VirtualEnvWrapper
-

**virtualenvwrapper** is a wrapper around virtualenv which provides some really nice utilities to create/activate/manage/destroy virtual environments, which otherwise will be chore. To install virtualenvwrapper, run following command:

```bash
  sudo pip install virtualenvwrapper
```
Once installed, you will need to configure it. Here is how I configure it.

```bash
  if [ `id -u` != '0' ]; then
    export VIRTUALENV_USE_DISTRIBUTE = 1        # <-- Always use pip/distribute
    export WORKON_HOME=$HOME/.virtualenvs       # <-- Where all virtualenvs will be stored
    source /usr/local/bin/virtualenvwrapper.sh
    export PIP_VIRTUALENV_BASE = $WORKON_HOME
    export PIP_RESPECT_VIRTUALENV = true
```
Logout once and login again for the configuration to take effect.
Now to create/activate/deactivate/delete a virutalenv, you will run following [self explainatory] command.

```bash

  $ mkvirtualenv my_project_venv
  $ workon my_project_venv
  $ deactivate
  $ rmvirtualenv my_project_venv
```

Basic dependency management with pip and virtualenv
-

pip in combination with virtualenv can provide basic dependency management facilities for your project.

You can use pip freeze command to export the list of currently installed packages. For example, here is the list of python packages I use to build this blog:

```bash 
  $ pip freeze -l 
  Jinja2==2.6
  PyYAML==3.10
  Pygments==1.4
  distribute==0.6.19
  markdown2==1.0.1.19
```

Note the -l switch. It tells pip to export only the packages installed in currently active virtual environment and exclude the globally installed packages from the list

You can save this exported list to a file and add it to you version control system.

```bash 
  $ pip freeze -l  > requirements.txt
```
pip can also install packages from a file containing the output of the pip freeze command.

```bash 
  $ pip install -r requirements.txt
```

Strings
-
Python strings are **immutable** which means they cannot be changed after they are created (Java strings also use this immutable style). Since strings can't be changed, we construct *new* strings as we go to represent computed values. So for example the expression ('hello' + 'there') takes in the 2 strings 'hello' and 'there' and builds a new string 'hellothere'.

Characters in a string can be accessed using the standard [ ] syntax, and like Java and C++, Python uses zero-based indexing, so if str is `'hello' str[1] is 'e'`. If the index is out of bounds for the string, Python raises an error. The Python style (unlike Perl) is to halt if it can't tell what to do, rather than just make up a default value. The handy "slice" syntax (below) also works to extract any substring from a string. The len(string) function returns the length of a string. 

The `[ ]` syntax and the `len()` function actually work on any sequence type -- strings, lists, etc.. Python tries to make its operations work consistently across different types. Python newbie gotcha: don't use "len" as a variable name to avoid blocking out the len() function. The '+' operator can concatenate two strings. Notice in the code below that variables are not pre-declared -- just assign to them and go.

```python
  s = 'hi'
  print s[1]          ## i
  print len(s)        ## 2
  print s + ' there'  ## hi there
```

Unlike Java, the '+' does not automatically convert numbers or other types to string form. The str() function converts values to a string form so they can be combined with other strings. 

```python
  pi = 3.14 
  ##text = 'The value of pi is ' + pi      ## NO, does not work
  text = 'The value of pi is '  + str(pi)  ## yes
```

For numbers, the standard operators, +, /, * work in the usual way. There is no ++ operator, but +=, -=, etc. work. If you want integer division, it is most correct to use 2 slashes -- e.g. 6 // 5 is 1 (previous to python 3000, a single / does int division with ints anyway, but moving forward // is the preferred way to indicate that you want int division.) 

The "print" operator prints out one or more python items followed by a newline (leave a trailing comma at the end of the items to inhibit the newline). A "raw" string literal is prefixed by an 'r' and passes all the chars through without special treatment of backslashes, so r'x\\nx' evaluates to the length-4 string 'x\\nx'. A 'u' prefix allows you to write a unicode string literal (Python has lots of other unicode support features -- see the docs below).

```python
  raw = r'this\t\n and that'
  print raw     ## this\t\n and that

  multi = """It was the best of times.
  It was the worst of times."""
```

String Methods
-

Here are some of the most common string methods. A method is like a function, but it runs "on" an object. If the variable s is a string, then the code s.lower() runs the lower() method on that string object and returns the result (this idea of a method running on an object is one of the basic ideas that make up Object Oriented Programming, OOP). 

Here are some of the most common string methods:


```python
s.lower(), s.upper() -- returns the lowercase or uppercase version of the string
s.strip() -- returns a string with whitespace removed from the start and end
s.isalpha()/s.isdigit()/s.isspace()... -- tests if all the string chars are in the various character classes
s.startswith('other'), s.endswith('other') -- tests if the string starts or ends with the given other string
s.find('other') -- searches for the given other string (not a regular expression) within s, and returns the first index where it begins or -1 if not found

s.replace('old', 'new') -- returns a string where all occurrences of 'old' have been replaced by 'new'
s.split('delim') -- returns a list of substrings separated by the given delimiter. The delimiter is not a regular expression, it's just text. 'aaa,bbb,ccc'.split(',') -> ['aaa', 'bbb', 'ccc']. As a convenient special case s.split() (with no arguments) splits on all whitespace chars.
s.join(list) -- opposite of split(), joins the elements in the given list together using the string as the delimiter. e.g. '---'.join(['aaa', 'bbb', 'ccc']) -> aaa---bbb---ccc
```

String Slices
-------------

The "slice" syntax is a handy way to refer to sub-parts of sequences -- typically strings and lists. The slice `s[start:end]` is the elements beginning at start and extending up to but not including end. Suppose we have `s = "Hello"`

```python
s[1:4] is 'ell' -- chars starting at index 1 and extending up to but not including index 4
s[1:] is 'ello' -- omitting either index defaults to the start or end of the string
s[:] is 'Hello' -- omitting both always gives us a copy of the whole thing (this is the pythonic way to copy a sequence like a string or list)
s[1:100] is 'ello' -- an index that is too big is truncated down to the string length
```

The standard zero-based index numbers give easy access to chars near the start of the string. As an alternative, Python uses negative numbers to give easy access to the chars at the end of the string: s[-1] is the last char 'o', s[-2] is 'l' the next-to-last char, and so on. Negative index numbers count back from the end of the string:

```python
s[-1] is 'o' -- last char (1st from the end)
s[-4] is 'e' -- 4th from the end
s[:-3] is 'He' -- going up to but not including the last 3 chars.
s[-3:] is 'llo' -- starting with the 3rd char from the end and extending to the end of the string.
```

It is a neat truism of slices that for any index n, `s[:n] + s[n:] == s`. This works even for n negative or out of bounds. Or put another way s[:n] and s[n:] always partition the string into two string parts, conserving all the characters. As we'll see in the list section later, slices work with lists too.

String % Operator
-

Python has a printf()-like facility to put together a string. The % operator takes a printf-type format string on the left (%d int, %s string, %f, %g floating point), and the matching values in a tuple on the right (a tuple is made of values separated by commas, typically grouped inside parenthesis):

```python
  # % operator
  text = ("%d little pigs come out or I'll %s and %s and %s" % (3, 'huff', 'puff', 'blow down'))
```

The above line is kind of long -- suppose you want to break it into separate lines. You cannot just split the line after the '%' as you might in other languages, since by default Python treats each line as a separate statement (on the plus side, this is why we don't need to type semi-colons on each line). 

To fix this, enclose the whole expression in an outer set of parenthesis -- then the expression is allowed to span multiple lines. This code-across-lines technique works with the various grouping constructs detailed below: ( ), [ ], { }.

Strings (Unicode)
-

Regular Python strings are *not* unicode, they are just plain bytes. To create a unicode string, use the 'u' prefix on the string literal:

```python
  >>> ustring = u'A unicode \u018e string \xf1'
  >>> ustring
  u'A unicode \u018e string \xf1'
```

A unicode string is a different type of object from regular "str" string, but the unicode string is compatible (they share the common superclass "basestring"), and the various libraries such as regular expressions work correctly if passed a unicode string instead of a regular string.

To convert a unicode string to bytes with an encoding such as 'utf-8', call the ustring.encode('utf-8') method on the unicode string. Going the other direction, the unicode(s, encoding) function converts encoded plain bytes to a unicode string:


```python
  >>> s = unistring.encode('utf-8')
  >>> s
  'A unicode \xc6\x8e string \xc3\xb1'  ## bytes of utf-8 encoding
  >>> t = unicode(s, 'utf-8')             ## Convert bytes back to a unicode string
  >>> t == unistring                      ## It's the same as the original, yay!
  True
```

The built-in print does not work fully with unicode strings. You can encode() first to print in utf-8 or whatever.

If Statement
-

Python does not use `{ }` to enclose blocks of code for if/loops/function etc.. Instead, Python uses the colon `:` and indentation/whitespace to group statements.

The boolean test for an if does not need to be in parenthesis (big difference from C++/Java), and it can have *elif* and *else* clauses (mnemonic: the word "elif" is the same length as the word "else").Any value can be used as an if-test. The "zero" values all count as false: None, 0, empty string, empty list, empty dictionary. 

There is also a Boolean type with two values: True and False (converted to an int, these are 1 and 0). Python has the usual comparison operations: ``==, !=, <, <=, >, >=``. Unlike Java and C, == is overloaded to work correctly with strings. The boolean operators are the spelled out words *and*, *or*, *not* (Python does not use the C-style \&\& || !). 

Here's what the code might look like for a policeman pulling over a speeder -- notice how each block of then/else statements starts with a : and the statements are grouped by their indentation:

```python
  if speed >= 80:
    print 'License and registration please'
    if mood == 'terrible' or speed >= 100:
      print 'You have the right to remain silent.'
    elif mood == 'bad' or speed >= 90:
      print "I'm going to have to write you a ticket."
      write_ticket()
    else:
      print "Let's try to keep it under 80 ok?"
```


Lists
-

Python has a great built-in list type named "list". List literals are written within square brackets [ ]. Lists work similarly to strings -- use the len() function and square brackets [ ] to access data, with the first element at index 0.

```python
  colors = ['red', 'blue', 'green']
  print colors[0]    ## red
  print colors[2]    ## green
  print len(colors)  ## 3
```

Assignment with an = on lists does not make a copy. Instead, assignment makes the two variables point to the one list in memory.

```python
  b = colors   ## Does not copy the list
```

The "empty list" is just an empty pair of brackets [ ]. The '+' works to append two lists, so [1, 2] + [3, 4] yields [1, 2, 3, 4] (this is just like + with strings).

Lists: FOR and IN
-

Python's *for* and *in* constructs are extremely useful, and the first use of them we'll see is with lists. The *for* construct -- for var in list -- is an easy way to look at each element in a list (or other collection). Do not add or remove from the list during iteration.

```python
  squares = [1, 4, 9, 16]
  sum = 0
  for num in squares:
    sum += num
  print sum  ## 30
```

If you know what sort of thing is in the list, use a variable name in the loop that captures that information such as "num", or "name", or "url". Since python code does not have other syntax to remind you of types, your variable names are a key way for you to keep straight what is going on.

The *in* construct on its own is an easy way to test if an element appears in a list (or other collection) -- value in collection -- tests if the value is in the collection, returning True/False.

```python
  list = ['larry', 'curly', 'moe']
  if 'curly' in list:
    print 'yay'
```

The for/in constructs are very commonly used in Python code and work on data types other than list, so should just memorise their syntax. You may have habits from other languages where you start manually iterating over a collection, where in Python you should just use for/in.

You can also use for/in to work on a **string**. The string acts like a list of its chars, so for ch in s: print ch prints all the chars in a string.

Lists: Range
-

The range(n) function yields the numbers 0, 1, ... n-1, and range(a, b) returns a, a+1, ... b-1 -- up to but not including the last number. The combination of the for-loop and the range() function allow you to build a traditional numeric for loop:

```python
  ## print the numbers from 0 through 99
  for i in range(100):
    print i
```

There is a variant xrange() which avoids the cost of building the whole list for performance sensitive cases (in Python 3000, range() will have the good performance behaviour and you can forget about xrange().

Lists: While Loop
-

Python also has the standard while-loop, and the *break* and *continue* statements work as in C++ and Java, altering the course of the innermost loop. The above for/in loops solves the common case of iterating over every element in a list, but the while loop gives you total control over the index numbers. Here's a while loop which accesses every 3rd element in a list:

```python
  ## Access every 3rd element in a list
  i = 0
  while i < len(a):
    print a[i]
    i = i + 3
```


Lists: Methods
--------------


Here are some other common list methods.


```python
list.append(elem) -- adds a single element to the end of the list. Common error does not return the new list, just modifies the original.
list.insert(index, elem) -- inserts the element at the given index, shifting elements to the right.
list.extend(list2) adds the elements in list2 to the end of the list. Using + or += on a list is similar to using extend().
list.index(elem) -- searches for the given element from the start of the list and returns its index. Throws a ValueError if the element does not appear (use 'in" to check without a ValueError).
list.remove(elem) -- searches for the first instance of the given element and removes it (throws ValueError if not present)
list.sort() -- sorts the list in place (does not return it). (The sorted() function shown below is preferred.)
list.reverse() -- reverses the list in place (does not return it)
list.pop(index) -- removes and returns the element at the given index. Returns the rightmost element if index is omitted (roughly the opposite of append()).
```

Notice that these are **methods** on a list object, while len() is a function that takes the list (or string or whatever) as an argument.

```python
  list = ['larry', 'curly', 'moe']
  list.append('shemp')         ## append elem at end
  list.insert(0, 'xxx')        ## insert elem at index 0
  list.extend(['yyy', 'zzz'])  ## add list of elems at end
  print list  ## ['xxx', 'larry', 'curly', 'moe', 'shemp', 'yyy', 'zzz']
  print list.index('curly')    ## 2

  list.remove('curly')         ## search and remove that element
  list.pop(1)                  ## removes and returns 'larry'
  print list  ## ['xxx', 'moe', 'shemp', 'yyy', 'zzz']
```

Common error: note that the above methods do not *return* the modified list, they just modify the original list.

```python
  list = [1, 2, 3]
  print list.append(4)   ## NO, does not work, append() returns None
  ## Correct pattern:
  list.append(4)
  print list  ## [1, 2, 3, 4]
```

One common pattern is to start a list a the empty list [ ], then use append() or extend() to add elements to it:


```python
  list = []          ## Start as the empty list
  list.append('a')   ## Use append() to add elements
  list.append('b')
```


Slices work on lists just as with strings, and can also be used to change sub-parts of the list.

```python
  list = ['a', 'b', 'c', 'd']
  print list[1:-1]   ## ['b', 'c']
  list[0:2] = 'z'    ## replace ['a', 'b'] with ['z']
  print list         ## ['z', 'c', 'd']
```

Lists: Sorting
--------------

The easiest way to sort is with the sorted(list) function, which takes a list and returns a new list with those elements in sorted order. *The original list is not changed*.

```python
  a = [5, 1, 4, 3]
  print sorted(a)  ## [1, 3, 4, 5]
  print a  ## [5, 1, 4, 3]
```

It's most common to pass a list into the sorted() function, but in fact it can take as input any sort of iterable collection. The older list.sort() method is an alternative detailed below. The sorted() function seems easier to use compared to sort(), so I recommend using sorted().

The sorted() function can be customized though optional arguments. The sorted() optional argument reverse=True, e.g. sorted(list, reverse=True), makes it sort backwards.

```python
  strs = ['aa', 'BB', 'zz', 'CC']
  print sorted(strs)  ## ['BB', 'CC', 'aa', 'zz'] (case sensitive)
  print sorted(strs, reverse=True)   ## ['zz', 'aa', 'CC', 'BB']
```

For more complex custom sorting, sorted() takes an optional "key=" specifying a "key" function that transforms each element before comparison. The key function takes in 1 value and returns 1 value, and the returned "proxy" value is used for the comparisons within the sort.

For example with a list of strings, specifying key=len (the built in len() function) sorts the strings by length, from shortest to longest. The sort calls len() for each string to get the list of proxy length values, and the sorts with those proxy values.

```python
  strs = ['ccc', 'aaaa', 'd', 'bb']
  print sorted(strs, key=len)  ## ['d', 'bb', 'ccc', 'aaaa']
```

As another example, specifying "str.lower" as the key function is a way to force the sorting to treat uppercase and lowercase the same:

```python
  print sorted(strs, key=str.lower)  ## ['aa', 'BB', 'CC', 'zz']
  ## "key" argument specifying str.lower function to use for sorting
```

You can also pass in your own MyFn as the key function, like this:

```python
  strs = ['xc', 'zb', 'yd' ,'wa']
  ## Say we have a list of strings we want to sort by the last letter of the string.
  ## Write a little function that takes a string, and returns its last letter.
  ## This will be the key function (takes in 1 value, returns 1 value).
  def MyFn(s):
    return s[-1]

  ## Now pass key=MyFn to sorted() to sort by the last letter:
  print sorted(strs, key=MyFn)  ## ['wa', 'zb', 'xc', 'yd']
```

To use ``key=`` custom sorting, remember that you provide a function that takes one value and returns the proxy value to guide the sorting.

There is also an optional argument "cmp=cmpFn" to sorted() that specifies a traditional two-argument comparison function that takes two values from the list and returns negative/0/positive to indicate their ordering. The built in comparison function for strings, ints, is cmp(a, b), so often you want to call cmp() in your custom comparator. *The newer one argument key= sorting is generally preferable*.

As an alternative to sorted(), the sort() method on a list sorts that list into ascending order, e.g. list.sort(). The sort() method changes the underlying list and returns None, so use it like this:

```python
  alist.sort()            ## correct
  alist = blist.sort()    ## NO incorrect, sort() returns None
```


The above is a very common misunderstanding with sort() -- it *does not return* the sorted list. The sort() method must be called on a list; it does not work on any enumerable collection (but the sorted() function above works on anything).

The sort() method predates the sorted() function, so you will likely see it in older code. The sort() method does not need to create a new list, so it can be a little faster in the case that the elements to sort are already in a list.

Tuples
-

A tuple is a fixed size grouping of elements, such as an (x, y) co-ordinate. Tuples are like lists, except they are immutable and do not change size (tuples are not strictly immutable since one of the contained elements could be mutable).

Tuples play a sort of "struct" role in Python -- a convenient way to pass around a little logical, fixed size bundle of values. A function that needs to return multiple values can just return a tuple of the values. 

For example, if I wanted to have a list of 3-d coordinates, the natural python representation would be a list of tuples, where each tuple is size 3 holding one (x, y, z) group.
To create a tuple, just list the values within parenthesis separated by commas. The "empty" tuple is just an empty pair of parenthesis. Accessing the elements in a tuple is just like a list -- len(), [ ], for, in, etc. all work the same.

```python
  tuple = (1, 2, 'hi')
  print len(tuple)  ## 3
  print tuple[2]    ## hi
  tuple[2] = 'bye'  ## NO, tuples cannot be changed
  tuple = (1, 2, 'bye')  ## this works
```

To create a size-1 tuple, the lone element must be followed by a comma.

```python 
  tuple = ('hi',)   ## size-1 tuple
```
  
It's a funny case in the syntax, but the comma is necessary to distinguish the tuple from the ordinary case of putting an expression in parentheses. In some cases you can omit the parenthesis and Python will see from the commas that you intend a tuple.

Assigning a tuple to an identically sized tuple of variable names assigns all the corresponding values. If the tuples are not the same size, it throws an error. This feature works for lists too.

```python
  (x, y, z) = (42, 13, "hike")
  print z  ## hike
  (err_string, err_code) = Foo()  ## Foo() returns a length-2 tuple
```


Exceptions
-

An exception represents a run-time error that halts the normal execution at a particular line and transfers control to error handling code. This section just introduces the most basic uses of exceptions.

For example a run-time error might be that a variable used in the program does not have a value (ValueError .. you've probably seen that one a few times), or a file open operation error because that a does not exist (IOError). (See http://docs.python.org/tutorial/errors.html)

Without any error handling code (as we have done thus far), a run-time exception just halts the program with an error message. That's a good default behavior, and you've seen it many times. You can add a "try/except" structure to your code to handle exceptions, like this:

```python
  try:
    ## Either of these two lines could throw an IOError, say
    ## if the file does not exist or the read() encounters a low level error.
    f = open(filename, 'rU')
    text = f.read()
    f.close()
  except IOError:
    ## Control jumps directly to here if any of the above lines throws IOError.
    sys.stderr.write('problem reading:' + filename)
  ## In any case, the code then continues with the line after the try/except
```

The `try:` section includes the code which might throw an exception. The ``except:`` section holds the code to run if there is an exception. If there is no exception, the except: section is skipped (that is, that code is for error handling only, not the "normal" case for the code). You can get a pointer to the exception object itself with syntax "except IOError, e: .. (e points to the exception object)".

List Comprehensions
-

A list comprehension is a compact way to write an expression that expands to a whole list. Suppose we have a list nums [1, 2, 3], here is the list comprehension to compute a list of their squares [1, 4, 9]:

```python
  >>> nums = [1, 2, 3, 4]
  >>> squares = [ n * n for n in nums ]   ## [1, 4, 9, 16]
```

The syntax is ``[ expr for var in list ]`` -- the for var in list looks like a regular for-loop, but without the colon (:). The expr to its left is evaluated once for each element to give the values for the new list. Here is an example with strings, where each string is changed to upper case with '!!!' appended:

```python
  >>> strs = ['hello', 'and', 'goodbye']
  >>> shouting = [ s.upper() + '!!!' for s in strs ]
    ['HELLO!!!', 'AND!!!', 'GOODBYE!!!']
```

You can add an if test to the right of the for-loop to narrow the result. The if test is evaluated for each element, including only the elements where the test is true.

```python
  nums = [2, 8, 1, 6]  ## Select values <= 2
  small = [ n for n in nums if n <= 2 ]  ## [2, 1]

  ## Select fruits containing 'a', change to upper case
  fruits = ['apple', 'cherry', 'bannana', 'lemon']
  afruits = [ s.upper() for s in fruits if 'a' in s ]
  ## ['APPLE', 'BANNANA']
```

Dictionaries
-

Python's efficient key/value hash table structure is called a "dict". The contents of a dict can be written as a series of key:value pairs within braces { }, e.g. dict = {key1:value1, key2:value2, ... }. The "empty dict" is just an empty pair of curly braces {}.
Looking up or setting a value in a dict uses square brackets, e.g. dict['foo'] looks up the value under the key 'foo'. 

Strings, numbers, and tuples work as keys, and any type can be a value. Other types may or may not work correctly as keys (strings and tuples work cleanly since they are immutable). Looking up a value which is not in the dict throws a KeyError -- use "in" to check if the key is in the dict, or use dict.get(key) which returns the value or None if the key is not present (or get(key, not-found) allows you to specify what value to return in the not-found case).

```python
  ## Can build up a dict by starting with the the empty dict {}
  ## and storing key/value pairs into the dict like this:
  ## dict[key] = value-for-that-key
  dict = {}
  dict['a'] = 'alpha'
  dict['g'] = 'gamma'
  dict['o'] = 'omega'

  print dict  ## {'a': 'alpha', 'o': 'omega', 'g': 'gamma'}

  print dict['a']     ## Simple lookup, returns 'alpha'
  dict['a'] = 6       ## Put new key/value into dict
  'a' in dict         ## True
  ## print dict['z']                  ## Throws KeyError
  if 'z' in dict: print dict['z']     ## Avoid KeyError
  print dict.get('z')  ## None (instead of KeyError)
```

A for loop on a dictionary iterates over its keys by default. The keys will appear in an arbitrary order. The methods dict.keys() and dict.values() return lists of the keys or values explicitly. There's also an items() which returns a list of (key, value) tuples, which is the most efficient way to examine all the key value data in the dictionary. All of these lists can be passed to the sorted() function.

```python
  ## By default, iterating over a dict iterates over its keys.
  ## Note that the keys are in a random order.
  for key in dict: print key
  ## prints a g o

  ## Exactly the same as above
  for key in dict.keys(): print key

  ## Get the .keys() list:
  print dict.keys()  ## ['a', 'o', 'g']

  ## Likewise, there's a .values() list of values
  print dict.values()  ## ['alpha', 'omega', 'gamma']

  ## Common case -- loop over the keys in sorted order,
  ## accessing each key/value
  for key in sorted(dict.keys()):
    print key, dict[key]

  ## .items() is the dict expressed as (key, value) tuples
  print dict.items()  ##  [('a', 'alpha'), ('o', 'omega'), ('g', 'gamma')]

  ## This loop syntax accesses the whole dict by looping
  ## over the .items() tuple list, accessing one (key, value)
  ## pair on each iteration.
  for k, v in dict.items(): print k, '>', v
  ## a > alpha    o > omega     g > gamma
```

**There are "iter" variants of these methods called iterkeys(), itervalues() and iteritems() which avoid the cost of constructing the whole list** -- a performance win if the data is huge. However, I generally prefer the plain keys() and values() methods with their sensible names. In Python 3000 revision, the need for the iterkeys() variants is going away.

**Strategy note:** from a performance point of view, the dictionary is one of your greatest tools, and you should use where you can as an easy way to organize data. For example, you might read a log file where each line begins with an ip address, and store the data into a dict using the ip address as the key, and the list of lines where it appears as the value. Once you've read in the whole file, you can look up any ip address and instantly see its list of lines. The dictionary takes in scattered data and make it into something coherent.


Dict: Formatting
-

The ``%`` operator works conveniently to substitute values from a dict into a string by name:

```python
  hash = {}
  hash['word'] = 'garfield'
  hash['count'] = 42
  s = 'I want %(count)d copies of %(word)s' % hash  # %d for int, %s for string
  # 'I want 42 copies of garfield'
```


Dict: Del - Operator
-

The "del" operator does deletions. In the simplest case, it can remove the definition of a variable, as if that variable had not been defined. Del can also be used on list elements or slices to delete that part of the list and to delete entries from a dictionary.

```python
  var = 6
  del var  # var no more!

  list = ['a', 'b', 'c', 'd']
  del list[0]     ## Delete first element
  del list[-2:]   ## Delete last two elements
  print list      ## ['b']

  dict = {'a':1, 'b':2, 'c':3}
  del dict['b']   ## Delete 'b' entry
  print dict      ## {'a':1, 'c':3}
```

Dict: Counting
-

A frequency table might be useful for compressing a text file. Because different letters appear with different frequencies, we can compress a file by using shorter codes for common letters and longer codes for letters that appear less frequently.

Dictionaries provide an elegant way to generate a frequency table:

```python
  >>> letter_counts = {}
  >>> for letter in "Mississippi":
  ...   letter_counts[letter] = letter_counts.get (letter, 0) + 1
  ...
  >>> letter_counts
  {'M': 1, 's': 4, 'p': 2, 'i': 4}
```

We start with an empty dictionary. For each letter in the string, we find the current count (possibly zero) and increment it. At the end, the dictionary contains pairs of letters and their frequencies.

It might be more appealing to display the frequency table in alphabetical order. We can do that with the items and sort methods:

```python
  >>> letter_items = list(letter_counts.items())
  >>> letter_items.sort()
  >>> print(letter_items)
  [('M', 1), ('i', 4), ('p', 2), ('s', 4)]
```

Notice in the first line we had to call the type conversion function list. That turns the promise we get from items into a list, a step that is needed before we can use the listâ€™s sort method.

Files
-

The `open()` function opens and returns a file handle that can be used to read or write a file in the usual way. 

The code `f = open('name', 'r')` opens the file into the variable f, ready for reading operations, and use `f.close()` when finished. 

Instead of 'r', use 'w' for writing, and 'a' for append. The special mode 'rU' is the "Universal" option for text files where it's smart about converting different line-endings so they always come through as a simple '\n'.

The standard for-loop works for text files, iterating through the lines of the file (this works only for text files, not binary files). The for-loop technique is a simple and efficient way to look at all the lines in a text file:

```python
  # Echo the contents of a file
  f = open('foo.txt', 'rU')
  for line in f:   ## iterates over the lines of the file
    print line,    ## trailing , so print does not add an end-of-line char
                 ## since 'line' already includes the end-of line.
  f.close()
```

**Reading one line at a time has the nice quality that not all the file needs to fit in memory at one time** -- handy if you want to look at every line in a 10 gigabyte file without using 10 gigabytes of memory. 

The `f.readlines()` method reads the whole file into memory and returns its contents as a list of its lines.

The `f.read()` method reads the whole file into a single string, which can be a handy way to deal with the text all at once, such as with regular expressions we'll see later.

For writing, `f.write(string)` method is the easiest way to write data to an open output file. Or you can use "print" with an open file, but the syntax is nasty. In python 3000, the print syntax will be fixed to be a regular function call with a file= optional argument: "print(string, file=f)".

For writing, use f.write() since print does not fully support unicode.

Regular Expressions
-

Regular expressions (called REs, or regexes, or regex patterns) are essentially a tiny, highly specialized programming language embedded inside Python and made available through the ``re`` module.

Using this little language, you specify the rules for the set of possible strings that you want to match; this set might contain English sentences, or e-mail addresses, or TeX commands, or anything you like. You can then ask questions such as "Does this string match the pattern?", or "Is there a match for the pattern anywhere in this string?". You can also use REs to modify a string or to split it apart in various ways.

In Python a regular expression search is typically written as:

```python
  import re
  match = re.search(pat, str)
```

The `re.search()` method takes a regular expression pattern and a string and searches for that pattern within the string.

If the search is successful, search() returns a match object or None otherwise. Therefore, the search is usually immediately followed by an if-statement to test if the search succeeded, as shown in the following example which searches for the pattern 'word:' followed by a 3 letter word (details below):

```python
  str = 'an example word:cat!!'
  match = re.search(r'word:\w\w\w', str)
  # If-statement after search() tests if it succeeded
  if match:                      
    print 'found', match.group() ## 'found word:cat'
  else:
    print 'did not find'
```

The code `match = re.search(pat, str)` stores the search result in a variable named "match". Then the if-statement tests the match -- if true the search succeeded and `match.group()` is the matching text (e.g. 'word:cat').

Otherwise if the match is false (None to be more specific), then the search did not succeed, and there is no matching text.

The 'r' at the start of the pattern string designates a python "raw" string which passes through backslashes without change which is very handy for regular expressions (Java needs this feature badly!). I recommend that you always write pattern strings with the 'r' just as a habit.

re: Basic Patterns
-

The power of regular expressions is that they can specify patterns, not just fixed characters. Here are the most basic patterns which match single chars:

```python
a, X, 9, <  ordinary characters just match themselves exactly. The meta-characters which do not match themselves because they have special meanings are:``. $ ? {} [ ] ^  * + | ( )`` (details below)
. (a period)  matches any single character except newline **'\\n'**
\w  (lowercase w) matches a "word" character: a letter or digit or underbar [a-zA-Z0-9_]. Note that although "word" is the mnemonic for this, it only matches a single word char, not a whole word. \W (upper case W) matches any non-word character.
\b  boundary between word and non-word.
\s  (lowercase s) matches a single whitespace character -- space, newline, return, tab, form [\n\r\t\f]. \S (upper case S) matches any non-whitespace character.
\t, \n, \r -- tab, newline, return
\d  decimal digit [0-9] (some older regex utilities do not support but \d, but they all support \w and \s)
^ = start, \$ = end,  match the start or end of the string
\  inhibit the "specialness" of a character. So, for example, use ``\``. to match a period or ``\`` to match a slash.
```

 If you are unsure if a character has special meaning, such as '@', you can put a slash in front of it, \\@, to make sure it is treated just as a character.


re: Basic Example
-

Joke: what do you call a pig with three eyes? piiig! 

The basic rules of regular expression search for a pattern within a string are:

- The search proceeds through the string from start to end, stopping at the first match found
- All of the pattern must be matched, but not all of the string.

If `match = re.search(pat, str)` is successful, match is not *None* and in particular ``match.group()`` is the matching text:

```python
  ## Search for pattern 'iii' in string 'piiig'.
  ## All of the pattern must match, but it may appear anywhere.
  ## On success, match.group() is matched text.
  match = re.search(r'iii', 'piiig') =>  found, match.group() == "iii"
  match = re.search(r'igs', 'piiig') =>  not found, match == None

  ## . = any char but \n
  match = re.search(r'..g', 'piiig') =>  found, match.group() == "iig"

  ## \d = digit char, \w = word char
  match = re.search(r'\d\d\d', 'p123g') =>  found, match.group() == "123"
  match = re.search(r'\w\w\w', '@@abcd!!') =>  found, match.group() == "abc"
```

re: Repetitions
-

Things get more interesting when you use + and * to specify repetition in the pattern

```python
+ means 1 or more occurrences of the pattern to its left, e.g. 'i+' = one or more i's
* means 0 or more occurrences of the pattern to its left
? means match 0 or 1 occurrences of the pattern to its left
```

Left & Most Largest
-

First the search finds the leftmost match for the pattern, and second it tries to use up as much of the string as possible -- i.e. ``+ and *`` go as far as possible (the + and * are said to be *"greedy"*).

```python
  ## i+ = one or more i's, as many as possible.
  match = re.search(r'pi+', 'piiig') =>  found, match.group() == "piii"

  ## Finds the first/leftmost solution, and within it drives the +
  ## as far as possible (aka 'leftmost and largest').
  ## In this example, note that it does not get to the second set of i's.
  match = re.search(r'i+', 'piigiiii') =>  found, match.group() == "ii"

  ## \s* = zero or more whitespace chars
  ## Here look for 3 digits, possibly separated by whitespace.
  match = re.search(r'\d\s*\d\s*\d', 'xx1 2   3xx') =>  found, match.group() == "1 2   3"
  match = re.search(r'\d\s*\d\s*\d', 'xx12  3xx') =>  found, match.group() == "12  3"
  match = re.search(r'\d\s*\d\s*\d', 'xx123xx') =>  found, match.group() == "123"

  ## ^ = matches the start of string, so this fails:
  match = re.search(r'^b\w+', 'foobar') =>  not found, match == None
  ## but without the ^ it succeeds:
  match = re.search(r'b\w+', 'foobar') =>  found, match.group() == "bar"
```

re: Email Example
-----------------

Suppose you want to find the email address inside the string 'xyz alice-b@google.com purple monkey'. We'll use this as a running example to demonstrate more regular expression features. Here's an attempt using the pattern `r'\w+@\w+'`:

```python
  str = 'purple alice-b@google.com monkey dishwasher'
  match = re.search(r'\w+@\w+', str)
  if match:
    print match.group()  ## 'b@google'
```

The search does not get the whole email address in this case because the \w does not match the '-' or '.' in the address. We'll fix this using the regular expression features below.

Square brackets can be used to indicate a set of chars, so [abc] matches 'a' or b' or 'c'. The codes `\w`, `\s` etc. work inside square brackets too with the one exception that dot (.) just means a literal dot. For the emails problem, the square brackets are an easy way to add '.' and '-' to the set of chars which can appear around the @ with the pattern `r'[\w.-]+@[\w.-]+'` to get the whole email address:

```python
  match = re.search(r'[\w.-]+@[\w.-]+', str)
  if match:
    print match.group()  ## 'alice-b@google.com'
```

(More square-bracket features) You can also use a dash to indicate a range, so [a-z] matches all lowercase letters. To use a dash without indicating a range, put the dash last, e.g. [abc-]. An up-hat \^ at the start of a square-bracket set inverts it, so [\^ab] means any char except 'a' or 'b'.

re: Group Extraction
-

The "group" feature of a regular expression allows you to pick out parts of the matching text. Suppose for the emails problem that we want to extract the username and host separately. To do this, add parenthesis ( ) around the username and host in the pattern, like this: `r'([\textbackslash{}w.-]+)@([\textbackslash{}w.-]+)'`. 

In this case, the parenthesis do not change what the pattern will match, instead they establish logical "groups" inside of the match text.

On a successful search, `match.group(1)` is the match text corresponding to the 1st left parenthesis, and `match.group(2)` is the text corresponding to the 2nd left parenthesis. The plain match.group() is still the whole match text as usual.

```python
  str = 'purple alice-b@google.com monkey dishwasher'
  match = re.search('([\w.-]+)@([\w.-]+)', str)
  if match:
    print match.group()   ## 'alice-b@google.com' (the whole match)
    print match.group(1)  ## 'alice-b' (the username, group 1)
    print match.group(2)  ## 'google.com' (the host, group 2)
```

A common workflow with regular expressions is that you write a pattern for the thing you are looking for, adding parenthesis groups to extract the parts you want.

re: findall
-

`findall()` is probably the single most powerful function in the ``re`` module. Above we used `re.search()` to find the first match for a pattern. `findall()` finds *all* the matches and returns them as a list of strings, with each string representing one match.

```python
  ## Suppose we have a text with many email addresses
  str = 'purple alice@google.com, blah monkey bob@abc.com blah dishwasher'

  ## Here re.findall() returns a list of all the found email strings
  emails = re.findall(r'[\w\.-]+@[\w\.-]+', str) ## ['alice@google.com', 'bob@abc.com']
  for email in emails:
     # do something with each found email string
     print email
```

For files, you may be in the habit of writing a loop to iterate over the lines of the file, and you could then call `findall()` on each line. Instead, let `findall()` do the iteration for you -- much better! Just feed the whole file text into `findall()` and let it return a list of all the matches in a single step (recall that `f.read()` returns the whole text of a file in a single string):

```python
  # Open file
  f = open('test.txt', 'r')
  # Feed the file text into findall(); it returns a list of all the found strings
  strings = re.findall(r'some pattern', f.read())
```

The parenthesis `( )` group mechanism can be combined with `findall()`. If the pattern includes 2 or more parenthesis groups, then instead of returning a list of strings, `findall()` returns a list of *tuples*. Each tuple represents one match of the pattern, and inside the tuple is the group(1), group(2), ... data. So if 2 parenthesis groups are added to the email pattern, then `findall()` returns a list of tuples, each length 2 containing the username and host, e.g. ('alice', 'google.com').

```python
  str = 'purple alice@google.com, blah monkey bob@abc.com blah dishwasher'
  tuples = re.findall(r'([\w\.-]+)@([\w\.-]+)', str)
  print tuples  ## [('alice', 'google.com'), ('bob', 'abc.com')]
  for tuple in tuples:
    print tuple[0]  ## username
    print tuple[1]  ## host
```

Once you have the list of tuples, you can loop over it to do some computation for each tuple. If the pattern includes no parenthesis, then `findall()` returns a list of found strings as in earlier examples.

If the pattern includes a single set of parenthesis, then `findall()` returns a list of strings corresponding to that single group. (Obscure optional feature: Sometimes you have parenthesis () groupings in the pattern, but which you do not want to extract. In that case, write the parens with a ?: at the start, e.g. (?: ) and that left paren will not count as a group result.)


re: Workflow and Debug
-

Regular expression patterns pack a lot of meaning into just a few characters , but they are so dense, you can spend a lot of time debugging your patterns. Set up your runtime so you can run a pattern and print what it matches easily, for example by running it on a small test text and printing the result of findall().

If the pattern matches nothing, try weakening the pattern, removing parts of it so you get too many matches. When it's matching nothing, you can't make any progress since there's nothing concrete to look at. Once it's matching too much, then you can work on tightening it up incrementally to hit just what you want.

The re functions take options to modify the behavior of the pattern match. The option flag is added as an extra argument to the `search()` or `findall()` etc., e.g. `re.search(pat, str, re.IGNORECASE)`.

- `IGNORECASE` -- ignore upper/lowercase differences for matching, so 'a' matches both 'a' and 'A'.
- `DOTALL` -- allow dot (.) to match newline -- normally it matches anything but newline. This can trip you up -- you think .* matches everything, but by default it does not go past the end of a line. Note that ``\s`` (whitespace) includes newlines, so if you want to match a run of whitespace that may include a newline, you can just use ``\s*``
- `MULTILINE` -- Within a string made of many lines, allow `^` and `\$` to match the start and end of each line. Normally `^/\$` would just match the start and end of the whole string.

re: Greedy vs. NoGreedy
-

This is optional section which shows a more advanced regular expression technique not needed for the exercises.

Suppose you have text with tags in it:`<b>foo</b> and <i>so on</i>`

Suppose you are trying to match each tag with the pattern `'(<.*>)'` -- what does it match first?

The result is a little surprising, but the greedy aspect of the .* causes it to match the whole `'<b>foo</b> and <i>so on</i>'` as one big match. The problem is that the .* goes as far as is it can, instead of stopping at the first > (aka it is "greedy").

There is an extension to regular expression where you add a ? at the end, such as .*? or .+?, changing them to be non-greedy. Now they stop as soon as they can. So the pattern `'(<.*?>)'` will get just '<b>' as the first match, and '</b>' as the second match, and so on getting each <..> pair in turn. The style is typically that you use a .*?, and then immediately its right look for some concrete marker (> in this case) that forces the end of the .*? run. 

The \*? extension originated in Perl, and regular expressions that include Perl's extensions are known as Perl Compatible Regular Expressions -- pcre. Python includes pcre support. Many command line utils etc. have a flag where they accept pcre patterns. 

An older but widely used technique to code this idea of "all of these chars except stopping at X" uses the square-bracket style. For the above you could write the pattern, but instead of .* to get all the chars, use [$\wedge$>]* which skips over all characters which are not > (the leading \^ "inverts" the square bracket set, so it matches any char not in the brackets).

re: Substitution
----------------

The re.sub(pat, replacement, str) function searches for all the instances of pattern in the given string, and replaces them. The replacement string can include '\1', '\2' which refer to the text from group(1), group(2), and so on from the original matching text.

Here's an example which searches for all the email addresses, and changes them to keep the user but have yo-yo-dyne.com as the host.

```python
  str = 'purple alice@google.com, blah monkey bob@abc.com blah dishwasher'
  ## re.sub(pat, replacement, str) -- returns new string with all replacements,
  ## \1 is group(1), \2 group(2) in the replacement
  print re.sub(r'([\w\.-]+)@([\w\.-]+)', r'\1@yo-yo-dyne.com', str)
  ## purple alice@yo-yo-dyne.com, blah monkey bob@yo-yo-dyne.com blah dishwasher
```

OS and os.path
-

The *os* and *os.path* modules include many functions to interact with the file system. The *shutil* module can copy files.


- `filenames = os.listdir(dir)` -- list of filenames in that directory path (not including . and ..). The filenames are just the names in the directory, not their absolute paths.

- `os.path.join(dir, filename)` -- given a filename from the above list, use this to put the dir and filename together to make a path

- `os.path.abspath(path)` -- given a path, return an absolute form, e.g. /home/nick/foo/bar.html

- `os.path.dirname(path)`, os.path.basename(path) -- given dir/foo/bar.html, return the dirname "dir/foo" and basename "bar.html"

- `os.path.exists(path)` -- true if it exists

- `os.mkdir(dir\_path)` -- makes one dir, os.makedirs(dir\_path) makes all the needed dirs in this path

- `shutil.copy(source-path, dest-path)` -- copy a file (dest path directories should exist)

```python
  ## Example pulls filenames from a dir, prints their relative and absolute paths
  def printdir(dir):
    filenames = os.listdir(dir)
    for filename in filenames:
       print filename  ## foo.txt
       print os.path.join(dir, filename) ## dir/foo.txt (relative to current dir)
       print os.path.abspath(os.path.join(dir, filename)) ## /home/nick/dir/foo.txt
```

Exploring a module works well with the built-in python help() and dir() functions. In the interpreter, do an "import os", and then use these commands look at what's available in the module: `dir(os), help(os.listdir), dir(os.path), help(os.path.dirname)`.

The Command Module
-

The *commands* module is a simple way to run an external command and capture its output.

- `(status, output) = commands.getstatusoutput(cmd)` -- runs the command, waits for it to exit, and returns its status int and output text as a tuple. The command is run with its standard output and standard error combined into the one output text. The status will be non-zero if the command failed. Since the standard-err of the command is captured, if it fails, we need to print some indication of what happened.
- `output = commands.getoutput(cmd) -- as above, but without the status int.
- There is a `commands.getstatus()`` but it does something else, so don't use it -- dumbest bit of method naming ever!
- If you want more control over the running of the sub-process, see the "popen2" module (http://docs.python.org/lib/module-popen2.html)
- There is also a simple `os.system(cmd)` which runs the command and dumps its output onto your output and returns its error code. This works if you want to run the command but do not need to capture its output into your python data structures.

```python
  ## Given a dir path, run an external 'ls -l' on it --
  ## shows how to call an external program
  def listdir(dir):
    cmd = 'ls -l ' + dir
    print "Command to run:", cmd   ## good to debug cmd before actually running it
    (status, output) = commands.getstatusoutput(cmd)
    if status:    ## Error case, print the command's output to stderr and exit
      sys.stderr.write(output)
      sys.exit(1)
    print output  ## Otherwise do something with the command's output
```

Urllib2
-


`urllib2` is a Python module for fetching URLs (Uniform Resource Locators). It offers a very simple interface, in the form of the urlopen function. This is capable of fetching URLs using a variety of different protocols. It also offers a slightly more complex interface for handling common situations - like basic authentication, cookies, proxies and so on. These are provided by objects called handlers and openers.

`urllib2` supports fetching URLs for many "URL schemes" (identified by the string before the ":" in URL - for example "ftp" is the URL scheme of ftp://python.org/) using their associated network protocols (e.g. FTP, HTTP). This tutorial focuses on the most common case, HTTP.

The simplest way to use urllib2 is as follows:

```python
  import urllib2
  response = urllib2.urlopen('http://python.org/')
  html = response.read()
```

Many uses of urllib2 will be that simple (note that instead of an 'http:' URL we could have used an URL starting with 'ftp:', 'file:', etc.). However, it's the purpose of this tutorial to explain the more complicated cases, concentrating on HTTP.

HTTP is based on requests and responses - the client makes requests and servers send responses. urllib2 mirrors this with a Request object which represents the HTTP request you are making. In its simplest form you create a Request object that specifies the URL you want to fetch. Calling urlopen with this Request object returns a response object for the URL requested. This response is a file-like object, which means you can for example call .read() on the response:

```python
  import urllib2

  req = urllib2.Request('http://www.voidspace.org.uk')
  response = urllib2.urlopen(req)
  the_page = response.read()
```

In the case of *HTTP*, there are two extra things that Request objects allow you to do: First, you can pass data to be sent to the server. Second, you can pass extra information ("metadata") about the data or the about request itself, to the server - this information is sent as HTTP "headers". Let's look at each of these in turn.

Sometimes you want to send data to a **URL** (often the URL will refer to a **CGI** (Common Gateway Interface) script or other web application). With *HTTP*, this is often done using what's known as a **POST** request. This is often what your browser does when you submit a *HTML* form that you filled in on the web. 

Not all *POSTs* have to come from forms: you can use a *POST* to transmit arbitrary data to your own application. In the common case of *HTML* forms, the data needs to be encoded in a standard way, and then passed to the Request object as the data argument. The encoding is done using a function from the *urllib* library not from *urllib2*.

```python
  import urllib
  import urllib2

  url = 'http://www.someserver.com/cgi-bin/register.cgi'
  values = {'name' : 'Michael Foord',
            'location' : 'Northampton',
            'language' : 'Python' }

  data = urllib.urlencode(values)
  req = urllib2.Request(url, data)
  response = urllib2.urlopen(req)
  the_page = response.read()
```

Some websites (like google for example) dislike being browsed by programs, or send different versions to different browsers. By default urllib2 identifies itself as Python-urllib/x.y (where x and y are the major and minor version numbers of the Python release, e.g. Python-urllib/2.5), which may confuse the site, or just plain not work.

The way a browser identifies itself is through the User-Agent header. When you create a Request object you can pass a dictionary of headers in. The following example makes the same request as above, but identifies itself as a version of Internet Explorer.

```python
  import urllib
  import urllib2

  url = 'http://www.someserver.com/cgi-bin/register.cgi'
  user_agent = 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)'
  values = {'name' : 'Michael Foord',
            'location' : 'Northampton',
            'language' : 'Python' }
  headers = { 'User-Agent' : user_agent }

  data = urllib.urlencode(values)
  req = urllib2.Request(url, data, headers)
  response = urllib2.urlopen(req)
  the_page = response.read()
```

**urlopen** raises **URLError** when it cannot handle a response (though as usual with Python APIs, builtin exceptions such as *ValueError*, *TypeError* etc. may also be raised).

*HTTPError* is the subclass of *URLError* raised in the specific case of HTTP URLs.

```python
  >>> req = urllib2.Request('http://www.pretend_server.org')
  >>> try: urllib2.urlopen(req)
  >>> except URLError, e:
  >>>    print e.reason
  >>>
  (4, 'getaddrinfo failed')
```



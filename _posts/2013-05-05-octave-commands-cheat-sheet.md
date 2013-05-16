---
layout: post
title: "Octave Commands Cheat Sheet"
description: ""
category: octave
tags: [octave, matlab, linear algebra, machine learning]
---
{% include JB/setup %}

Octave Commands Cheat Sheet
=

**Author: Alberto Negron**  
**Version: 0.8.0** <br>
**Last Update: 12<sup>th</sup> May 2013**


Management & Help
-


Task | Command
---- | -------
exits software | quit or exit
list variables | who/whos
deallocate variable(s) | clear [***varname***]
displays search path | path
Adds path to search | addpath
clear screen | clc
list .m files in dir | what
search for keyword | lookfor
displays help for function | help ***funname***
Parse and execute the contents of FILE | source(".octaverc")
List installed packages | pkg list
Install packages | pkg install [-auto or -noauto] pkg_name.tar.gz
Describe packages | pkg describe **pkg_name**
Load/Unload package | pkg load/unload **pkg_name**
Uninstall package | pkg uninstall **pkg_name**


Shell Commands
-


Task | Command
---- | -------
change working directory to dir | cd ***dirname*** 
print working directory | pwd 
print directory listing | ls \[***options***\] 
return value of named environment variable | getenv (string) 
execute arbitrary shell command string | system (cmd) 


Special Operators
-

Definition | Operator | Example
---------- | -------- | -------
comment | % | % Initialising
wildcard | \* | clear p\*
array | \[ \] | \[1 2; 3 4\]
Cell Arrays |\{ \} | p = { 10 , 15 , "rectangle" }; p{1} =10
ranges | start:step:stop (default step=1) | 1:10 or 1:10:100
variable assignment | = | A=magic(3);
do not display | ; | a=1;

Workflow
-
Task | Command
---- | -------
Suspend the execution of the program. | pause; or pause(5)
print string to the stout | fprintf("Hello World")

Vectors & Matrices
-

#####Rules

* Square brackets delimit literal matrices.
* Commas separate elements on the same row.
* Semicolons separate rows. 
* Commas may be replaced by spaces, and 
* semicolons may be replaced by one or more newlines. 
* Elements of a matrix may be arbitrary expressions, provided that all the dimensions agree.
* Optional: Matrices are denoted by uppercase letters A,B,X etc while vectors by lowercase letters a,b,y etc.

Example | Expression
------- | ----------
enter a row vector | \[ x, y, ... \] 
enter a column vector | \[ x; y; ... \] 
enter a 2×2 matrix | \[ w, x; y, z \] 
Return a row vector with N linearly spaced elements between BASE and LIMIT|linspace (BASE, LIMIT, N)
Similar to **linspace** except that the values are logarithmically spaced |  logspace(BASE, LIMIT, N)
Higher Dimensional Arrays | B(z,y,z)=1
Sorting arrays | sort(A); or sort(A, 'descend');
Return true if any element in array is non-zero | any(A)
Return true if all element are non-zero | all(A)


#####Array Operations

Task | Function
---- | --------
select elements of a vector | A(i) 
select elements of a matrix | A(i,j) 
select rows (columns) corresponding to the elements of a range | A(1:2,1); A(1:2,1:5);
select all rows (columns) | A(:,1); A(3,:);
Delete a row/column of a matrix | A(2,:)= \[\] or A(:,5) = \[\]


Linear Algebra
-

Task | Function
---- | --------
dimensions of the array | size 
returns larger dimension |length 
allocates array with 0’s | zeros
allocates array with 1’s | ones
transpose array | '
inverse of matrix | inv
pseudo inverse of matrix | pinv
Identity matrix | eye
Compute the rank of a Matrix | rank(A)
returns the lower triangular part of A | tril(A) 
returns the upper triangular part of A | triu(A) 
Create an N-by-N magic square | magic
Compute the dot product of two vectors | dot(A,B)
Compute the vector cross product of two 3-dimensional vectors X and Y | cross(X,Y)


Plotting
-

Task | Command | Example
---- | ------- | -------
2-D plot | plot | plot(x,y); plot(x, a+b+c);
Surface plot | surf | surf(theta0, theta1, J);
Contour plot | contour | contour(theta0, theta1, J, logspace(-2, 3, 20))
Specify x-, y-, or z-axis labels for the current axis | [x,y,z]label | xlabel('k lags')
Set a plot window to plot window N |figure  | figure;plot(x, a);
close figure window(s) | close | close \[***(N),all,all hidden***\]
new graphic objects are added to the plot | hold | hold on;plot(x, b);
Clear plot and restore default graphics settings | hold | hold off;
Display a legend for the axes | legend | plot(X,Y);legend('Temperature', 'Gas Demand');

Math Functions
-

Type        | Function    | Examples
------------- | ------------- | --------
Trigonometric    | sin, cos, tan    |floor((1+tan(1.2)) / 1.2)
Inverse Trigonometric    | asin, acos, atan   |
Natural Logarithm | log |
Base 10 Logarithm | log10 | log10(100)/log10(10)
Exponentiation |  exp |exp(0)
Absolute value |abs | abs(-3)
Square Root |sqrt | sqrt(3\^2 + 4\^2)
X raised to the Y power | power(X,Y) | power(3,2)
Real part of complex number | real | real(3+5I)
Imaginary part of complex number | imag | imag(3+5I)
Evaluate polynomials | polyval | polyval([2 10.1 0 6],0)

Statistical Functions
-
Task | Example  |Function
---- | -------- | ------- 
Uniform random numbers btw 0 and 1 (both excluded) | rand | rand(3,5)
Normal(0,1) random numbers | randn | randn(1,3)
Gamma distribution | randg | randg | randg(3,3)
Exponential distribution | rande | rande(1,10)
Poisson distribution | randp | randp(0.5,3,3)
Min value by column | min | min(A)
Max value by column | max | max(A)

Constants
-

Name  | Expression
----- | ----------  
Default Variable | ans  
Pi |  pi
Euler's number |  e 
Imaginary number |  i, j and I
Infinity |  inf
Not a Number |  NaN
machine precision | eps
true/false | logical 1/0

Logical Operators
-

Expression | Operator
-----------| --------
is greater than | \> 
is less than | < 
is greater than or equal to | >= 
is less than or equal to | <= 
is equal to | == 
is not equal to | ∼=  or !=
AND with short circuiting | &&
with short circuiting | &#124;&#124; 
AND | &
OR | &#124;
NOT | ∼ 


Auxiliary Functions
-

Task | Function
---- | --------
Check a scalar | isscalar(a)
Check a vector | isvector(b)
Check a matrix | ismatrix(b)
**is** func available | is TAB TAB
Type info | typeinfo(b)


String Functions
-
Task | Function  | Example
---- | -------- | --------
Compare 2 strings | strcmp | strcmp("hello","Hello")



Import & Export Data
-
Task | Command | Example
---- | ------- | -------
Read the matrix DATA from a text file | dlmread (FILE, SEP, R0, C0) | dmlread("virus.dat",",",1,1);
Write data to a text file | dlmwrite (FILE, M, DELIM, R, C) | dlmwrite("out.txt",yhat,";",1,1);
Read the csv files | csvread (FILENAME, DLM_OPTS) | csvread("input.csv");
Write csv files | csvwrite (FILENAME, X, DLM_OPTS) | csvwrite("output.csv", yhat);



Defining Functions
-

###Simplest Form

```matlab
  function name
      body
  end
```

Example:

```matlab
  function wakeup
        printf ("\a");
    end
```

### Passing Params

```matlab
  function name (arg-list)
        body
    end
```

Example:

```matlab
  function wakeup (message)
        printf ("\a%s\n", message);
    end
     
  wakeup ("Rise and shine!");
```

### Return Single Value

```matlab
  function ret-var = name (arg-list)
        body
    end
```
    
Example:

```matlab
  function retval = avg (v)
        retval = sum (v) / length (v);   
    end
```

### Return Multiple Values

```matlab
  function [ret-var1,ret-var2,…,ret-varn] = name (arg-list)
        body
    end
```

Example:

```matlab
  function [mu,sigma] = basicStat(X)
    mu = mean(X);
    sigma = std(X);
  end
```

Statements
-

### IF Statement

```matlab
  if (condition)
       then-body
    elseif (condition)
       elseif-body
    else
       else-body
    end
```

Example:

```matlab
   if (rem (x, 2) == 0)
       printf ("x is even\n");
     elseif (rem (x, 3) == 0)
       printf ("x is odd and divisible by 3\n");
     else
       printf ("x is odd\n");
     end
```     

     
> Note that the ***elseif*** keyword must not be spelled ***else if***, as is allowed in Fortran. If it is, the space between the ***else*** and ***if*** will tell Octave to treat this as a new ***if*** statement within another ***if*** statement's ***else*** clause

### SWITCH Statement

```matlab
    switch (X)
       case 1
         do_something ();
       case 2
         do_something_else ();
       otherwise
         do_something_completely_different ();
     end
```
     
Example:

```matlab
     A = 7;
     switch A
       case { 6, 7 }
         printf ("variable is either 6 or 7\n");
       otherwise
         printf ("variable is neither 6 nor 7\n");
     end
```

One advantage of using the ***switch*** statement compared to using ***if*** statements is that the labels can be strings

```matlab
  switch (X)
       case "a string"
         do_something
       ...
     endswitch
```
###WHILE Statement

```matlab
  while (condition)
       body
     end
```

Example:

```matlab
     fib = ones (1, 10);
     i = 3;
     while (i <= 10)
       fib (i) = fib (i-1) + fib (i-2);
       i++;
     end
```

### DO-UNTIL Statement

```matlab
     do
       body
     until (condition)
```

Example:

```matlab
     fib = ones (1, 10);
     i = 2;
     do
       i++;
       fib (i) = fib (i-1) + fib (i-2);
     until (i == 10)
```

### FOR Statement

```matlab    
     for var = expression
       body
     end
```

Example:

```matlab
  fib = ones (1, 10);
     for i = 3:10
       fib (i) = fib (i-1) + fib (i-2);
     end
```

###Other Cheat Sheets / Documentation

* Octave/Matlab quick Reference <http://academic.macewan.ca/davisj80/files/2012/08/octave_qr.pdf>
* Octave Quick Reference (V2.9.8) <http://folk.ntnu.no/joern/itgk/refcard-a4.pdf>
* Official Documentation <http://www.gnu.org/software/octave/doc/interpreter/>




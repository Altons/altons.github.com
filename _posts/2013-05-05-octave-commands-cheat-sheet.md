---
layout: post
title: "Octave Commands Cheat Sheet"
description: ""
category: octave
tags: [octave, matlab, linear algebra, machine learning]
---
{% include JB/setup %}


**Author: Alberto Negron**  
**Version: 0.1.0**


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
array | \[ \] | \[1 2; 3 4\]
increment | : | 1:10
variable assignment | = | A=magic(3);
do not display | ; | a=1;

Matrices
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

#####Retrieving Elements from Matrix/Vector

Task | Function
---- | --------
select elements of a vector | A(i) 
select elements of a matrix | A(i,j) 
select rows (columns) corresponding to the elements of a range | A(1:2,1); A(1:2,1:5);
select all rows (columns) | A(:,1); A(3,:);


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
Create an N-by-N magic square | magic
Compute the dot product of two vectors | dot(A,B)
Compute the vector cross product of two 3-dimensional vectors X and Y | cross(X,Y)


Plotting
-

Task | Command | Example
---- | ------- | -------
2-D plot | plot |   plot(x,y); plot(x, a+b+c);
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

Type          | Function      | Examples
------------- | ------------- | --------
Trigonometric    | sin, cos, tan    |floor((1+tan(1.2)) / 1.2)
Inverse Trigonometric    | asin, acos, atan   |
Natural Logarithm | log |
Base 10 Logarithm | log10 | log10(100)/log10(10)
Exponentiation |    exp |exp(0)
Absolute value |abs |   abs(-3)
Square Root |sqrt | sqrt(3\^2 + 4\^2)

Constants
-

Name  | Expression
----- | ----------  
Default Variable | ans  
Pi |    pi
Euler's number |    e 
Imaginary number |  i and j
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
is not equal to | ∼= 
AND with short circuiting | &&
with short circuiting | &#124;&#124; 
AND | &
OR | &#124;
NOT | ∼ 

###Other Cheat Sheets

* Octave/Matlab quick Reference <http://academic.macewan.ca/davisj80/files/2012/08/octave_qr.pdf>
* Octave Quick Reference (V2.9.8) <http://folk.ntnu.no/joern/itgk/refcard-a4.pdf>


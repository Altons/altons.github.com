---
layout: post
title: "Core JavaScript"
description: ""
category: JavaScript
tags: [javascript, programming, basics]
---
{% include JB/setup %}


<a id="index"></a>
Core JavaScript
=


This is a summary of the key core elements of the JavaScript Language.


Index
-
1. [Basic](#basic)
2. [ Basic Types](#types)
3. [Object Type](#object)
4. [Array Type](#arrays)
5. [Operators](#operators)
6. [Comparison Operators](#relational)
7. [Logical  Operators](#logical)
8. [Functions](#func)
9. [Methods](#methods)
9. [Control Structures](#control)
9. [Constructors](#constructor)

<a id="basic"></a>
Basic
-

| Syntax        | Description  
| ------------- |-------------
| //     | Anything following 2 slahes is a comment
| ; | statements end with semicolon
| var x;     | Declare a variable named x 
| x=0; | Now the variable x has the value 0
| var x=0; | Declare and assign a value to variable x
 


[back to index](#index)

<a id="types"></a>
Basic Types
-



| Type        | Description  
| ------------- |-------------
| x =1; | Numbers
| x=0.01; |  Just one number type for integers and reals
| x="Hello World"; | Strings
| x = true; x=false; | Boolean values
| x = null; | Null values
| x = undefined; | Undefined is like null

[back to index](#index)

<a id="object"></a>
Object Type
-

```javascript
var car = {
	color:"red",
	engine: "2.0 diesel",
	airCondtion: true,
	doors: 5
	};
```

| Syntax        | Description  
| ------------- |-------------
| . or []     | Access the properties of an object
| car.color | "red"
| car["doors"]    |  5 
| car.make="Toyota"; | Creates a new properties by assignment
| car.features = {};5 | {} is an empty object with no properties

[back to index](#index)

<a id="arrays"></a>
Arrays
-

| Syntax        | Description  
| ------------- |-------------
| var even = [2,4,6,8];    | Array of 4 values
| array[0] | 2: the first element (index 0) of the array
|even.length | 4: how many elements in the array
|even[4]=12;| Creates a new element in the array
|even[4]=14;| Update existing element by assignment
| var empty=[] | empty array with no elements
| empty.length | 0


Arrays and objects can hold other arrays and objects:

```javascript
var distance= [		// An array with 2 elements
	{x1:0,y1:-2},	// each element is an object
	{x2:2,y2:5}
	];

var data:{						// An object with 2 properties
		year:[2000,2001,2002] 	// the value of each property is
		value:[60,87,73]		// an array
		};
```

[back to index](#index)

<a id="operators"></a>
Operators
-

| Operator        | Example  
| ------------- |-------------
| Addition | 3+7
| Subtraction | 3-2
| Multiplication | 3*5
| Division | 12/4
| More complicated ones | distance[0].x1 + distance[1].x2
| Concatenate strings | "3" + "2"
| var count=0; | Define a variable
| count++; | Increment the variable
| count--; | Decrement the variable
| count+=2;| Same as count=count+2
| count*=3;| Same as count=count*3

[back to index](#index)

<a id="relational"></a>
Comparison Operators
-

| Operator        | Example  
| ------------- |-------------
| This is assignment, not equally test |  var x=2, y=3;
| equality | x==y
| inequality | x!=y
| less-than | x<y
| less-than or equal | x<=y
| greater-than | x>y
| greater-than or equal | x>=y



[back to index](#index)

<a id="logical"></a>
Logical Operators
-

| Operator        | Meaning  
| ------------- |-------------
| && | AND
| &#124;&#124; | OR
| ! | inverts a boolean value

[back to index](#index)

<a id="func"></a>
Functions
-

```javascript
// functions are enclosed in curly braces
function greeting(name) {
	return "Hello " + name;
	};
	
// Can be assigned to vars
var square = function(x) {
		return x*x;
		};
```

[back to index](#index)

<a id="methods"></a>
Methods
-

When we combine functions with objects, we get methods:

```javascript
var points = [ {x:0,y:0} , {x:1,y:1}];

points.dist = function() {
	var p1 = this[0];
	var p2 = this[1];
	var a = p2.x-p1.x;
	var b = p2.y-p1.y;
	return Math.sqrt(a*a+b*b);
	};

points.dist()
```

[back to index](#index)

<a id="control"></a>
Control Structures
-

```javascript
// Conditionals

function abs(x) {
	if (x>=0) {
		return x;
		}
	else
		{
		return -x;
		}
	}

// Loops
function factorial(n) {
	var product =1;
	while (n > 1) {
		product *= n;
		n--;
		}
	return product;
	}
	
function factorial2(n) {
	var i, product =1;
		for(i=2;,i<=n,i++) {
			product*=i;
			}
		return product;
	}
```

[back to index](#index)

<a id="constructor"></a>
Constructors
-

```javascript
// Define a constructor function to initialize a new Point Object
function Point(x,y) {  // By convention, constructors start with capitals
	this.x = x;		   // this keyword is the new object being initialized
	this.y = y; 	   // Store functions arguments as objects properties
	}				   // No return is necessary


// Use a constructor function with the keyword "new" to create instances

var p = new Point(1,1);

// Define methods for Point objects by assigning them to the prototype
// object associated with the constructor function

Point. prototype.r = function() {
	return Math.sqrt( this.x*this.x + this.y*this.y);
	};
	
```

[back to index](#index)

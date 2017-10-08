##SYNTAX PARSER

A programmer that reads your code and determines what it does and if the grammar is valid

Your code isn't magic. Someone else wrote a program to translate it for the computer.


##LEXICAL ENVIRONMENT

Where something sits physically in the code you write.

'Lexical' means 'having to do with words or grammar'.

A lexical environment exists in programming languages in which where you write something is important.

We are talking about where its sitting and what surronds it.

##EXECUTION CONTEXT

A wrapper to help manage the code that is running.

There are lots of lexical environments.

It can contain things beyond what you've written on your code.

##NAME/VALUE PAIR

A name which maps to a unique value.

The name may be defined more than once, but can only have one value in any given context.

##OBJECT

A collection of name value pairs.

That value in itself might have another object and it would nest down.

## THE GLOBAL ENVIRONMENT AND THE GLOBAL OBJECT

Execution Context (GLobal)

The execution context is your global context.

It has a few things that come along for the ride. The thing that is accessible to everything in your code.

It creates two things for you:

1. Global Object(window)
2. 'this' - creates a special variable

Then you have the following:

3. Outer ENVIRONMENT
4. your code

##GLOBAL

Not inside a function

The execution Context: Creation and 'Hoisting'

```js
b();
console.log(a);
var a = 'Hello World';
function b() {
  console.log('called b');
}

Output:
called b
undefined
```

* Execution Context is created(CREATION PHASE 1)

The reason js behaves the way it does. 

That vars and functions  are to some degree available even though the exist later on in the code is because the execution context is created in two phases.

**Creation phases**

We have:

1. Global object
2. 'this'
3. Outer environment

* Set up Memory Space for Variables and Functions "Hoisting"

It's not actually moving code to the top of the page. 

Before your code begins to be executed line by line the JS engine has already set up memory space for the variables and functions you've created. 

So the functions and variables exist in memory.

The js engine when it sets up the memory space for a variable it won't know what its value will ultimately end up being until the execution phase so it gives a default value of undefined.

**All variable are initially set to undefined.**

"Hoisting"
 - Variable setup
 	(and set equal to 'undefined')
 - Function setup

**Undefined** - special value that js has within it internally which means the var has not been set but has got memory space in the creation phase.

**Is not defined** - there is no variable because in creation phase it didn't set anything up in memory. So by execution phase that is how it tells you that.

## THE EXECUTION CONTEXT: CODE EXECUTION

Execution Context runs code(Execution Phase)

It runs your code line by line

SINGLE THREADED: 

* One command at a time


SYNCHRONOUS:

* One at a time and in order

## FUNCTION INVOCATION AND THE EXECUTION STACK

INVOCATION:

Running a function or calling a function

In js by using parenthesis

Every function creates a new execution context which goes through a creation phase and then runs line by line through the execution phases

```js
function b() {
}

function a() {
  b();
}

a();
```

Stack translation:


3. b()
    Execution Context
    (create and execute)

2. a()
    Execution Context
    (create and execute)

1. Global Execution Context
    (created and code is executed)
    
```js
 function a() {
  b();
  var c;
}

 function b() {
   var d;
 }

a();
var d;
```

Stack translation:

4. a() finished with declaring var c;

3. b()
    Execution Context
    (create and execute)
    var d is then accessed and this is popped off the stack

2. a()
    Execution Context
    (create and execute)

1. Global Execution Context
    (created and code is executed)
    
## FUNCTIONS, CONTEXT AND VARIABLE ENVIRONMENTS
 
VARIABLE ENVIRONMENTS:
 
Where the variables live
 
How they relate to each other in memory
 
```js
 function b() {
  var myVar;
  console.log(myVar);
}

function a() {
  var myVar = 2;
  console.log(myVar);
  b();
}

var myVar = 1;
console.log(myVar);
a();

output:
1
2
undefined
 

//Example

function b() {
  console.log(myVar); // output: 1
}

function a() {
  var myVar = 2;
  b();
}

var myVar = 1;
a()

Output: 1
```


Every execution has a reference to its outer environment.
In the case of function b (even a) the reference to the outer environment to the Global Execution context.

![](images/scope-chain.png)

```js
function a() {

  function b() {
    console.log(myVar); // output: 1
  }

  var myVar = 2;
  b();
}

var myVar = 1;
a()

Output: 2

```

![](images/scope-chain-reference.png)

If you struggle to think about it lexically think about it like who created me?

In this case *'function b'* isn't created until *'function a's'* execution context  is created because the global is just going to add *myVar* and *a()* then when when a is run its going to go through its create phase and say 'ah I need to create *b*'. 

Since *b* was created during the creation phase of *a's* execution context then a is its outer reference.

##SCOPE,ES6 and LET

SCOPE:

Where a variable is available in your code

And if its truly the same variable, or a new copy

//LET

Allows the js engine to use block scoping.
It's declared inside a block and only available inside that block.

//What about asynchronous callbacks?

**ASYNCHRONOUS:**

More that one at a time

MISSING JS ENGINE PIC

The JS engine doesn't run by itself.

There are other engines running outside of this.

Rendering Engine - renders, prints or paints to the screen

HTTP request - elements of the browser which have to deal with requests and response e.g. request data

all that is running while it is running asynchronously in the browser. What happens in the javascript engine however, run synchronously.

###Event Queue

![](images/js-engine.png)


There is another list other than the call stack which is called the **Event Queue**.

This is full of events, notifications of events that may be happening.

So when the browser somewhere outside the js engine has an event that inside the js we want to be notified of, it gets placed in the queue.

Whether or not we have a function that needs to respond to it we can listen to for that event and have that function handle it, but either way the event gets placed on the queue.

A click event for example or another http request

###WHAT HAPPENS?

The event queue gets looked at when the execution stack is empty.

Then that stack is empty then it periodically looks at the event queue, it waits for something to be there and if something is there its looking to see if a particular function is meant to be run when that event is triggered.

e.g. so it sees a click event, it processes the click event knows 'hey theres a function that needs to be run for that event' so creates and executes an execution context for it. Then moves on to the next one.

But again the event queue won't be processed until the execution stack is empty, Until JS runs through the code line by line.

So whats happening is the browser is asynchronously putting things into the event queue. 
But the code that is running line by line and then when the call stack is empty (all execution contexts are gone) then it processes the events, it waits for them and sees the event and if an event causes a function to be created and executed then it will be put on execution stack and run like normal.


```js
function waitThreeSeconds() {
  var ms = 3000 + new Date().getTime();
  while(new Date() < ms){}
    console.log('finished function');
}

function clickHandler() {
  console.log('click event!');
}

//listen for the click event
document.addEventListener('click', clickHandler);

waitThreeSeconds();
console.log('finished execution');

Output:
finished function
finished execution
click event!
```

Remember the call stack has to be empty before it goes the event queue and create an execution stack for it (the click handler function). So long running functions can actually interrupt events being handled.

But this is how js is dealing with the fact that asynchronous events are happening, that elsewhere simultaneously in the browser in this example that things are happening,that when complete that js needs to know about so it just runs its normal code and when thats all done it will go and look at the event queue and if its already done it will just continue to watch that event queue and it will loop the event loop.

so thats how javascript though synchronous deals with asynchronous events. Any events that happens outside the engine it will be put in the queue and when the call stack is empty it will process that events in the queue in the order they happened.

##DYNAMIC TYPING

You don't tell the engine what type of data a variable  holds, it figures it out while your code is running.

Variables can hold different types of values because it's all figured out during execution.

##PRIMITIVE TYPES

A type of data that represents a single value.

That is, not an object


There are 6 primitive types.

1. Undefined - undefined represents lack of existence(you shouldn't set a variable to this)
2. Null - null represents lack of existence (you can set a variable to this, leave undefined for the engine)
3. Boolean - true or false.
4. Number - floating point number(there's always some decimals). Unlike other programming languages there's only one 'number' type...and it can make math weird
5. String - a sequence of characters (both '' and "" can be used)
6. Symbol - Used in ES6


##OPERATORS

A special function that is syntactically (written) differently

Generally operator takes two parameters and returns one results

##OPERATOR PRECEDENCE AND ASSOCIATIVITY

####OPERATOR PRECEDENCE

hich operator function gets called first

Function are called in order or precedence (HIGHER precedence wins)

####ASSOCIATIVITY:

What order an operator gets called in: Left-to-right or right-to-left

TIP: you can checkout js precedence in js precedence e.g. multiplication has precedence of 14 and addition is 13 so multiplication has higher precedence

```js
var a = 3 + 4 * 5;
console.log(a);

Output:
23
```

###COERCION

Converting a value from one type to another
This happens quite often in Javascript because its dynamically typed.

###COMPARISON OPERATORS

```js
console.log(1 < 2 < 3); //true
console.log(3 < 2 < 1); //true
```

Even though it uses comparison operator occurring twice so same precedence. however ASSOCIATIVITY is left to right for comparison operators.

###DEFAULT VALUES

Undefined is the default value until you assign a value

```js
function greet(name) {
  name = name || '<Your name here>'; //ES6
  console.log('Hello ' + name);
}

greet();
```

Whats going to happen if **undefined** ends up being the name when the function is called?

So what does the OR operator return when you pass parameters to it?

```js
true || false
Output: true

undefined || 'hello'
Output: "hello"
```

It returns in this the case the value that can be coerced to true.

```js
"hi" || "hello"
Output" "hi"
```

It will return hi just because thats the 1st one.

Summary:
The OR operators special behaviour in that if you pass 2 variables that can be coerced to true and false. It will return the 1st that coerces to true.

```js
0 || 1
Output: 1

"" || "hello"
Output: "hello"

null || "hello"
Output: "hello"

//Example 1 revisit

function greet(name) {
  name = name || '<Your name here>'; //ES6
  console.log('Hello ' + name);
}

greet('Tony');
greet();

Output:
'Hello Tony'
'Hello <Your name here>'
```
##OBJECTS AND functions

####Objects and the document

Objects are collection of name value pairs

An object can have properties and methods

An object can have the following:

- Primitive "property"
  *e.g. boolean, number*
- Object "property"
  *e.g. an object can have another object attached to it a a child*
- Function "method"
  *e.g. An object can also have functions but because its attached to the object they are called methods.*

and these sit in memory.
  
The object has references to the space, address or dots where these different properties and properties live.

![](images/function-object.png)

```js

// example (this is not the best way)

var person = new Object();

person["firstName"] = "Tony";

It's going to create that spot in memory and give it that name and this object will get a reference to that allocation in memory.

person["lastName"] = "Alicea";

var firstNameProperty = "firstName";

console.log(person);
console.log(person[firstNameProperty]);

Output:
Object
Tony

//example the more common operator for accessing properties and methods

console.log(person.firstName);

Output:
Tony

//an object inside an object
person.address = new Object();
person.address.street = "64 zoo lane";
person.address.city = "New York";
person.address.state = "NY";

console.log(person.address.street)
console.log(person["address"]["state"])
Output:
"64 zoo lane"
"NY"
```

The associativity of the member access(.) and computer member access([]) is left to right.

**TIP:** Always use the Dot operation(.)

##OBJECTS AND OBJECT LITERALS

####OBJECT LITERALS

var person = new Object();

Theres a shorthand for this called Object Literal

```js
var person = {};
console.log(person);

Output:
Object

var Tony = {
  firstName: 'Tony',
  lastName: 'Alicea',
address: {
  street: '64 zoo lane',
  city: 'New York',
  state: 'NY'
  }
};
console.log(person);

function greet(person) {
  console.log('Hi ' + person.firstName);
}

greet(Tony);
greet({
  firstName: 'Mary',
  lastName: 'Doe'
  });

Tony.address2 = {
  street: '333 Second St.'
}

console.log(Tony);

Output:
Hi Tony!
Hi Mary!
{firstName: "Tony", lastName: "Alicea", address: {…}, address2: {…}}
```


//NAMESPACE

A container for variables and functions

Typically to keep variables and functions with the same name separate

JS doesn't have namespaces but we can do that with objects which will act as a container.


```js
var greet = 'Hello!';
var greet = 'Hola!';

console.log(greet);

var english = {};
var spanish = {};

english.greet = 'Hello!';
spanish.greet = 'Hola!';


console.log(english);

Output:
Object{greet: hello}
-------------------

var english = {
  greetings: {
    basic: 'Hello!'
  }
};

console.log(english);

Output:
Object{greetings: Object}
```
##JSON AND OBJECT LITERALS

**JSON **- JavaScript Object Notation

```js

var objectLiteral = {
  firstName: 'Mary',
  isAProgrammer: true
}

console.log(objectLiteral);
```

Now we tend to send information using the JSON format. It's just a string but it looks like object literal syntax except for some differences.

* Properties have to be wrapped in quotes

```json
{
  "firstName": "Mary",
  "isAProgrammer": true
}
```

So JSON isn't really a part of javascript but because its so easy for js to understand what JSON is JS does come with some built-in functionality to transfer between the two.

So for any object you can do the following.

* Convert the object into a JSON string using **JSON.stringify** and passing in the object

```json
console.log(JSON.stringify(objectLiteral));

Output:
{"firstName": "Mary", "isAProgrammer": true}
```

* Convert JSON string into an object using **JSON.parse** and passing in the string.

```js
var jsonValue = JSON.parse('{ "firstName": "Mary", "isAProgrammer": true}')

console.log(jsonValue);


Output:
Object {firstName: 'Mary', isAProgrammer: true
}
```


JSON is more strict.

It requires you to put quotes around names and doesn't allow you to put functions as values.

##FUNCTIONS ARE OBJECTS

####First Class Functions

Everything you can do with other types you can do with functions.

Assign them to a variable, pass them around, create them on the fly.

![](images/function-object.png)

Function is a special type of object because it has all the features of a normal object and has some other special properties.

You can attach other properties such as:

- Primitive
- Object
- Function

It also has other properties:

- NAME - optional, can be anonymous
- CODE - that you write is just one of those properties. Whats special about that property is that it is "Invocable" (), meaning that you can say run this code sitting on the property and thats when the execution context is created and run.

**SUMMARY:**

It is imperative to think of function as an object whose code happen to be one of the properties of the object that can be passed around and properties added onto it.

```js
function greet() {
  console.log('hi');
}

greet.language = 'english';
console.log(greet.language);

Output:
english
```

##FUNCTION STATEMENTS AND FUNCTION EXPRESSIONS

The main difference between a function expression and a function statement is the function name, which can be omitted in function expressions to create anonymous functions.

####EXPRESSION:

A unit of code that results in a value

It doesn't have to save to a variable

```js
a = 3; //unit of code
Output: 3

a = {greeting: 'hi'}
Output: Object

var anonymousGreet = function() {
  console.log('hi');
}
```

####STATEMENT:
This is a function statement because that in itself does not return a value e.g. if statement and inside the if statement you have en expression e.g. (a === 3)

```js
function greet() {
  console.log('hi');
}
```

function statements gets hoisted and gets evaluated ahead of time.



```js
greet();

function greet() {
  console.log('hi');
} //hi

anonymousGreet(); //undefined

anonymousGreet = function() {
  console.log('hi');
}

//remember functions in js are objects so I'm creating an object on the fly and setting to this variable and that variable will save to an address in memory.

Output:
hi
Uncaught TypeError: undefined is not a function
```

Function is hoisted and it saves the variable to memory and initially sets it to undefined until it hits the execution phase.

**Important:** Function expressions are not hoisted

A function expression that is passing a function as a parameter to another function.

```js
function log(a) {
  a(); //invoking the function thats been passed down below
}

//You can pass in a number, string or even in this case an object
log(function(){
  console.log('hi');
 });
```

**Summary:**

The concept of **First Class Functions** where you can pass functions around to other functions, use them do you like variables. This introduces an entire a new class of programming of functional programming.

##BY VALUE VS BY REFERENCE

![](images/function-object.png)

####BY VALUE

When passing or referencing or setting equal one value to another by copying the value.The two variables become the same by copying the value into two different spots into memory.

When you set a variable to an object and try to get the two variable to be the same value like (b = a or pass to a function) that new variable b instead of getting a new location in memory it points to the same location in memory that a does. No new object or copy of it is created, it points to the same address.
This is called **BY REFERENCE**.

**All objects act by reference.**

Relate: imagine having asma and assman now bother different names for yourself but whichever one someone refers to you, it don't matter because you still live in the same place.

```js
b = a;
a = 2;

console.log(a);
console.log(b);

//by reference( all objects(including functions))
var c = {greeting: 'hi'};
var d;

d = c;
c.greeting = 'hello'; //mutate
 console.log(c);
 console.log(d);
 
  //by REFERENCE (even as parameters)
function changeGreeting(obj) {
  obj.greeting = 'Hola'; //mutate
}

changeGreeting(d);
console.log(c);
console.log(d);

//equal operator sets up new memory space(new address)

c = { greeting: 'howdy'};
console.log(c);
console.log(d);
```

The equals operator saw that the object did not exist in memory yet and so it added it and pointed *'c'* there. *'c'* is now pointing to a different spot in memory from *'d'*.

continued...

```js
Output:
 2
 3

 Object{greeting:'hello'}
 Object{greeting:'hello'}

'Hola'
'Hola'
```


####MUTATE

To change something

"Immutable" means it can't be changed


**SUMMARY:**

* All **PRIMTIVE** types are **BY VALUE**
* All **OBJECTS** are by **REFERENCE**

##OBJECTS, FUNCTIONS and 'this'

Remember when a function is invoked a new execution context is created.

Think of execution context as focusing on that code portion of the object.

* An execution context is created
* Each execution has this variable environment where the variables created inside that function live
* It has a reference its outer lexical environment where it sits in the scope chain.
* The js engine every time an execution context is created, every time a function is run it gives us a variable called **'this'**
* **'this'** will be pointing at a different object depending on how the function is invoked
 **PUT PICTURE**

```js

function a() {
  console.log(this);
  this.newvariable = 'hello' //it's attaching it to the global object
}

var b = function() {
  console.log(this)
}

a();
Output: Window

console.log(newvariable);
Output: hello

b();
Output: Window

//object literal

var c = {
  name: 'The c object',
  log: function(){
  this
    console.log(this) //the this keyword becomes the object the method is sitting in
  }
}

c.log();
Output:
{name: "The c object", log: function}

//minor change:

var c = {
  name: 'The c object',
  log: function(){
  this.name = 'Updated c object';
    console.log(this) //the this keyword becomes the object the method is sitting in
    
    var setname = function(newname){
    		this.name = newname; //this keyword is referring to the global object
   }
   setname('Updated again! The c object');
   console.log(this)
  }
}

c.log();
Output:
{name: "Updated c object", log: function}
{name: "Updated c object", log: function}


//avoiding this reffering to the global object by setting this to a viariable
var c = {
  name: 'The c object',
  log: function(){
  var self = this; <--tada!!
  self.name = 'Updated c object';
    console.log(self);
    
    var setname = function(newname){
    		self.name = newname;
   }
   setname('Updated again! The c object');
   console.log(self);
  }
}

Output:
{name: "Updated c object", log: function}
{name: "Updated again! The c object", log: function}
```

## CONCEPTUAL ASIDE: ARRAYS - COLLECTION OF ANYTHING

Arrays can hold anything because js is dynamically typed, it will just figure it out in the fly.

##'arguments' AND SPREAD

picture

arguements contains a list of all the values you pass to whatever function your calling.

###ARGUEMENTS:
The parameters you pass to a function

Javascript gives you a keyword of the same name which contains them all

```js
function greet(firstName, lastName, language){

	language = language || 'en'
	
	if(arguements.length === 0) {
		console.log('Missing parameters!');
		console.log('----------');
		return;
	}
	
	console.log(firstName);
	console.log(lastName);
	console.log(language);
	console.log(arguements);
	console.log('arg:0',arguments[0]);
	console.log('----------');
}

greet();
OUTPUT:
'Missing parameters!'

greet(John);
OUTPUT:
John
undefined
en
['John', 'en']
arg 0: John
```

Hoisted and set them to undefined ang given memory space. Js doesn't care if you don't pass in arguements.

Arguments will become deprecated.



###SPREAD OPERATOR

```js

function greet(firstName, lastName, language = 'en', ...others){
	
	if(arguements.length === 0) {
		console.log('Missing parameters!');
		console.log('----------');
		return;
	}
	
	console.log(firstName);
	console.log(lastName);
	console.log(language);

}

greet(John, , , '123 barneby street', 'hot choca');
OUTPUT:
John
undefined
en
```

###FRAMEWORK ASIDE - FUNCTION OVERLOADING

In other prog languages theres this idea of function overloading.

That means you can have a function of the same name which has different number of parameters. That isn't available with javascript.

```js
function greet(firstname, lastname,language) {

language = language || 'en';

if(language === 'en'){
	console.log(`Hello ${firstname} ${lastname}`}
	
if(language === 'es'){
	console.log(`Hola ${firstname} ${lastname}`}	
	
}
greet('John', 'Doe', 'en');
greet('John', 'Doe', 'es');
```

Alternatively

```js

function greet(firstname, lastname,language) {

	language = language || 'en';
	
	if(language === 'en'){
		console.log(`Hello ${firstname} ${lastname}`)
		}
		
	if(language === 'es'){
		console.log(`Hola ${firstname} ${lastname}`}	
		
	function greetEnglish(firstname, lastname) {
		greet(firstname, lastname, 'en');
	}
	
	function greetSpanish(firstname, lastname) {
		greet(firstname, lastname, 'es');
	}
}
greetEnglish('John', 'Doe');
greetSpanish('John', 'Doe');

OUTPUT:
Hello John Doe
Hola John Doe
```
##CONCEPTUAL ASIDE - SYNTAX PARSERS

it is going through your code character by character, making assumptions, stating certain rules and can even making changes to your code before its executed.

##DANGEROUS ASIDE

####AUTOMATIC SEMICOLON INSERTION

Anywhere where the syntax parsers expects the semi-colon to be, it will put one for you. You never want that especially in the case of return;

```js
function getPerson() {
	return 
		{
		firstname: 'Tony'
		}
	}
	
	console.log(getPerson());
	
	OUTPUT: 
	undefined
```

It was undefined because of automatic semicolon insertion. JS engine if it sees a carriage return after keyword return it will automatically insert a semicolon essentially running the following.

```js
function getPerson() {
	return; 
		{
		firstname: 'Tony'
		}
	}
	
	console.log(getPerson());
	
	OUTPUT: 
	undefined
```

Avoid it like this by putting curly braces on same line

```js
function getPerson() {
	return {
		firstname: 'Tony'
		}
	}
	
	console.log(getPerson());
	
OUTPUT: 
undefined
```

COME BACK TO WHITESPACE VIDEO

##IIFE

COME BACK TO MAKE NOTES

##UNDERSTANDING CLOSURES

```js
function greet(whattosay) {

	return function(name) {
		console.log(whattosay + ' ' + name);
	}
	
}

greet('Hi')('Tony');
OUTPUT:
Hi Tony


seeing it differently:

function greet(whattosay) {

	return function(name) {
		console.log(whattosay + ' ' + name);
	}
	
}

var sayHi = greet('Hi');
sayHi('Tony');
OUTPUT:
Hi Tony
```


part 2
insert pics of execution stack 
```js
function buildFunctions() {
	var arr = [];
	
	for (var i = 0; i< 3; i++) {
		arr.push(function() {
			console.log(i);
			});
		}
		
	return arr;
	
}

var fs = buildFunctions();

fs[0]();
fs[1]();
fs[2]();
OUTPUT:
3
3
3
```
//rough explanation:
everytime the loop is run it pushs an object (arr.push etc) in to the array
The loop only stop once it is no longer less than 3 
at this point the execution stack for the function which console logs i is invoked and it doesn't have a reference of i so it goes up the scope chain. 
Now the execution stack for the loop has been popped off but variable i is still stored in memory and i is 3 by the time you call all these functions and so each element in the arrays execution stack refers to that i.


Block scope with LET keyword in loop:

```js
function buildFunctions() {
	var arr = [];
	

	for (let i = 0; i< 3; i++) {
		arr.push(function() {
			console.log(i);
			});
		}
		
	return arr;
	
}

var fs = buildFunctions();

fs[0]();
fs[1]();
fs[2]();
OUTPUT:
0
1
2

function buildFunctions() {
	var arr = [];
	

	for (var i = 0; i< 3; i++) {
		let j = 1; <--
		arr.push(function() {
			console.log(j);
			});
		}
		
	return arr;
	
}

var fs = buildFunctions();

fs[0]();
fs[1]();
fs[2]();
OUTPUT:
0
1
2

or the ES5 way by doing an IIFE
function buildFunctions() {
	var arr = [];
	

	for (var i = 0; i< 3; i++) {
		arr.push(
		(function(j) {
			return function() {
				console.log(j);
            }
			}(i))
		 )
		}
		
	return arr;
	
}

var fs = buildFunctions();

fs[0]();
fs[1]();
fs[2]();
OUTPUT:
0
1
2

```

##FRAMEWORK ASIDE: FUNCTION FACTORIES

factory just means a function that return or makes other things for me

```js
function makeGreeting(language) {
 
    return function(firstname, lastname) {
     
        if (language === 'en') {
            console.log('Hello ' + firstname + ' ' + lastname);   
        }

        if (language === 'es') {
            console.log('Hola ' + firstname + ' ' + lastname);   
        }
        
    }
    
}

var greetEnglish = makeGreeting('en');
var greetSpanish = makeGreeting('es');

greetEnglish('John', 'Doe');
greetSpanish('John', 'Doe');

```
make greetinghighlighted pic and manya more

##CLOSURES AND CALLBACKS

```
function sayHiLater() {

	var greeting = 'Hi!';
	
	setTimeout(function() {
	console.log(greeting);
	}, 3000);

}

sayHiLater();
OUTPUT:
Hi! - after three seconds
```
CALLBACK FUNCTION:
A functionyou give to another function, to be run when the other function is finished

So the function you call (i.e. invoke),'calls back' by calling the function you gave it when it finishes .

```js
function tellMeWhenDone(callback) {
 var a = 1000; //some work
 var b = 2000; //some work
 
 callback(); //the 'callback it runs the function I give it!

}

tellMeWhenDone(function() {
 console.log('I am done');
 });
 
 tellMeWhenDone(function() {
 console.log('All done...');
 });

OUTPUT:
I am done
All done
```

##CALL(),APPLY() AND BIND()

picture of what a function is and what it contains

All functions have access to the following methods:

* call()
* apply()
* bind()


```js 
var person = {
	firstname: 'John',
	lastname: 'Doe',
	getFullName: function() {
	
	var fullname = this.firstname + ' ' + this.lastname;
	return fullname;
	
 }
}

var logName = function(lang1, lang2) {
console.log('Logged:' + this.getFullName());

}

//Now at the moment the this.getFullName in Logname is undefined because it is not in the global object and that is what 'this' is pointing to . So wouldn't it be useful if we can control what its pointing to?


var logPersonName = logName.bind(person);
notice im not invoking the function and then applying.bind. Because the function return a value I'm looking to use the function as an object and call a method on the object. Then I can pass onto it whatever object I want to be the 'this' variable when the function runs. The bind method returns a new function so it actually makes a copy of the logPersonName function and sets up this new copy so whenever its run and execution context is created the js engine will see that this is created with this bind.


logName();


//or you could even do it like this:

var person = {
	firstname: 'John',
	lastname: 'Doe',
	getFullName: function() {
	
	var fullname = this.firstname + ' ' + this.lastname;
	return fullname;
	
 }
}

var logName = function(lang1, lang2) {
console.log('Logged:' + this.getFullName());
}.bind(person);


logName();
Output:
Logged: 'John Doe'


//Moving on:

var person = {
	firstname: 'John',
	lastname: 'Doe',
	getFullName: function() {
	
	var fullname = this.firstname + ' ' + this.lastname;
	return fullname;
 }
}

var logName = function(lang1, lang2) {
console.log('Logged:' + this.getFullName());
console.log('Arguements: ' + lang1 + lang2);
console.log('---------');
}

var logPersonName = logName.bind(person);


logPersonName('en');
logName.call(person);
//also lets me decide what the this variable points to. Unlike bind which creates a copy of the function call actually executes it. You can pass in parameters,
logPersonName(person, 'en' , 'es');


logName.apply(person,['en','es']
this does the exact same thing except for one difference you can't just pass parameters like you would in call. The apply method wants an array of parameters.

Output:
//bind
Logged: 'John Doe'
'Arguements: en undefined
------------------------
//call
Logged: 'John Doe'
'Arguements: en es
------------------------
//apply
Logged: 'John Doe'
'Arguements: en es

//function borrowing

var person2 = {
firstname = {
	firstname: 'Jane',
	lastname: 'Doe'
}

console.log(person.getFullName.apply(person2))

OUTPUT:
Jane Doe

//FUNCTION CURRYING
this has to do with bind. 

function multiply(a,b) {
	return a * b;
	}
	
var multiplyByTwo = multiply.bind(this, 2);
//that means a will always be bound to 2

function multiply(a,b) {
	return a * b;
	}
	
var multiplyByTwo = multiply.bind(this, 2);
console.log(multiplyByTwo(4));

OUTPUT:
8

//even if try change it won't work 

function multiply(a,b) {
	return a * b;
	}
	
var multiplyByTwo = multiply.bind(this, 2);
//that means a will always be bound to 2

function multiply(a,b) {
	return a * b;
	}
	
var multiplyByTwo = multiply.bind(this, 2);
console.log(multiplyByTwo(3,7));

OUTPUT:
14
```

####CURRYING
Creating a copy of a function but with some preset parameters

very useful in mathematical situations

##FUNCTIONAL PROGRAMMING

Where we think and code in terms of functions.



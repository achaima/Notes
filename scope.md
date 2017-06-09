#SCOPES AND CLOSURES



##1. COMPILER

Every variable and function declaration gets declared. 

It gets stored in the scope engine.

* Variables start with ```var```

* Functions start with ```function```

Therefor a function expression like an IIFE(Immediatly Invoked Function Expression), in other words, a self-executing function is not stored in compile time because it has a parantheses before ```function```.

```js
FUNCTION EXPRESSION - 

var asma = function() {
	console.log("asma");
};

asma();


```

```js
IIFE -
 
(function Asma() {
	console.log("Hey there!");
    })();
	
//Hey there!
```

###HOISTING

* Hoisted is deciding what goes where.

* Functions will be placed before variables as they are **1st class citizens**


```js
function doSomething(a) {... var a};
function doSomethingElse(a) {...}
    
var b;

```
**Note:** ```var a``` is declared in function ```doSomething```, by putting it in the parameter you are implicitly creating a variable.



##2. RUN TIME

*AKA Code execution*

Javascript talks to the scope engine at the run time using:

1. LHS (Left Hand Side)
2. RHS (Right Hand Side)

* ```LHS``` - is when you look for an identifier for **assigning purposes**.

* ```RHS``` - is when you look for an identifier with the **purpose of using it.** 



```js 
function doSomething(a) {
    function doSomethingElse(a) {
        return a - 1;
    }

    var b;

    b = a + doSomethingElse( a * 2 );
    console.log(b * 3);
}

doSomething( 3 ); // 24

```

**Steps:**

```js
1. doSomething (3) 
	//doSomething parameter/variable a is assigned to 3 
```
```js
2. b = a + doSomethingElse( a * 2 );	
	// a is 3
```
```js
3. b = 3 + doSomethingElse (3 * 2);  
	//doSomethingElse(6) 
	//Now we got assigned a value time to execute the function
```
```js
4. function doSomthingElse(6) {
	return 6 - 1
	} 								  
	//doSomethingElse = 5;
```
```js
5. b = 8						  
	// a = 3 & doSomethingElse = 5
```
```js
6. console.log (b * 3) 			  
	// 24
```

**LHS** is only interested in values being assigned:

```js
a = 3; 
b = a + doSomethingElse( a * 2 );
```

RHS is interested in executing functions:

```js
doSomething(3)...;
doSomethingElse( a * 2);
console.log(b * 3);
```



**Conversation**

**Engine:** "Hey Scope, I have an RHS reference for ```doSomething```. Do we have it?"

**Scope:** "Yeah I checked with compiler and looks like that we do"

**Engine:** "I'm executing doSomething. Looks like I'm going to need your help Scope. I've got an LHS reference for ```a```, you got it?

**Scope:** "It will be in the doSomething. It implicitly declared a as a parameter so I just made one."

**Engine:** "I've got another RHS reference for ```doSomethingElse```"

**Scope:** "I checked with compiler again, wish he'd talk to you himself, you know? but anyway we got it"

**Engine:** "What about ```var b``` I have a RHS reference for it"

**Scope:** "Yep we got that. It's in function ```doSomething scope```"

**Engine:** "After all that works out and scope just so you know ```var a``` in ```doSomethingElse``` changes to ```6``` when we execute the function. Does that sound right?"

**Scope:** "It sure does, engine, it sure does."

**Engine:** Hey, Scope, sorry to bother you again. I need an RHS look-up for ```console```. Ever heard of it?

**Scope:** No problem, Engine, this is what I do all day. Yes, I've got ```console```. He's built-in. Here ya go.

**Engine:** Perfect. Looking up ```log(..)```. OK, great, it's a function.

**Engine:** Yo, Scope. Can you help me out with an RHS reference to ```b```. I think I remember it being ```8```, but just want to double-check.

**Scope:** You're right, Engine. Same guy, hasn't changed. Here ya go.


##Nested Scope

We said that Scope is a set of rules for looking up variables by their identifier name. There's usually more than one Scope to consider, however.

Just as a block or function is nested inside another block or function, scopes are nested inside other scopes. So, if a variable cannot be found in the immediate scope, Engine consults the next outer containing scope, continuing until found or until the outermost (aka, global) scope has been reached.


```js
function foo(a) {
	console.log( a + b );
}

var b = 2;

foo( 2 ); // 4
```

**Steps:**

```js
1. foo(2)
	//london(a) 
	//a is now assigned to 2
	//console.log ( 2 + b)'
```
```js
2. var b = 2;
	//console.log (2 + 2)
```

**Conversation:**

**Engine:** "Hey scope of london looks like I have an RHS reference ```+b``` for ``b``. You got that?"

**London Scope:** "Can't say I do."

**Engine:** "You going to have to ask around in scope land."

**Scope:** "I went out of  ```function foo``` to global scope and found ```b```. It's out there so it can go in london scope. Here ya go!"

**Engine:** "Cheers Scope"

___________________________

####Errors

```js
function foo(a) {
	console.log( a + b );
	b = a;
}

foo( 2 );

//ReferenceError b is not defined
```

* ```b``` is an undeclared variable when console.log is executed.

However,

```js
function foo(a) {
	b = a;
	console.log( a + b );
}

foo( 2 ); // 4
```
* Unless it's in strict mode scope will by default create a ```variable``` for ```b``` in the **global** scope. 

```js
function foo(a) {
	b = a;
	console.log( a + b );
}

foo( 2 ); // 4
console.log(a); // ReferenceError a is not defined
console.log(b); // 2
```





##Lexical scope




###Lex-time


* Lexical scope is scope that is defined at lexing time. 

* In other words, lexical scope is based on where variables and blocks of scope are authored, by you, **at write time**, and thus is (mostly) set in stone by the time the lexer processes your code.
 
 
Scenario 1 :

```js 
function foo(a) {
	var b = 2;

	// some code

	function bar() {
		// ...
	}

	// more code

	var c = 3;
}

bar(); // fails

console.log( a, b, c ); // all 3 fail
```
 The only way we can get a result for console.log is if we call the foo function and ensure all a, b and c are global variables. Seeing as we didn't declare it as a variable in either of the function scopes, global scope will create and store it. We don't really ever want to do this. (It's like kicking people out their houses and increasing the risk of them messing with others in and out of houses)

Scenario 2 :

```js 
function foo(a) {
	b = 2;

	// some code

	function bar() {
		// ...
	}

	// more code

	c = 3;
}

foo(1);
console.log( a, b, c ); // 1 , 2, 3
```

##Hiding In Plain Scope

The traditional way of thinking about functions is that you declare a function, and then add code inside it. But the inverse thinking is equally powerful and useful: take any arbitrary section of code you've written, and wrap a function declaration around it, which in effect "hides" the code. 

*(It''s more like you create a house around the household members rather than creating a house and then finding household members to add.)*

```js
function doSomething(a) {
	function doSomethingElse(a) {
		return a - 1;
	}

	var b;

	b = a + doSomethingElse( a * 2 );

	console.log( b * 3 );
}

doSomething( 2 ); // 15
```

###Collision Avoidance

```js
function foo() {
	function bar(a) {
		i = 3; // changing the `i` in the enclosing scope's for-loop
		console.log( a + i );
	}

	for (var i=0; i<10; i++) {
		bar( i * 2 ); // oops, infinite loop ahead!
	}
}

foo();
```

**Steps:**

```js
1. i = 3;
	//variable i is declared
```
```js
2. console.log (a + i)
```
```js
3. (var i=0; i<10; i++)
	//i to start from 0 till 10 to increment by 1 each loop
```
```js
4.  bar( i * 2 );
	//Each increment it should execute this
	// i = 3 in bar scope
	//HOWEVER i will always be i = 3 and so it will be stuck in an
	//endless loop as it overwrites the i in the for loop.
```
```js
5. foo(1);
	//foo
```

Alternatively, one way you could avoid that is by limiting the scope of ```i``` to function bar/foo.

```js
function foo() {
    function bar(a) {
        var i = 3; // changing the `i` in the enclosing scope's for-loop
        console.log( a + i );
		console.log(a, "a");
    }

    for (var i=0; i<10; i++) {
        bar( i * 2 ); 
    }
}

foo();

//Result:
3
0 "a"
5
2 "a"
7
4 "a"
9
etc...
```

**Steps:** 

```js
1. i = 3;
	//variable i is declared
```

```js
2. console.log (a + i)
	//a + 3
```
```js
3. (var i=0; i<10; i++)
	//i to start from 0 till 10 to increment by 1 each loop
```
```js
4.  bar( i * 2 );
	//Each increment it should execute this
	//so starting from i = 0 
	//bar (0) which is what bar(a) gets assigned to
```
```js
5. console.log(a + i);
	//console.log (0 + 3) 
	//1st loop should return 3 
```


**!!Important:** Scope look-up stops once it finds the first match.

Scope look-up always starts at the innermost scope being executed at the time, and works its way outward/upward until the first match, and stops. 


```js
function foo(a) {
	var b = a * 2;

	function bar(c) {
	console.log(a, b, c);
    }
		
    bar(b * 3);
}

foo( 2 ); // 2, 4, 12
```


* Bubble 1 encompasses the global scope, and has just one identifier in it: ```foo```

* Bubble 2 encompasses the scope of foo, which includes the three identifiers: ```a```, ```bar``` and ```b```

* Bubble 3 encompasses the scope of bar, and it includes just one identifier: ```c```

**Steps:**

```js
1. foo(2)
	//foo(2) is now assigned to foo(a)
	//a = 2
```
```js
2. var b = a * 2;
	//b = 2 * 2
	//b = 4
```

```js
3. bar(b * 3)
	//bar(4 * 3)
	//bar(12) is now assigned to bar(c)
```
```js
4. console.log(a, b, c);
	//2, 4, 12
```

##Cheating Lexical


If lexical scope is defined only by where a function is declared, which is entirely an author-time decision, how could there possibly be a way to "modify" (aka, cheat) lexical scope at run-time?

JavaScript has two such mechanisms. Both of them are equally frowned-upon in the wider community as bad practices to use in your code. But the typical arguments against them are often missing the most important point: cheating lexical scope leads to poorer performance.

1. eval(..)
2. with(){}

###Eval

The eval(..) function in JavaScript takes a string as an argument, and treats the contents of the string as if it had actually been authored code at that point in the program. In other words, you can programmatically generate code inside of your authored code, and run the generated code as if it had been there at author time.

```js
function foo(str, a) {
	eval( str ); // cheating!
	console.log( a, b );
}

var b = 2;

foo( "var b = 3;", 1 ); // 1 3
```

**Steps:**

```js
1. foo( "var b = 3;", 1 );
	// This is then equivalant to foo(str, a)
	// str = "var b = 3;"
	// a = 1;
```
```js
2. eval ( str );
	/*eval(..) function in JavaScript takes a string as an 	argument, and treats the contents of the string as if it 	had actually been authored code at that point in the 	program.*/
	//b = 3 
	//var b = 2 is a global variable as it's outside 
	//function foo and now there is a local variable b
	//inserted into function foo so it will take that
	//because that is the first match that is found.
```
```js
3. console.log(a, b);
	// 1 3
```

setTimeout(..) and setInterval(..) can take a string for their respective first argument, the contents of which are evaluated as the code of a dynamically-generated function. This is old, legacy behavior and long-since deprecated. **Don't do it!**


##with
**with** is typically explained as a short-hand for making multiple property references against an object without repeating the object reference itself each time.

```js
For example:

var obj = {
	a: 1,
	b: 2,
	c: 3
};

// more "tedious" to repeat "obj"
obj.a = 2;
obj.b = 3;
obj.c = 4;

// "easier" short-hand
with (obj) {
	a = 3;
	b = 4;
	c = 5;
}
```

However, there's much more going on here than just a convenient short-hand for object property access. Consider:

```js
function foo(obj) {
	with (obj) {
		a = 2;
	}
}

var o1 = {
	a: 3
};

var o2 = {
	b: 3
};

foo( o1 );
console.log( o1.a ); // 2

foo( o2 );
console.log( o2.a ); // undefined
console.log( a ); // 2 -- Oops, leaked global!
```

**Steps:**

```js
1. foo(o1);
	//foo(o1) o1 object is now assigned to as foo(obj)
```
```js
2. console.log (o1.a)
	//with(o1) { a = 2; } this variable a overwrites 01.a = 3
	// a = 2;
```
```js
3. foo( o2 ); 
	//foo(o2) o2 object is now assigned to as foo(obj)
```
```js
4. console.log( o2.a );
	// it is undefined because object o2 has no var a, neither does function foo or the global object. 
	//Only closed scopes 01() and with()
```
```js
5. console.log( a )
	// var a is undeclared making it a global variable
```
The with statement takes an object, one which has zero or more properties, and treats that object as if it is a wholly separate lexical scope, and thus the object's properties are treated as lexically defined identifiers in that "scope".

**Note:** Even though a with block treats an object like a lexical scope, a normal var declaration inside that ```with``` block will not be scoped to that with block, but instead the containing function scope.

It is a strange sort of mind-bending thought to see with turning, at runtime, an object and its properties into a "scope" with "identifiers". But that is the clearest explanation I can give for the results we see.

**Note:** In addition to being a bad idea to use, both eval(..) and with are affected (restricted) by ***"strict mode"***. with is outright disallowed, whereas various forms of indirect or unsafe eval(..) are disallowed while retaining the core functionality.

Both eval(..) and with cheat the otherwise author-time defined lexical scope by modifying or creating new lexical scope at runtime. These stop the optimisations and make your code run slower.

##Chapter 3: Function vs. Block Scope


##Functions As Scopes

We've seen that we can take any snippet of code and wrap a function around it, and that effectively "hides" any enclosed variable or function declarations from the outside scope inside that function's inner scope.

For example:

```js
var a = 2;

function foo() { // <-- insert this

	var a = 3;
	console.log( a ); // 3

} // <-- and this
foo(); // <-- and this

console.log( a ); // 2
```
While this technique "works", it is not necessarily very ideal. There are a few problems it introduces:

1. The first is that we have to declare a named-function foo(), which means that the identifier name foo itself **"pollutes" the enclosing scope** (global, in this case).

2. We also **have to explicitly call the function by name**(foo()) so that the wrapped code actually executes.

It would be more ideal if the function didn't need a name (or, rather, the name didn't pollute the enclosing scope), and if the function could automatically be executed.

Like this, IIFE

```js
var a = 2;

(function foo(){ 
	var a = 3;
	console.log( a ); // 3

})(); 

console.log( a ); // 2
```

 * Instead of treating the function as a standard declaration, the function is treated as a function-expression.
 
*  Hiding the name foo inside itself means it does not pollute the enclosing scope unnecessarily.
 
* This is because it will be ignored in compile time as it is neither a variable or function declaration. It will just be there waiting to be executed at run time.
 
###Anonymous vs. Named
 
You are probably most familiar with function expressions as callback parameters, such as:

```js
setTimeout( function(){
	console.log("I waited 1 second!");
}, 1000 );
```

This is called an **"anonymous function expression"**, because function()... has no name identifier on it. Function expressions can be anonymous, but function declarations cannot exclude the name -- that would be illegal JS grammar.

Anonymous function expressions are quick and easy to type, and many libraries and tools tend to encourage this idiomatic style of code. However, they have several draw-backs to consider:

1. **Difficulty debugging** - Anonymous functions have no useful name to display in stack traces.

2. **Function cannot refer to itself** - Without a name, if the function needs to refer to itself, for recursion, etc., the deprecated arguments.callee reference is unfortunately required. Another example of needing to self-reference is when an event handler function wants to unbind itself after it fires.

3. **Self-documented code helps** - Anonymous functions omit a name that is often helpful in providing more readable/understandable code.

**Inline function expressions** are powerful and useful -- the question of anonymous vs. named doesn't detract from that. Providing a name for your function expression quite effectively addresses all these draw-backs, but has no tangible downsides. The **best practice** is to always name your function expressions:

```js
setTimeout( function timeoutHandler(){ // <-- Look, I have a name!
	console.log( "I waited 1 second!" );
}, 1000 );
```

While certainly less common, naming an IIFE has all the aforementioned benefits over anonymous function expressions, so it's a good practice to adopt.



##Blocks As Scopes

But even if you've never written a single line of code in block-scoped fashion, you are still probably familiar with this extremely common idiom in JavaScript:

```js
for (var i=0; i<10; i++) {
	console.log( i );
}
```

We declare the variable ```i``` directly inside the for-loop head, most likely because our intent is to use ```i``` only within the context of that ```for-loop```, and essentially ignore the fact that the variable actually scopes itself to the enclosing scope (function or global).

That's what block-scoping is all about. **Declaring variables as close as possible, as local as possible, to where they will be used.**

##Closures 
*Reference Javascript Closure tutorial(closures explained)*

* In javascript variables that are defined outside the function are automatically available inside the function because of lexical scoping.
 
* Anthing defined inside cannot be accessed outside but everything outside can be accessed inside.

```js
var passed = 3;

var addTo = function() {
var inner = 2;
return passed + inner;
};

console.dir(addTo);

/*Note in the console you will see the following:
> function addTo()
 > function scope
  > Closure
 	> passed: 3
  > Global: Window
*/
```

```js
var passed = 3;

var addTo = function() {
var inner = 2;
return passed + inner;
};

console.dir(addTo);

//passed: 4

```

```js
var addTo = function(passed){

	var add = function(inner) {
	return passed + inner;
	};
	
	return add;
};

console.dir(adddTo(3));
// passed: 3

var addThree = new addTo(3);
var addFour = new addTo(4);

console.dir(addThree); //passed: 3
console.dir(addFour) // passed: 4

```

```js
var addTo = function(passed){

	var add = function(inner) {
	return passed + inner;
	};
	
	return add;
};

console.dir(adddTo(3));
// passed: 3

var addThree = new addTo(3);
var addFour = new addTo(4);

console.log(addThree(1)); // 4
console.log(addFour(1)); // 5

```

* Closures are nothing but functions with preserved data.

Just to really hammer it home.

A closure is an inner function that has access to the outer (enclosing) function's variables—scope chain. The closure has **3 scope chains**: 

1. Access to its own scope (variables defined between its curly brackets)

2. Access to the outer function's variables

3. Access to the global variables. 


* Closure retains state and scope after it executes

```js
$(document).ready(function(){ 
	var a = 1;
	
  $('button').onClick('click', function(){
  	alert(a);
  	});
  	
  });
  
// Function runs when the document is ready
// Sets up the onClick listener so that this will be the only function running when the button is clicked
// Problem is this whole function has run and is finished but we don't want 'a' to disappear
// Function alert 'a' needs to access it
//JS notices it reference something in a different scope and so it keeps 'a' alive

```

 On a tangent ...
 
```js

 function celebrityID () {
    var celebrityID = 999;
    // We are returning an object with some inner functions​
    // All the inner functions have access to the outer function's variables​
    return {
        getID: function ()  {
            // This inner function will return the UPDATED celebrityID variable​
            // It will return the current value of celebrityID, even after the changeTheID function changes it​
          return celebrityID;
        },
        setID: function (theNewID)  {
            // This inner function will change the outer function's variable anytime​
            celebrityID = theNewID;
        }
    }
​
}
​
​var mjID = celebrityID (); // At this juncture, the celebrityID outer function has returned.​
mjID.getID(); // 999​
mjID.setID(567); // Changes the outer function's variable​
mjID.getID(); // 567: It returns the updated celebrityId variable

//Now my confusion here was how is it accessing get and setID outside the function scope. 
//This really boggled my mind and went against everything I read. 
//However, I was wrong in my approach. 

//celebrityID is a function and the variables and functions are private to that scope 
//However to access it you must assign the function to a variable
//In the process the function gets declared and returns an object
//That object is stored to the variable and now you are free to access the properties (using dot or bracket notation) which is:
//1. getID
//2. setID

//You can see the object returned for yourself by console.log('mjID');

/*Note: you still can't access var celebrityID = 999; as that is a private variable that is not being 'returned' and so is not part of the object.

You can access the variable celebrityID,
but only using the methods inside the function as they have special access to it
since they are all inside function celebrityID()*/

```


FOR LOOPS - DECONSTRUCTED

```js
var counter = (function() {
var i = 0; 

return {
	get: function() {
		return i;
    },
	set: function(val) {
		i = val;
    },
	increment:function() {
		i++;
    }
 }

})();

console.log(counter.get()); //0
counter.set(5); // 5
counter.increment();
console.log(counter.get()); //6

//or if you change set to 'i <= 5'
console.log(counter.get()); //0
counter.set(5); // 0
counter.increment();
console.log(counter.get()); //1


//i starts from 0 
//as long as it less than the value(5) it will move onto increment function and increment
//so thats why with the IIFE previously the output was 6 because unlike

```

##Learn the difference between var, let and const

```VAR``` - The scope of a variable defined with var is **function scope** or **declared outside any function, global**.

```LET``` - The scope of a variable defined with let is **block scope**.

```CONST``` - The scope of a const defined with const is **block scope**. A const identifier **cannot be reassigned.**


```js
VAR: 

  function blocky() {
  if(true) {
   	  var something = 'something';
   }
   console.log(something);
 } 
 
 blocky();
 //something
 
```

```js
LET:

function blocky() {
  if(true) {
   	  let something = 'something';
   }
   console.log(something);
 } 
 
 blocky();
 //Uncaught ReferenceError: something is not defined
 
 
 
 function blocky() {
 	   let something='something';
  if(true) {
   	  let something = 'something2';
   	  console.log(something);
   }
   console.log(something);
 } 
 
 blocky();
 //something2
 //something
```

```js 
 CONST:
 
  function blocky() {
 	    const something='something';
  if(true) {
   	  let something = 'something2';
   	  console.log(something);
   }
   something = 'something new';
   console.log(something);
 } 
 
 blocky();
 
 //something2
 //Uncaught TypeError: Assignment to constant variable
```

continue.... module patterns






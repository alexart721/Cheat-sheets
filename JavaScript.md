# JavaScript
A scripting language that allows you to create dynamically updating content.
JavaScript is interpreted, meaning it is run from top to bottom, as though being read.
JavaScript is object-oriented, "everything is an object" (except primitives) and has methods.

Table of Contents:

[TOC]

------

## Internal vs External JavaScript

  - Internal - JavaScript is inserted into the HTML file, ideally in the <head></head>, between <script></script> tags
  - External - JavaScript is written in external files and imported into the HTML, again inside <script></script> tags in the head
  - Inline JavaScript - a form of internal JavaScript that involves writing JavaScript inside the HTML body without script tags. While it works, it is considered bad practice.

------

## Script Loading Strategies

Since JavaScript is interpreted and the head of the HTML is loaded before the body is parsed, the code may not function as intended. You can use:

```javascript
 document.addEventListener('DOMContentLoaded', function() {...})
```

to ensure the HTML body is completely loaded and parsed for code inside the function.
For external scripts, you can use the 'async' and 'defer' attributes (see below). If no attribute is specified, when the HTML parser reaches the script tag, the HTML rendering is blocked until the script is fetched and executed. If the async attribute is added, the script will be downloaded without blocking the page render. Once the download is complete, the script will execute. While the script is executing the page is blocked from rendering. There are no guarantees that scripts will run in any specific order, so only use async when scripts don't depend on other scripts. For example:

```html
<script async src="js/vendor/jquery.js"></script>
<script async src="js/script2.js"></script>
<script async src="js/script3.js"></script>
```

jquery.js may load before or after script2.js and script3.js

For the defer attribute, the script will be downloaded without blocking the page render (as with async) but won't run until the page content has completely loaded.

```html
<script defer src="js/vendor/jquery.js"></script>
<script defer src="js/script2.js"></script>
<script defer src="js/script3.js"></script>
```

The above scripts will run in order after all page content has loaded. Generally, use the defer attribute.

------

## Primitives

A primitive is data that is not an object and has no methods. JavaScript has 7 primitive data types:
  - string (typeof instance === 'string')
  - number (typeof instance === 'number')
  - bigint (typeof instance === 'bigint')
  - boolean (typeof instance === 'boolean')
  - undefined (typeof instance === 'undefined')
  - symbol (typeof instance === 'symbol')
  - null (structural root primitive, typeof instance === 'object')

All primitives are immutable (cannot be altered). A variable can be assigned to a new value, but the existing primitive does not change (4 will always be 4).
Except for null and undefined, all primitives have object equivalents that wrap around the primitive values:
  - String for the string primitive
  - Number for the number primitive
  - BigInt for the bigint primitive
  - Boolean for the boolean primitive
  - Symbol for the symbol primitive

The wrapper's valueOf() method returns the primitive value.

------

## Decimals in JavaScript

Because all numbers in JavaScript are IEEE 75 floating point numbers, some decimal numbers cannot be represented with perfect accuracy. To avoid issues with unexpected trailing decimals, you can either convert the number to a string and then back to a number:

```javascript
function calcTax (base) {
    return parseFloat((base * 0.2).toFixed(2));
}
```

or you can work in integers to the precision that you want:

```javascript
function calcTax (base) {
    const padding = 100; // For 2 decimal precision
    base = base * padding;
    return Math.round(base * 0.2) / padding;
}
```

------

## Objects

A data structure that can have properties and methods.
Objects are stored by reference, and equality comparisons are done by reference, not by content.
const does not prevent mutation, only reassignment.

```javascript
const obj1 = {a: 1, b: 2};
let obj2 = {a: 1, b: 2};

console.log(obj1 === obj2); // false
```

If you have two variables pointing to the same object and mutate the object through one variable the other will update as well:

```javascript
obj2 = obj1;

obj1.c = 3; // This is allowed despite obj1 being declared with const since obj1 is not being reassigned, only mutated
obj2.d = 4;

console.log(obj1); // {a: 1, b: 2, c: 3, d: 4}
console.log(obj2); // {a: 1, b: 2, c: 3, d: 4}
```

Reassigning a variable does not mutate the previously referenced object:

```javascript
obj2 = { ...obj2, a: 3};

console.log(obj1); // {a: 1, b: 2, c: 3, d: 4}
console.log(obj2); // {a: 3, b: 2, c: 3, d: 4}
```

------

## Useful Built-in Objects

  - Set
    - Allows for storage of unique values of any type, whether primitive values or object references
    - A value in a set may occur only once
    - The has method (Set.prototype.has) typically performs faster than Array.prototype.includes when Array.length is equal to Set.size
  - Map
    - Holds key-value pairs and remembers insertion order of the keys
    - Different from Objects in a few ways:
      - Map does not contain default keys
      - Map's keys can be anything (including functions and objects), Object keys must be a string or a symbol
      - Map has a size property to easily retrieve number of items, Object size must be determined manually
      - Map performs better in scenarios involving frequent additions and removals of key-value pairs
      - Must use .set method to add items in a Map

------

## Class Patterns

Functional with shared methods

```javascript
// Class

function Phone (number) {
  var result = {};
  result.number = number;
  Object.assign(result, phoneMethods);
  return result;
};

var phoneMethods = {};
phoneMethods.dial = function (number) {
  console.log('Dialing', number);
};

var motorola = Phone(695323871);

// Sub-class

function SmartPhone (number, email) {
  var result = Phone(number);
  result.email = email;
  Object.assign(result, smartPhoneMethods);
  return result;
};

var smartPhoneMethods = {};
smartPhoneMethods.sendEmail = function (email) {
  console.log('Emailing', email);
};

var iPhone = SmartPhone(642503917, 'jack@apple.com');
```

Pseudo-classical:

```javascript
// Class

function Phone (number) {
  // var this = Object.create(Phone.prototype);
  this.number = number;
  // return this;
};

Phone.prototype.dial = function (number) {
  console.log('Dialing', number);
};

// Initialized variables must use 'new' keyword to turn the function into a constructor
var motorola = new Phone(695323871);

// Sub-class

function SmartPhone (number, email) {
  // var this = Object.create(SmartPhone.prototype);
  Phone.call(this, number);
  this.email = email;
  // return this;
};

SmartPhone.prototype = Object.create(Phone.prototype);
SmartPhone.prototype.constructor = SmartPhone;
SmartPhone.prototype.sendEmail = function (email) {
  console.log('Emailing', email);
};

var iPhone = new SmartPhone(642503917, 'jack@apple.com');
```

ES6 Classes:

```javascript
// Class

class Phone {
  constructor (number) {
    this.number = number;
  }
  dial (number) {
    console.log('Dialing', number);
  }
}

// Sub-class

class SmartPhone extends Phone {
  constructor (number, email) {
    super(number);
    this.email = email;
  }
  sendEmail (email) {
    console.log('Emailing', email);
  }
}
```

------

## Variables

Can be declared using 'var', 'let', and 'const':
  - 'var' was the original keyword, and has some properties that cause issues:
    - Variables can be declared AFTER they are initialized and it will still work because var is hoisted:

      ```javascript
      myName = 'Chris';

      function logName() {
          console.log(myName);
      }

      logName();

      var myName;
      ```

    - The same variable can be declared as many times as you like:

      ```javascript
      var myName = 'Chris';
      var myName = 'Bob';
      ```

    - 'var' is function scoped, so, outside a function it is global, which can cause problems. In the below example with a for loop using 'var', though the outputs will occur one second apart from each other, as intended, the setTimeouts are implemented by the for loop far before they start executing. Therefore, i will be 3 because it was declared using var and is globally scoped since a for loop is not a function.

      ```javascript
      for (var i = 0; i < 3; i++) {
          setTimeout(() => {
              console.log(i);
          }, i * 1000);
      }
      // Output:
      // 3
      // (one second delay)
      // 3
      // (one second delay, two total seconds)
      // 3
      
      // Since let is block scoped, each increment of i will be logged as expected
      for (let i = 0; i < 3; i++) {
          setTimeout(() => {
              console.log(i);
          }, i * 1000);
      }
      // Output:
      // 0
      // (one second delay)
      // 1
      // (one second delay, two total seconds)
      // 2
      ```

  - 'let' addresses these issues and is preferred in modern JavaScript
    - Variables have to be declared before or at the same time that they are initialized
    - Variables can only be declared once, but can be reassigned
    - 'let' is block scoped (any code inside curly braces)

  - 'const' variables cannot be reassigned

------

## Spread and Rest Syntax

Spread syntax allows an iterable, such as an array or string, or an object to be expanded. Useful for copying arrays and objects as well as passing array/object elements to a function.

```javascript
// Copy all elements of an array
let numberStore = [0, 1, 2];
let newNumber = 12;
numberStore = [...numberStore, newNumber]; // [0, 1, 2, 12]

let parts = ['shoulders', 'knees'];
let lyrics = ['head', ...parts, 'and', 'toes']; //  ["head", "shoulders", "knees", "and", "toes"]

// Copy an object
let objClone = { ...obj };
let obj1 = { foo: 'bar', x: 42 };
let obj2 = { foo: 'baz', y: 13 };

let clonedObj = { ...obj1 }; // { foo: "bar", x: 42 }
let mergedObj = { ...obj1, ...obj2 }; // { foo: "baz", x: 42, y: 13 }

// Pass elements to a function
function sum(x, y, z) {
    return x + y + z;
}
const numbers = [1, 2, 3];

console.log(sum(...numbers)); // expected output: 6

// Spread syntax can be used multiple times
function myFunction(v, w, x, y, z) { }
let args = [0, 1];
myFunction(-1, ...args, 2, ...[3]);
```

Spread syntax only goes one level deep while copying, and may be unsuitable for copying multidimensional arrays and objects:

```javascript
let a = [[1], [2], [3]];
let b = [...a];

b.shift().shift(); //  1

//  Oh no!  Now array 'a' is affected as well:
a //  [[], [2], [3]]
```

Rest syntax, though it looks exactly like Spread syntax, behaves in the opposite fashion. Rest syntax collects multiple elements and places them into a single element. Rest syntax is used in the function definition/expression while spread syntax, when used with functions, is used in the invocation.

```javascript
function myFun(a,  b, ...manyMoreArgs) {
  console.log("a", a)
  console.log("b", b)
  console.log("manyMoreArgs", manyMoreArgs)
}

myFun("one", "two", "three", "four", "five", "six");

// Console Output:
// a, one
// b, two
// manyMoreArgs, ["three", "four", "five", "six"]

```

Unlike the spread operator, the rest operator can only be used once and must be the last parameter in the function definition:

```javascript
// There can be only one rest parameter
function foo(...one, ...wrong, ...wrong) {}

// The rest parameter must be last
function foo(...wrong, arg2, arg3) {}
function foo(arg1, arg2, ...correct) {}
```

------

## Functions

Performs a task or calculates a value.

Things passed to a function are called 'parameters'. Primitive parameters are passed by value, object parameters are passed by reference.



### Pure Functions

'Pure functions' have the following properties:

  - Given the same input, will always return the same output
  - Produces no side effects
    - Does not mutate the external state, for example



### Function Declarations

A function declaration (also definition or statement) has the following form:

```javascript
function square(number) {
  return number * number;
}
```

Function declarations are hoisted and may be called before they are defined (though this is now considered bad practice).



### Function Expressions

A function expression has the following form:

```javascript
const square = function (number) {
  return number * number;
}
```



### Arrow Functions

or, using ES6 arrow notation:

```javascript
const square = (number) => {
  return number * number;
}
```

With ES6, the above can be further simplified:

```javascript
const square = number => number * number;
```

Since there is only one parameter being passed, the parentheses can be omitted, and since there is only one return line the expression can be moved to the declaration line and the return becomes implicit. Note that if you put curly braces around the above expression JavaScript will interpret this as returning an object. Arrow functions do not have their own `this` or `arguments` and are always anonymous (would need to be assigned to a variable to be reused). Arrow functions take their context from the one immediately above at execution time.



### Function Scope and this

Variables defined inside a function cannot be accessed outside the function. Functions can, however, access variables defined inside the scope in which it was defined. In other words, a function defined in the global scope can access all variables defined in the global scope. A function defined inside another function can also access all variables defined in its parent function, and any other variables to which the parent function has access.

```javascript
// The following variables are defined in the global scope
var num1 = 20,
    num2 = 3,
    name = 'Chamakh';

// This function is defined in the global scope and has access to the above
function multiply() {
  return num1 * num2;
}

multiply(); // Returns 60

// A nested function example
function getScore() {
  var num1 = 2,
      num2 = 3;

  function add() {
    return name + ' scored ' + (num1 + num2);
  }

  return add(); // Returns invoked add function
}

getScore(); // Returns "Chamakh scored 5"
```

As an object method, the context generally refers to the object:

```javascript
const america = {
  countryName: 'The United States of America',
  yearFounded: 1776,

  describe() {
    console.log(`${this.countryName} was founded in ${this.yearFounded}.`)
  },
};

america.describe(); // 'The United States of America was founded in 1776.'

```

In the above case, the context at invocation is the object `america` (typically remembered as 'the thing to the left of the dot at execution'). Since the function takes its context at execution (invocation) time, be wary of the following:

```javascript
const func = america.describe;

func(); // 'undefined was founded in undefined.'
```

The context at invocation can be leveraged with methods in additional ways:

```javascript
function methodize (func) {
    return function (b) {
        return func(this, b); // pass the attached context as a parameter
    }
}

function add (a, b) {
    return a + b;
}
Number.prototype.add = methodize(add); // Now the add method just awaits the parameter 'b'
(3).add(5); // Returns 8, 3 is passed in the place of the parameter 'this' (to the left of the dot)
```

Also, be aware that the callback of a setInterval function is the global context:

```javascript
const book = {
  currentPage: 1,
  readPage: function() {
    setInterval(function() {
      this.currentPage += 1;
      console.log(this.currentPage);
    }, 1000);
  }
}
book.readPage(); // NaN... NaN... Nan...
```

This can be fixed by using an arrow function that takes its context from the context immediately above it:

```javascript
const book = {
  currentPage: 1,
  readPage: function() {
    setInterval(() => {
      this.currentPage += 1;
      console.log(this.currentPage);
    }, 1000);
  }
}
book.readPage(); // 1... 2... 3...
```



### Apply, Call, and Bind

In the above examples, `this` was determined by context, however you can explicitly set the context using the apply, call, or bind method. Both apply and call invoke a function with a passed `this` context and arguments. If no context is provided, the global context is assumed. The main difference between apply and call is that apply takes the arguments as an array while call takes the arguments one by one.

```javascript
const book = {
  title: 'Brave New World',
  author: 'Aldous Huxley',
};

function summary() {
  console.log(`${this.title} was written by ${this.author}.`)
}

summary(); // 'undefined was written by undefined.'

summary.call(book); // 'Brave New World was written by Aldous Huxley.'

function longerSummary(genre, year) {
  console.log(
    `${this.title} was written by ${this.author}. It is a ${genre} novel written in ${year}.`,
  )
}

longerSummary.call(book, 'dystopian', 1932);
// 'Brave New World was written by Aldous Huxley. It is a dystopian novel written in 1932.'
longerSummary.apply(book, ['dystopian', 1932]);
// 'Brave New World was written by Aldous Huxley. It is a dystopian novel written in 1932.'
```

The bind method creates a new function that, when called, has its `this` keyword set to the provided value.

```javascript
const braveNewWorldSummary = summary.bind(book);

braveNewWorldSummary(); // 'Brave New World was written by Aldous Huxley.'
```

Attempting to re-bind `braveNewWorldSummary` will fail:

```javascript
const book2 = {
  title: '1984',
  author: 'George Orwell',
};

braveNewWorldSummary.bind(book2);

braveNewWorldSummary(); // 'Brave New World was written by Aldous Huxley.'
```



### Recursion

A function can call itself, which is called 'recursion':

```javascript
const foo = function bar() {
    // The following are equivalent forms of recursion for this function:
    bar();
    arguments.callee();
    foo();
}
```

Recursion uses a function stack:

```javascript
function foo(i) {
  if (i < 0)
    return;
  console.log('begin: ' + i);
  foo(i - 1);
  console.log('end: ' + i);
}
foo(3);

// Output:

// begin: 3
// begin: 2
// begin: 1
// begin: 0
// end: 0
// end: 1
// end: 2
// end: 3

```



### Nested Functions and Closures

Nested functions (a function within a function) form a closure. A closure is an expression (most commonly, a function) that can have free variables together with an environment that binds those variables (that "closes" the expression). This means that the nested function contains the scope of the outer function. In summary:

- The inner function can be accessed only from statements in the outer function
- The inner function can use the arguments and variables of the outer function, while the outer function cannot use the arguments and variables of the inner function

As an example:

```javascript
function outside(x) {
  function inside(y) {
    return x + y;
  }
  return inside;
}
fn_inside = outside(3); // Returns the function waiting for another invocation

result = fn_inside(5); // returns 8

result1 = outside(3)(5); // returns 8
```

x is preserved when inside is returned. Since each call provides potentially different arguments, a new closure is created for each call to `outside`. The memory can be freed only when the returned `inside` is no longer accessible.



### Function arguments object

You can access the parameters passed to a function by using the array-like `arguments` object. The `arguments` object has a length property and individual arguments can be accessed by `arguments[i]`. As an example:

```javascript
function myConcat(separator) {
   var result = ''; // initialize list
   var i;
   // iterate through arguments passed in addition to separator
   for (i = 1; i < arguments.length; i++) {
      result += arguments[i] + separator;
   }
   return result;
}

// returns "red, orange, blue, "
myConcat(', ', 'red', 'orange', 'blue');
```

Alternatively, you can use the rest parameters syntax as described above to capture additional parameters:

```javascript
function multiply(multiplier, ...theArgs) {
  return theArgs.map(x => multiplier * x);
}

var arr = multiply(2, 1, 2, 3);
console.log(arr); // [2, 4, 6]
```

Differences between the rest parameters and the arguments object:

- The arguments object is not a real array, while rest parameters are Array instances
- The arguments object has additional functionality specific to itself
- The arguments object contains all parameters while the rest parameters array does not contain any arguments defined before it



### Default Parameter Values

Parameters can also have default values, as shown for the parameter b below:

```javascript
function multiply(a, b = 1) {
  return a * b;
}

multiply(5); // 5
multiply(5, 2); // 10
```

------

## Promises and Asynchronous Functions


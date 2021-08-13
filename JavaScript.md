# JavaScript
A scripting language that allows you to create dynamically updating content.
JavaScript is interpreted, meaning it is run from top to bottom, as though being read.
JavaScript is object-oriented, "everything is an object" (except primitives) and has methods.


## Internal vs External JavaScript
  - Internal - JavaScript is inserted into the HTML file, ideally in the <head></head>, between <script></script> tags
  - External - JavaScript is written in external files and imported into the HTML, again inside <script></script> tags in the head
  - Inline JavaScript - a form of internal JavaScript that involves writing JavaScript inside the HTML body without script tags. While it works, it is considered bad practice.


## Script Loading Strategies
Since JavaScript is interpreted and the head of the HTML is loaded before the body is parsed, the code may not function as intended.
You can use document.addEventListener('DOMContentLoaded', function() {...}) to ensure the HTML body is completely loaded and parsed for code inside the function.
For external scripts, you can use the 'async' and 'defer' attributes (e.g., <script src="script.js" defer></script>).
For the async attribute, the script will be downloaded without blocking the page while the script is being fetched. Once the download is complete, the script will execute.
While the script is executing the page is blocked from rendering. There are no guarantees that scripts will run in any specific order, so only use when scripts don't depend on other scripts.
For example:

<script async src="js/vendor/jquery.js"></script>
<script async src="js/script2.js"></script>
<script async src="js/script3.js"></script>

jquery.js may load before or after script2.js and script3.js

For the defer attribute, the script won't run until the page content has completely loaded.

<script defer src="js/vendor/jquery.js"></script>
<script defer src="js/script2.js"></script>
<script defer src="js/script3.js"></script>

The above scripts will run in order after all page content has loaded.


# Variables
Can be decalred using 'var', 'let', and 'const':
  - 'var' was the original keyword, and has some properties that cause issues:
    - Variables can be declared AFTER they are initialized and it will still work because var is hoisted:

      myName = 'Chris';

      function logName() {
        console.log(myName);
      }

      logName();

      var myName;

    - The same variable can be declared as many times as you like:

      var myName = 'Chris';
      var myName = 'Bob';

  - 'let' addresses these issues and is preferred in modern JavaScript
    - Variables have to be declared before or at the same time that they are initialized
    - Variables can only be declared once, but can be reassigned

  - 'const' variables cannot be reassigned


# Primitives
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


# Useful Built-in Objects
  - Set
  - Map
  - Promise
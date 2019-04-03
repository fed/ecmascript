class: center, middle, inverse

# EcmaScript Latest Features

---

# Agenda

1.

---

Reviewing most of ES6 language features through examples and common use cases.

---

# Good Read

http://es6-features.org/

---

Multiple names: ES6 = ES2015 = Harmony?. ES6 sounds more familiar as previous releases were ES5 and ES3. However the year-y version of the name preferred as they want to encourage yearly releases at some point.

---

class: center, middle, inverse

# let, const and the new block scope

---

Keyword Scope Comments
Global scope. Variables get attached to the global window object.
Function scope. 
let Block Hoisted, although cannot be accessed before getting declared. More on this here.
const
Can’t be reassigned, arrays and objects can be mutated though as it’s only the reference to those what cannot be changed.

---

* const does not imply any kind of immutability of the value itself, it only implies immutability of the binding.
* From a compiler optimisation point of view, it's already really really easy to identify a binding and figure out if it's never reassigned. All of the relevant optimizations are already possible without const ever being a thing.
* A matter of communication: there are lots of bindings that we happen to never change that we're totally fine if they do change. In fact, that's probably true for most bindings. It's not something your code will generally care about. It's probably a good idea to use const to communicate that you really don't intend for something to be changed.
* Avoid using let at the top level scope: if you need to use let at the top-level scope, that's a sign you have some sort of *global singleton* state which could cause you problems.

---

# Hoisting

When using var declarations get hoisted, while assignments don’t (temporal dead zone). Unlike what happens with var, variables declared with let and constants can’t be accessed before they get declared.

---

```js
console.log(username); // undefined

var username = 'homer.simpson';

console.log(username); // homer.simpson
console.log(email); // Uncaught ReferenceError: email is not defined
let email = 'homer.simpson@springfield.com';
```

---

Redeclaring variables: variables declared using var can be redeclared, even reusing their previous value. This is not true for variables declared using let.

```js
var age = 20;
var age = 20 * 1.5;
console.log(age); // 30

let name = 'John Doe';
let name = 'Someone else'; // Uncaught SyntaxError: Identifier 'name' has already been declared
```

---

Note that we can now move from IIFEs to blocks as we’ve got a new scope other than global and function:

---

```js
var country = 'Ireland';

// Function scope, we are not overriding the global country variable
(function () {
    var country = 'Australia';
})();

// Block scope with let/const, we have no name collisions either
{
    const country = 'New Zealand';
}

console.log(country); // still Ireland
```

---

class: center, middle, inverse

# Fat arrow functions (lambdas)

---

Are always anonymous (cannot be named)

```js
const doSth = (args) => 'value';
```

Equivalent to:

```js
const doSth = function (args) { return 'value'; }
```

---

We don’t need to wrap arguments in parenthesis if there’s only one:

```js
const doubles = [1, 2, 3].map(number => number * 2);
```

---

Arrow functions are automatically bound to their lexical scope.

```js
function asyncGreeting() {
  this.greeting = `G'day mate`;

  setTimeout(function () {
    console.log(this.greeting);
  }.bind(this)); // If we don't bind it here, it will log undefined

  setTimeout(() => {
    console.log(this.greeting); // This works just fine w/o binding
  });
}
```

---

If we don’t wrap our function body in curly braces, it means we’ve got an implicit return statement. Also, if we want to return an object, we need to wrap it in parenthesis.

```js
const numbers = [1, 2, 3];
const doubles = numbers
    .map((number) => 2 * number)
    .map((number) => ({ value: number });
```

---

Arrow functions cannot be used as function constructors, we can‘t instantiate them using the new operator as these are completely anonymous and dependent on their surrounding context.

```js
const Dog = (name) => {
  this.name = name;
};

// throws TypeError: Dog is not a constructor
const chubbs = new Dog('Chubbs');
```

---

class: center, middle, inverse

# Default arguments

---

```js
function greeter(username = 'fknussel') {
  return `Howdy ${username}`;
}
```

---

We can even make use of this feature to mark parameters as required:

```js
function getUserDetails(username = requiredParam('username')) {
  // Somehow return user details here...
}

function requiredParam(param) {
  throw new Error(`Missing parameter: ${param}`);
}

try {
  const userDetails = getUserDetails();
} catch (error) {
  console.error(error.message);
}
```

---

class: center, middle, inverse

# Rest parameters

---

If the last named argument of a function is prefixed with ..., it becomes an array whose elements are supplied by the actual arguments passed to the function.

```js
savePreferredLanguages('English', 'Spanish', 'Italian');
function savePreferredLanguages(primary, ...others) {
  console.log('primary', primary); // English
  console.log('others', others); // ['Spanish', 'Italian']
}
```

---

By the way, this is different from the arguments object. Rest parameters are just the ones that haven’t been given their own name, while the arguments object contains all args passed in to the function. Also, the arguments object is not a real array (it is similar to an array, but does not have any Array properties except length), while rest parameters are Array instances, meaning methods like sort, map, forEach or pop can be applied on it directly.

---

class: center, middle, inverse

# Spread operator

---

These three dots are spreading the array out into its constituent elements:

```js
const first = [1, 2, 3];
const last = [4, 5, 6];
console.log(first.push(last))    // [1, 2, 3, [4, 5, 6]]
console.log(first.push(...last)) // [1, 2, 3, 4, 5, 6]
function addThreeThings(a, b, c) {
  return a + b + c;
}
addThreeThings(...first); // same as: addThreeThings(1, 2, 3)
```

---

We can also use this feature to cast any iterable (in this example, a NodeList) into an instance of Array:

```js
const items = [...document.querySelectorAll('.item')];
```

We can now map, filter, reduce, etc. through `items`

---

class: center, middle, inverse

# Destructuring assignment with aliases and default values

---

We can rename or alias object properties fairly simple when destrucruting:

```js
const {country, capital: capitalCity, language} = {
    country: 'Norway',
    capital: 'Oslo',
    language: 'Norwegian'
};

// Note how we've changed capital to be capitalCity
console.log(country, capitalCity, language);
```

---

We can also assign default values (even at the same time we alias some destructured properties):

```js
const {
    firstName = 'John',
    lastName: surname = 'Doe'
} = {
    lastName: 'Knüssel'
};

console.log(firstName, surname); // John Knüssel
```

---

Destructuring also works on arrays, note how we skip both what comes before and after CAD.

```js
const currencies = ['AUD', 'NZD', 'EUR', 'CAD', 'GBP'];
const [australian,,,canadian] = currencies;

console.log(australian, canadian);
```

---

Finally, note that we can also use the spread operator here:

```js
const [australian, ...others] = ['AUD', 'NZD', 'EUR', 'CAD', 'GBP'];
console.log(others); // ['NZD', 'EUR', 'CAD', 'GBP']
```

---

class: center, middle, inverse

# Shorthand properties

---

This is sort of a backwards destructuring:

```js
const firstname = 'John';
const lastname = 'Doe';
const person = { firstname, lastname };
```

---

The ES5 equivalent would be:

```js
const person = { firstname: firstname, lastname: lastname };
```

---

class: center, middle, inverse

# Computed properties

---

This allows us to have dynamic key names on our objects. Just wrap your variable within square brackets and use it as your key:

```js
const action = 'sayHi';
const person = {
  name: 'John',
  [action]: () => console.log('Heya')
}

person.sayHi();
```

---

class: center, middle, inverse

# Method notation on objects

---

This is just sugar syntax that allows us to use a class-like method notation in our objects. The ES5 equivalent is: sayHi: () => console.log('Heya');

```js
const person = {
  name: 'John',
  sayHi() {
    console.log('Heya');
  }
}
```

---

class: center, middle, inverse

# The for-of loop

---

The for...of loop iterates over iterable objects (including Array, Map, Set, String, NodeList, arguments object and so on).

```js
const items = document.querySelectorAll('.item');

for (let item of items) {
  console.log(item);
}
```

---

class: center, middle, inverse

# Template literals and Interpolation

---

We use backticks to denote template literals, and we interpolate variables and expressions using `${}`. This just means calling `toString()` on each interpolation and joining the strings together.

---

Note that string templates preserve white spaces and line breaks.

```js
const x = 3;
const y = 7;
const blurb = `the result of
  ${x} + ${y}
is
  ${x + y}`;
```

---

class: center, middle, inverse

# Tagged template literals

---

This allows us to define custom string interpolation rules. We tag a string with a function name:

```js
const name = 'John Doe';
const age = 28;
const interpolated = myFunction`my name is ${name} and i'm ${age}`;
```

---

# Why don’t we have a regular function call?

Because of the way arguments get passed in onto myFunction. The string chunks and interpolations are passed separately as follows:

```js
myFunction(stringChunks, ...interpolations)
```

where:

```js
console.log(stringChunks); // ['my name is ', ' and i'm ', '']
console.log(interpolations); // ['John Doe', 28]
```

---

We can now do things like:

```js
function myFunction(stringChunks, ...interpolations) {
  const name = interpolations[0];
  const country = interpolations[1];
  let nationality;

  if (country === 'IT') {
    nationality = 'italian';
  } else if (country === 'AU') {
    nationality = 'australian';
  } else {
    nationality = 'unknown';
  }

  return stringChunks[0] + name + stringChunks[1] + nationality;
}
```

---

We could even use tagged template literals to add some markup, for example, wrap a string within an styled `<span>` tags or so.

---

class: center, middle, inverse

# Symbols

---

* https://medium.com/code-ops/party-tricks-with-es6-symbols-ee328fdb6c4b
* https://medium.com/code-ops/party-tricks-with-es6-symbols-ee328fdb6c4b

---

class: center, middle, inverse

# Map and WeakMap

---

# Map

The map object is just a simple key value map. JavaScript objects and maps are very similar to each other. However, maps does offer us a few bonuses that we don’t get from objects:

* an instance of Object has a prototype, so by default there are keys in the map whether the user has added them or not.
* an object key has to be a string, whereas in a map, it can be anything from a function to an object, to all other types of primitives.
* maps have methods on them that allow you to easily get the size of your map — we don’t have such a thing implemented natively on objects.
* Map gives us several iterators that we can use to go over our map to access the keys, values and entries.

---

```js
const myMap = new Map();

// API
myMap.set('foo', 'bar');
myMap.get('foo'); // returns bar
myMap.has('foo'); // returns true
myMap.size; // returns 1
myMap.clear();

myMap.keys(); // returns ['foo']
myMap.values(); // returns ['bar']
myMap.entries(); // returns ['foo', 'bar']

for([key, value] of myMap.entries()){
  console.log(key, value);
}
```

---

# WeakMap

WeakMap isn’t iterable, so you don’t get enumeration methods like .forEach.
The options of what is available for us to use as keys in our map is limited when using weakMaps: keys must be reference types. You can’t use value types like symbols, numbers, or strings as keys.

No references are held to the keys of the map, making it available for automatic garbage collection. This means WeakMap is great at keeping around metadata for objects, while those objects are still in use.

---

class: center, middle, inverse

# Set and WeakSet

---

# Set

Similar to Map, although Set doesn’t have keys, there’s only values, meaning sets can’t have duplicate values because the values are also used as keys.
const mySet = new Set([1, 2, 3, 4, 4]);

mySet.add(3);

---

# WeakSet

@TODO

---

class: center, middle, inverse

# Promises

---

Promises can either be resolved or rejected. When you resolve a promise, the .then() will fire, and when you reject a promise, the .catch() will fire instead. Usually, inside of your promise, you have some sort of logic that decides whether you’re going to reject or resolve the promise.

---

```js
const d = new Promise((resolve, reject) => {
  setTimeout(() => {
    if (true) {
      resolve('Promise got resolved, running then statements');
    } else {
      reject('Promise got rejected, running catch statements');
    }
  }, 500);
});
d
    .then((data) => {
    console.log(data);
    return 'foo bar';
  })
  .then((data) => {
      throw new Error('This will make catch statement run');
    console.log(data);
  })
  .catch((error) => console.error(error));
```

---

# Some extra notes on Promises:

* Promises start out in pending state and are settled once they’re either resolved or rejected.
* A .then callback can transform the result of the previous branch by returning a value.
* A .then callback can block on another promise by returning it.

---

# Some extra notes on Promises (cont'd):

* Promise.resolve(value) creates a promise that’s fulfilled with the provided value.
* Promise.reject(reason) creates a promise that’s rejected with the provided reason.
* Promise.all(…promises) creates a promise that settles when all …promises are fulfilled or 1 of them is rejected. This allows for running tasks in parallel.
* Promise.race(…promises) creates a promise that settles as soon as 1 of …promises is settled.

---

# Promises aren’t without some drawbacks as well:

* You can’t cancel a Promise, once created it will begin execution. If you don’t handle rejections or exceptions, they get swallowed.
* You can’t determine the state of a Promise, i.e.: whether it’s pending, fulfilled or rejected. Or even determine where it is in it’s processing while in pending state.
* If you want to use Promises for recurring values or events, there is a better mechanism/pattern for this scenario called streams.

---

class: center, middle, inverse

# Classes

---

* Not traditional OOP classes, this is just sugar syntax on top of prototypal inheritance.
* Constructor allows for class initialisation.
* Prototypal inheritance can be achieved using the extends keyword.
* Instance methods are declared using the short object literal syntax.
* Static methods need a static keyword prefix.
* Getters and setters allow you to use standard property access notation to read and write instance attributes while still having the ability to customise how the property is retrieved or mutated.

---

```js
class Dog extends Animal {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }

  static isAwesome() {
    return true;
  }
  getName() {
    return this.name;
  }

  bark() {
    super.makeSound('woof');
  }

  get age() {
    return this.age * 7;
  }

  set age(newAge) {
    this.age = newAge / 7;
  }
}
```

---

class: center, middle, inverse

# Generators

---

# Generators

Generators produce encapsulated suspended execution contexts. We can yield values and iterate over them until no more values exist in the generator.

They return an object that implements an iteration protocol (i.e.: has a next method that returns {value: 1, done: false})

---

* https://medium.com/javascript-scene/7-surprising-things-i-learned-writing-a-fibonacci-generator-4886a5c87710
* https://davidwalsh.name/es6-generators

---

class: center, middle, inverse

# Proxies

---

* http://librosweb.es/tutorial/que-son-y-como-funcionan-los-proxies-de-ecmascript-5-es2015/

---

class: center, middle, inverse

# ES Modules

---

# Importing

```js
import defaultMember from 'module-name';
import * as name from 'module-name';
import {member} from 'module-name';
import {member as alias} from 'module-name';
import {member1, member2} from 'module-name';
import {member1, member2 as alias2, [...]} from 'module-name';
import defaultMember, {member [ , [...] ]} from 'module-name';
import defaultMember, * as name from 'module-name';
import 'module-name';
```

---

# Named vs Default exports

```js
// named export
import {Provider} from 'react-redux';

// default export
import React from 'react';

// namespace import syntax
import * as myModule from './myModule';
```

---

# Exporting

```js
export {name1, name2, ..., nameN};
export {variable1 as name1, variable2 as name2};
export let name1, name2, ..., nameN; // also var
export let name1 = ..., name2 = ..., ..., nameN; // also var, const

export default expression;
export default function (...) { ... } // also class
export default function namedFunction(...) { … } // also class
export {name1 as default, ... };

export * from ...;
export {name1, name2, ..., nameN} from ...;
export {import1 as name1, import2 as name2, ..., nameN} from ...;
```

---

class: center, middle, inverse

# Reflection

---

class: center, middle, inverse

# Iterables

---

class: center, middle, inverse

# Additions to String function constructor

---

Note all of these are instance methods.

---

# String.prototype.startsWith(substring)

```js
'javascript'.startsWith('java'); // returns true
```

---

# String.prototype.endsWith(substring)

```js
'javascript.endsWith('script'); // returns true
```

---

# String.prototype.includes(substring)

```js
'javascript'.includes('src'); // returns true
```

---

# String.prototype.repeat(times)

```js
'woot'.repeat(2); // returns wootwoot
```

---

class: center, middle, inverse

# Additions to the Number function constructor

---

Note all of these are static methods rather than instance ones.

---

# Number.isInteger(value)

Returns true or false depending on whether the argument is an integer.

---

# Number.isNaN()

Returns a boolean indicating whether or not the given value is of type number and its value is NaN.

---

# Number.isFinite()

Returns a boolean indicating whether or not the given value is of type number and its value is finite.

Note on Number.isNaN and Number.isFinite: these static methods are more robust than their global counterparts window.isNaN and window.isFinite as the former don’t cast the parameter to be a number. Thus the argument needs to meet two conditions: first it has to be of type number, and secondly it needs to either be finite or NaN.

---

class: center, middle, inverse

# Additions to the Array function constructor

---

Note that some of these are static methods whereas the rest are instance methods.

---

# Array.from(iterable)

An iterable collection can be a DOM collection (NodeList), an object, a string, etc. For instance:

```js
const items = Array.from(document.querySelectorAll('.item'));
```

We can now map, filter, reduce, etc. the items list. But you can’t do all these operations on a NodeList which is the result of querying the DOM.

---

# Array.of()

This is essentially similar to the Array constructor, the only difference being the handling of integer arguments: while Array.of(7) creates an array with a single element, Array(7) creates an empty array with a length property of 7 — note this implies an array of 7 empty slots, not slots with actual undefined values).

```js
Array.of(7); // [7]
Array.of(1, 2, 3); // [1, 2, 3]
Array(7); // [ , , , , , , ]
Array(1, 2, 3); // [1, 2, 3]
```

---

* Array.prototype.find()
* Array.prototype.findIndex()
* Array.prototype.keys()
* Array.prototype.values()
* Array.prototype.fill()

---

# Links and Resources

* https://egghead.io/courses/learn-es6-ecmascript-2015
* https://leanpub.com/ecmascript2015es6guide/read
* https://ponyfoo.com/articles/es6
* https://github.com/lukehoban/es6features
* https://github.com/addyosmani/es6-equivalents-in-es5
* https://www.toptal.com/javascript/javascript-es6-cheat-sheet
* https://devhints.io/es6

---

class: center, middle, inverse, contact-details

## .secondary[Slides:] https://fknussel.com
## .secondary[Email:] fknussel@gmail.com
## .secondary[Twitter/GitHub:] @fknussel

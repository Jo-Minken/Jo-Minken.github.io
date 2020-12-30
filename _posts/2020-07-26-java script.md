---
layout: post
title: Learn JavaScript (codecademy)
date: 2020-07-26
categories: [codecademy]
---

This post is a note for what I have learned from
- [codecademy - Learn JavaScript](https://www.codecademy.com/learn/introduction-to-javascript)
- [codecademy - Learn Asynchronous JavaScript](https://www.codecademy.com/learn/asynchronous-javascript)
- [codecademy - Building Interactive JavaScript Websites](https://www.codecademy.com/learn/build-interactive-websites)
- [codecademy - How to Debug JavaScript Errors](https://www.codecademy.com/learn/javascript-errors-debugging)

## ES6 browser compatibility

JavaScript ES6 is syntactically more similar to other object-oriented programming languages. 
This leads to less friction when experienced, non-JavaScript developers want to learn JavaScript

- [caniuse.com](https://caniuse.com) — A website that provides data on web browser compatibility for HTML, CSS, and JavaScript features
- Babel — A Javascript library that you can use to convert new, unsupported JavaScript (ES6), into an older version (ES5) that is recognized by most modern browsers

### Babel

#### npm

Developers use *npm* to access and manage Node packages

```
project
|_ src
|___ main.js
```
place your ES6 JavaScript file in a directory called src
the path to the ES6 file is ./src/main.js

```
$ npm init
```

`npm init` command creates a **package.json** file in the root directory

```
project
|_ src
|___ main.js
|_ package.json
```

#### Babel install

The `install` command creates a folder called **node_modules** and copies the package files to it
It also installs all of the dependencies for the given package

```
$ npm install babel-cli -D
$ npm install babel-preset-env -D
```

The `-D` flag instructs npm to add each package to a property called `devDependencies` in **package.json**

- `babel-cli` — A Node package that contains command line tools for Babel
- `babel-preset-env` — A Node package that contains ES6+ to ES5 syntax mapping information

```
project
|_ node_modules
|___ .bin
|___ ...
|_ src
|___ main.js
|_ package.json
```

#### .babelrc

```
$ touch .babelrc
```
Create a new `.babelrc` file.

```
project
|_ node_modules
|___ .bin
|___ ...
|_ src
|___ main.js
|_ .babelrc
|_ package.json
```

Inside **.babelrc** you need to define the *preset* for your source JavaScript file. The preset specifies the version of your initial JavaScript file
transpile JavaScript code from versions ES6 and later (ES6+) to ES5

```
# .bablerc

{
  "presets": ["env"]
}
```

`["env"]` instructs Babel to transpile any code from versions ES6 and later

#### Babel Source Lib

```
# package.json

...
"scripts": {
  "test": "echo \"Error: no test specified\" && exit 1",
  "build": "babel src -d lib"
}
```

specify a script in **package.json** that initiates the ES6+ to ES5 transpilation

- `babel` — The Babel command call responsible for transpiling code.
- `src` — Instructs Babel to transpile all JavaScript code inside the **src** directory.
- `-d` — Instructs Babel to write the transpiled code to a directory.
- `lib` — Babel writes the transpiled code to a directory called `lib`

#### run build

```
$ npm run build
```

Babel writes the ES5 code to a file named **main.js** (the same name as the original file), inside of a folder called `lib`

```
project
|_ lib
|___ main.js
|_ node_modules
|___ .bin
|___ ...
|_ src
|___ main.js
|_ .babelrc
|_ package.json
```

view the ES5 code in **./lib/main.js**

## data types

Number, String, Boolean, Null, Undefined, Symbol, Object

### string interpolation

```javascript
console.log(`${v1} and ${v2}`) //not single quote
```

using template literals to embed variables 

## iterators

### .forEach() Method

```javascript
groceries.forEach(groceryItem =$ console.log(groceryItem));
```

https://s3.amazonaws.com/codecademy-content/courses/learn-javascript-iterators/iterator+anatomy.svg

execute the same code on every element in an array but does not change the array and returns `undefined`

### .map() Method

```javascript
const numbers = [1, 2, 3, 4, 5]; 
const bigNumbers = numbers.map(number =$ {
  return number * 10;
});

console.log(bigNumbers); // Output: [10, 20, 30, 40, 50]
```

it takes an argument of a callback function and returns a new array

### .filter() Method

```javascript
const words = ['chair', 'music', 'pillow', 'brick', 'pen', 'door']; 
const shortWords = words.filter(word =$ {
  return word.length < 6;
});

console.log(shortWords); // Output: ['chair', 'music', 'brick', 'pen', 'door']
```
`filter()` returns a new array
The callback function for the `.filter()` method should return `true` or `false`

### .findIndex() Method

```javascript
const jumbledNums = [123, 25, 78, 5, 9]; 
const lessThanTen = jumbledNums.findIndex(num =$ {
  return num < 10;
});

console.log(lessThanTen); // Output: 3 
```

Calling `.findIndex()` on an array will return the index of the first element that evaluates to `true` in the callback function

### .reduce() Method

```javascript
const numbers = [1, 2, 4, 10];

const summedNums = numbers.reduce((accumulator, currentValue) =$ {
  return accumulator + currentValue
})
console.log(summedNums) // Output: 17

const summedNums = numbers.reduce((accumulator, currentValue) =$ {
  return accumulator + currentValue
}, 100)  //Second argument: base of the sum
console.log(summedNums); // Output: 117
```

`accumulator`: sum

### .some() Method

```javascript
const array = [1, 2, 3, 4, 5];
const even = (element) =$ element % 2 === 0;

console.log(array.some(even)); // Output: true
```

The `some()` method tests whether at least one element in the array passes the test implemented by the provided function

### .every() Method

```javascript
const array1 = [1, 30, 39, 29, 10, 13];
const isBelowThreshold = (currentValue) =$ currentValue < 40;

console.log(array1.every(isBelowThreshold)); // Output: true
```

The `every()` method tests whether all elements in the array pass the test implemented by the provided function

## objects

Key-value data

```javascript
let spaceship = {
  'Fuel Type' : 'Turbo Fuel',
  homePlanet : 'Earth',
  color: 'silver',
  'Secret Mission' : 'Discover life outside of Earth.'
};

spaceship.color = 'glorious gold'; // property assign
delete spaceship['Secret Mission']; // property delete
```

### Method

```javascript
let alienShip = {
  retreat() {
    console.log(retreatMessage)
  },
  takeOff() {
    console.log('Spim... Borp... Glix... Blastoff!')
  }
};

alienShip.retreat();
alienShip.takeOff();
```

#### Looping Through Objects

```javascript
// for...in
for (let crewMember in spaceship.crew) {
  console.log(`${crewMember}: ${spaceship.crew[crewMember].name}`)
};
```

#### methods

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object#Methods_of_the_Object_constructor

### Privacy

```javascript
const bankAccount = {
  _amount: 1000
}

bankAccount._amount = 1000000; // Even so, it is still possible to reassign _amount
```

`_` before the name of a property to mean that the property should not be altered

#### getters

```javascript
const person = {
  _firstName: 'John',
  _lastName: 'Doe',
  get fullName() {
    if (this._firstName && this._lastName){
      return `${this._firstName} ${this._lastName}`;
    } else {
      return 'Missing a first name or a last name.';
    }
  }
}

// To call the getter method: 
person.fullName; // 'John Doe'
```

#### setters

```javascript
const person = {
  _age: 37,
  set age(newAge){
    if (typeof newAge === 'number'){
      this._age = newAge;
    } else {
      console.log('You must assign a number to age');
    }
  }
};
```

## class

```javascript
class Dog {
  constructor(name) {
    this.name = name;
    this.behavior = 0;
  } 

  get name() {
    return this._name;
  }

  get behavior() {
    return this._behavior;
  }

  incrementBehavior() {
    this._behavior++;
  }
}

const halley = new Dog('Halley'); // Create new Dog instance
console.log(halley.name); // Log the name value saved to halley
// Output: 'Halley'
```

### Inheritance

```javascript
class Animal {
  constructor(name) {
    this._name = name;
    this._behavior = 0;
  }

  get name() {
    return this._name;
  }

  get behavior() {
    return this._behavior;
  }

  incrementBehavior() {
    this._behavior++;
  }
} 

class Cat extends Animal {
  constructor(name, usesLitter) {
    super(name);
    this._usesLitter = usesLitter;
  }

  get usesLitter() {
    return this._usesLitter;
  }
}
```

`super` keyword calls the `constructor()` of a parent class
If you use `this` before `super`, JavaScript will throw an error

### Static Methods

methods that aren’t available in individual instances, but can be called directly from the class (ex.  `Date.now()` )

```javascript
class Animal {
  constructor(name) {
    this._name = name;
    this._behavior = 0;
  }

  static generateName() {
    const names = ['Angel', 'Spike', 'Buffy', 'Willow', 'Tara'];
    const randomNumber = Math.floor(Math.random()*5);
    return names[randomNumber];
  }
} 
```

## modules

### using modules

#### exports

JavaScript *modules* are reusable pieces of code that can be exported from one program and imported for use in another program

```javascript
// Node.js
module.exports = {
  specialty: "Roasted Beet Burger with Mint Sauce",
  getSpecialty: function() {
    return this.specialty;
  } 
}; 

// ES6
let Menu = {};
Menu.specialty = "Roasted Beet Burger with Mint Sauce";
Menu.getSpecialty = () =$ { return this.specialty; };

export default Menu;
```

Make the module available for use in another file with Node.js `module.exports` syntax

#### Node.js: require()

```javascript
// Node.js
const Menu = require('./menu.js');

console.log(Menu.getSpecialty());
```

use the `require()` function to import modules

#### export specific attributes or methods

```javascript
let specialty = '';
function isVegetarian() {
}; 
function isLowSodium() {
}; 

export { specialty, isVegetarian };
```
`export { specialty, isVegetarian };` exports objects by their variable names
`isLowSodium` is not exported, since it is not specified in the export syntax

#### import

```javascript
import Menu from './menu';
import { specialty, isVegetarian } from './menu';
```

`import` keyword for importing objects

#### export named exports
```javascript
export let specialty = '';
export function isVegetarian() {
}; 
function isLowSodium() {
}; 
```

```javascript
import { specialty, isVegetarian } from 'menu';
```

#### export as

```javascript
let specialty = '';
let isVegetarian = function() {
}; 
let isLowSodium = function() {
}; 

export { specialty as chefsSpecial, isVegetarian as isVeg, isLowSodium };
```

```javascript
import { chefsSpecial, isVeg } from './menu';
```

```javascript
import * as Carte from './menu';

Carte.chefsSpecial;
Carte.isVeg();
Carte.isLowSodium(); 
```

#### combining export statements

```javascript
let specialty = '';
function isVegetarian() {
}; 
function isLowSodium() {
}; 
function isGlutenFree() {
};

export { specialty as chefsSpecial, isVegetarian as isVeg };
export default isGlutenFree;
```

```javascript
import { specialty, isVegetarian, isLowSodium } from './menu';
import GlutenFree from './menu';
```

## Promise

 A `Promise` object can be in one of three states:

- **Pending**: The initial state— the operation has not completed yet.
- **Fulfilled**: The operation has completed successfully and the promise now has a *resolved value*. For example, a request’s promise might resolve with a JSON object as its value.
- **Rejected**: The operation has failed and the promise has a reason for the failure. This reason is usually an `Error` of some kind.

```javascript
const executorFunction = (resolve, reject) =$ {
  if (someCondition) {
      resolve('I resolved!');
  } else {
      reject('I rejected!'); 
  }
}
const myFirstPromise = new Promise(executorFunction);
```

### Consuming Promises

`.then()` is a higher-order function— it takes two callback functions as arguments. We refer to these callbacks as *handlers*. When the promise settles, the appropriate handler will be invoked with that settled value.

- The first handler, sometimes called `onFulfilled`, is a *success handler*, and it should contain the logic for the promise resolving.
- The second handler, sometimes called `onRejected`, is a *failure handler*, and it should contain the logic for the promise rejecting.

```javascript
const handleSuccess = (resolvedValue) =$ {
  console.log(resolvedValue);
};

const handleFailure = (rejectionReason) =$ {
  console.log(rejectionReason);
};

prom.then(handleSuccess, handleFailure);
```

#### Using catch() with Promises

```javascript
prom
  .then((resolvedValue) =$ {
    console.log(resolvedValue);
  })
  .catch((rejectionReason) =$ {
    console.log(rejectionReason);
  });
```

#### Chaining multiple promises

```javascript
firstPromiseFunction()
.then((firstResolveVal) =$ {
  return secondPromiseFunction(firstResolveVal);
})
.then((secondResolveVal) =$ {
  console.log(secondResolveVal);
});
```

##### DON'T !!!! : Nesting promises

```javascript
returnsFirstPromise()
.then((firstResolveVal) =$ {
  return returnsSecondValue(firstResolveVal)
    .then((secondResolveVal) =$ {
      console.log(secondResolveVal);
    })
})
```

#### Using Promise.all()

```javascript
let myPromises = Promise.all([returnsPromOne(), returnsPromTwo(), returnsPromThree()]);

myPromises
  .then((arrayOfValues) =$ {
    console.log(arrayOfValues);
  })
  .catch((rejectionReason) =$ {
    console.log(rejectionReason);
  });
```

`Promise.all()` accepts an array of promises as its argument and returns a single promise

## Asyn-Await

JavaScript is non-blocking: instead of stopping the execution of code while it waits, JavaScript uses an [event-loop](https://youtu.be/8aGhZQkoFbQ) which allows it to efficiently execute other tasks while it awaits the completion of these asynchronous actions

### Asyn

```javascript
const myFunc = async () =$ {
  // Function body here
};

myFunc().then(resolvedValue =$ {
  console.log(resolvedValue);
});
```

The `async` keyword is used to write functions that handle asynchronous actions
`async` functions always return a promise

#### Await

```javascript
async function asyncFuncExample(){
  let resolvedValue = await myPromise();
  console.log(resolvedValue);
}

asyncFuncExample(); // Prints: I am resolved now!
```

The `await` keyword can only be used inside an `async` function. `await` is an operator: it returns the resolved value of a promise
We can `await` the resolution of the promise it returns inside an `async` function

### Handling dependent Promises

#### Native Promise

```javascript
function nativePromiseVersion() {
    returnsFirstPromise()
    .then((firstValue) =$ {
        console.log(firstValue);
        return returnsSecondPromise(firstValue);
    })
   .then((secondValue) =$ {
        console.log(secondValue);
    });
}
```

#### Rewrite with async

```javascript
async function asyncAwaitVersion() {
 let firstValue = await returnsFirstPromise();
 console.log(firstValue);
 let secondValue = await returnsSecondPromise(firstValue);
 console.log(secondValue);
}
```

##### error handling

```javascript
async function usingTryCatch() {
 try {
   let resolveValue = await asyncFunction('thing that will fail');
   let secondValue = await secondAsyncFunction(resolveValue);
 } catch (err) {
   // Catches any errors in the try block
   console.log(err);
 }
}

usingTryCatch();
```

### Handling independent Promises

```javascript
async function waiting() {
 const firstValue = await firstAsyncThing();
 const secondValue = await secondAsyncThing();
 console.log(firstValue, secondValue);
}

async function concurrent() {
 const firstPromise = firstAsyncThing();
 const secondPromise = secondAsyncThing();
console.log(await firstPromise, await secondPromise);
}
```

#### Await Promise.all()

```javascript
async function asyncPromAll() {
  const resultArray = await Promise.all([asyncTask1(), asyncTask2(), asyncTask3(), asyncTask4()]);
  for (let i = 0; i<resultArray.length; i++){
    console.log(resultArray[i]); 
  }
}
```

## Requests

### XHR Send Requests

```javascript
// Send GET Request
const xhr = new XMLHttpRequest();
const url = 'https://api-to-call.com/endpoint';

xhr.responseType = 'json';
xhr.onreadystatechange = () =$ {
  if (xhr.readyState === XMLHttpRequest.DONE){
    return xhr.response;
  }
}

xhr.open('GET', url);
xhr.send();

// Send POST Request
const xhr = new XMLHttpRequest();
const url = 'https://api-to-call.com/endpoint';
const data = JSON.stringify({id: '200'});

xhr.responseType = 'json';
xhr.onreadystatechange = () =$ {
  if(xhr.readyState === XMLHttpRequest.DONE){
    return xhr.response;
  }
}

xhr.open('POST', url);
xhr.send(data);
```

#### set http header

```javascript
xhr.setRequestHeader('Content-type', 'application/json');
xhr.setRequestHeader('apikey', apiKey);
xhr.send();
```

###  `fetch()` function

- Creates a request object that contains relevant information that an API needs.
- Sends that request object to the API endpoint provided.
- Returns a promise that ultimately resolves to a response object, which contains the status of the promise with information the API sent back.

```javascript
// GET fetch()
fetch('https://api-to-call.com/endpoint').then(response =$ {
  if (response.ok) {
    return response.json();
  }
  throw new Error('Request failed!');
}, networkError =$ {
  console.log(networkError.message);
}).then(jsonResponse =$ {
  return jsonResponse;
});

// POST fetch()
fetch('https://api-to-call.com/endpoint', {
  method: 'POST',
  body: JSON.stringify({id: "200"})
}).then(response =$ {
  if(response.ok){
	  return response.json();  
  }
	throw new Error('Request failed!');
}, networkError =$ {
  console.log(networkError.message);
}).then(jsonResponse =$ {
  console.log(jsonResponse);
})
```

MDN Web Docs - Web APIs > Fetch API<br>
[https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

### Async / Await

```javascript
// GET fetch()
const getData = async () =$ {
  try {
    const response = await fetch('https://api-to-call.com/endpoint');
    if (response.ok) {
      const jsonResponse = await response.json();
      return jsonResponse;
    }
    throw new Error('Request failed!');
  } catch (error) {
    console.log(error);	
  }
}

// POST fetch()
const getData = async () =$ {
  try {
    const response = await fetch('https://api-to-call.com/endpoint', {
      method: 'POST',
      body: JSON.stringify({id: 200})
    })
    if(response.ok){
      const jsonResponse = await response.json();
      return jsonResponse;
    }
    throw new Error('Request failed!');
  } catch(error) {
    console.log(error);
  }
}
```



# Interactive JS Sites

## Load Script

### Script tag
#### Defer

```html
<script src="example.js" defer></script>
```

ensures that the entire HTML file has been parsed before the script is executed

 it will execute immediately after it has been downloaded

#### async

```html
<script src="example.js" async></script>
```

it will execute immediately after it has been downloaded

## DOM

### Select and Modify Elements

```javascript
document.querySelector('p'); // specify a CSS selector and returns the first element that matches
document.getElementById('bio');

let blueElement = document.querySelector('.blue');
blueElement.style.backgroundColor = 'blue';
```

#### Create and Insert Elements

```javascript
let paragraph = document.createElement('p');
paragraph.id = 'info'; 
paragraph.innerHTML = 'The text inside the paragraph';

document.body.appendChild(paragraph);
```

#### Remove an Element

```javascript
let paragraph = document.querySelector('p');
document.body.removeChild(paragraph);
```

### Event

JavaScript interprets events as *event object* that store event information residing in properties and methods
event properties can hold data like the event target or event type

MDN Web Docs - Event reference<br>
[https://developer.mozilla.org/en-US/docs/Web/Events](https://developer.mozilla.org/en-US/docs/Web/Events)

#### Event Object Properties

```javascript
let share = document.getElementById('share-button');
let sharePhoto = function(event) {
  event.target.style.display = 'none';
  text.innerHTML = 'You share the puppy in ' 
  + event.timeStamp + ' ms.';
}

share.onclick = sharePhoto;
```

- the `.target` property to access the element that triggered the event.
- the `.type` property to access the name of the event.
- the `.timeStamp` property to access the number of milliseconds that passed since the document loaded and the event was triggered.

The `.addEventListener()` method is good for large code and it is especially useful for adding multiple event handlers to a specific event

# How to Debug JavaScript Errors

### Debugging

#### JavaScript Error Types

- **SyntaxError**: This error will be thrown when a typo creates invalid code — code that cannot be interpreted by the compiler. When this error is thrown, scan your code to make sure you properly opened and closed all brackets, braces, and parentheses and that you didn’t include any invalid semicolons.
- **ReferenceError**: This error will be thrown if you try to use a variable that does not exist. When this error is thrown, make sure all variables are properly declared.
- **TypeError**: This error will be thrown if you attempt to perform an operation on a value of the wrong type. For example, if we tried to use a string method on a number, it would throw a TypeError.

#### Debugging with `console.log()`

#### Check Documentation

MDN Web Docs - JavaScript<br>
[https://developer.mozilla.org/en-US/docs/Web/JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)

### Error handling
#### Constructing an Error

```javascript
console.log(Error('Your password is too weak.'));
console.log(new Error('Your password is too weak.')); // Both methods will lead to the same functionality
// Prints: Error: Your password is too weak.
```

#### Throw an Error

```javascript
throw Error('Something wrong happened');
// Error: Something wrong happened

console.log('This will never run'); // the console.log() statement will not run
```

#### Catch an Error

```javascript
try {
  throw Error('This error will get caught');
} catch (e) {
  console.log(e); // e represents the thrown error
}
// Prints: This error will get caught

console.log('The thrown error that was caught in the try...catch statement!');
// Prints: 'The thrown error that was caught in the try...catch statement!'

/* An example using try and catch */
const someVar = 'Cannot be reassigned';
try {
  someVar = 'Still going to try';
} catch(e) {
  console.log(e);
}
// Prints: TypeError: Assignment to constant variable.
```


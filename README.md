<!--

WARNING!! DON'T EDIT THE FILE README.md on the root of the project, that one is a GENERATED FILE!

You should just edit the source file at src/README.md - the one which stars with ## Design patterns for JS

-->

## Design patterns for JS
### featuring modules
<img src="img/rubik.gif" class="logo"></p>

Tiago Garcia @ [Women Who Code SF](https://www.meetup.com/Women-Who-Code-SF/) - Oct 27th, 2016

---

## Tiago Garcia

<p class="ac-column-logo">
  <img src="img/avatar.jpg" class="avatar ac-logo-big">
</p>

<ul class="ac-column-text">
  <li>JavaScript P.I., dog daddy, vegetarian rollerblader,<br/>not your typical Brazilian.</li>
  <li>Tech Manager at [Avenue Code](http://www.avenuecode.com)</li>
  <li>Tech Lead at [Macys.com](http://www.macys.com)</li>
  <li>Twitter: [@tiagooo_romero](https://twitter.com/tiagooo_romero)</li>
  <li>Site: *[tiagorg.com](http://tiagorg.com)*</li>
  <li>*[tgarcia@avenuecode.com](mailto:tgarcia@avenuecode.com)*</li>
</ul>

----

<img src="img/ac-slide.svg" class="full" />

---

## Agenda

- Patterns and Design Patterns
- JavaScript Design Patterns
    - Constructor
    - Fa&ccedil;ade
    - Module
      - CommonJS
      - AMD
      - ES2015
    - Observer aka Event

---

## Patterns

- Pattern:
    - Proven *solution* to a certain class of *problems* in a specific *context*
    - Pre-conditions -> requirements
    - Post-conditions -> consequences
- Term coined by *Christopher Alexander*, an architect, as an aid to design cities and buildings, in 1977.
- Pattern *language*: a common vocabulary
- *Network* of connected patterns -> reuse

----

## Design Patterns

- In computing, Design Patterns came to address common problems in OOP.
- 1994: *Gang Of Four* publishes [Design Patterns](http://www.amazon.com/Design-Patterns-Object-Oriented-Professional-Computing/dp/0201634988), a critical turning point in the history of OOP.
   - Features 23 classic design patterns, categorized in *creational*, *structural* and *behavioral*.
   - Many believe that "Design Patterns" term is exclusive to those 23 patterns -> not the case

----

## Design Patterns

- Back in 1994, programming languages' native support to Design Patterns was hazy.
- Nowadays, many of those patterns have been incorporated into programming languages and frameworks. Ex: Python, Java.

---

## JS Design Patterns

- JS code is more complex today than ever before.
   - Dazzling experience & interaction
   - Async calls, Callbacks, Promises
   - Single Page Applications
- Good reasons to use Design Patterns in JS:
   - Avoid "spaghetti code" (unstructured/hard to read).
   - Better overall maintainability, making it clear where to change the code.
   - Far more objective unit tests.
- Today we are covering some of the work of [Stoyan Stefanov](https://twitter.com/stoyanstefanov) and [Addy Osmani](https://twitter.com/addyosmani).

----

## Remember Uncle Ben!

<img src="img/uncle-ben.png">

---

## Constructor

- *Creational* pattern
- Purpose: to initialize a newly created object once memory has been allocated.
- Pre-condition: a definition for the object
- Post-condition: a customized object allocated in the memory

----

## Constructor

- JS is indeed OO but it differs from classic OO, and there are different ways to construct an object:
```javascript
  // object literal a.k.a. Singleton
  var myDog = {};

  // ES5 Object.create method from prototype
  var myDog = Object.create(Dog.prototype);

  // constructor call
  var myDog = new Dog();
```

----

## Basic Constructor

- *new* must be used before invoking.
- *this* references the new object being created.
- Returns a new instance, no need for *return*.

```javascript
  function Dog(name, breed) {
    this.name = name;
    this.breed = breed;
    this.bark = function() {
      return this.name + ': woof, woof!';
    };
  }

  var myDog = new Dog('Sherlock', 'beagle');
  console.log(myDog.bark());
```
<p class="center">[view on Plunker](http://plnkr.co/edit/ARySm1?p=preview)</p>

----

## Prototype

- Defining a method in the constructor is bad, as each new instance will contain a definition for the method.
- By writing on the function's *prototype*, all instances can share the same definition of a method.

```javascript
  function Dog(name, breed) {
    this.name = name;
    this.breed = breed;
  }
  Dog.prototype.bark = function() {
    return this.name + ': woof, woof!';
  };

  var myDog = new Dog('Sherlock', 'beagle');
  console.log(myDog.bark());
```


----

## ES2015 Classes

- ES2015 introduced some syntax sugar to make this easier to code and more straightforward to read.

```javascript
  class Dog {
    constructor(name, breed) {
      this.name = name;
      this.breed = breed;
    }

    bark() {
      return `${this.name}: woof, woof!`;
    }
  }

  var myDog = new Dog('Sherlock', 'beagle');
  console.log(myDog.bark());
```

<p class="center">[view on Plunker](http://plnkr.co/edit/xSNeLd?p=preview)</p>

---

## Fa&ccedil;ade

- *Structural* pattern
- Purpose: to provide a convenient high-level interface to a component, hiding its underlying complexity.
- Represents one of the main principles from GoF: "Program to an interface, not an implementation".
- Pre-condition: a complex, low-level code chunk
- Post-condition: a simplified, high-level interface

----

## Fa&ccedil;ade

- Example: printing out a JSON from the server

```javascript
  function ajax(url, callback) {
    let xhr = new XMLHttpRequest();
    xhr.onload = function() {
      if (this.status === 200) {
        callback(JSON.parse(this.responseText));
      }
    };
    xhr.open('GET', url, true);
    xhr.send();
  }

  ajax('http://swapi.co/api/planets/1/', console.log);
```

<p class="center">[view on Plunker](http://plnkr.co/edit/bqCCAq?p=preview)</p>

----

## IIFE

- Immediately-Invoked Function Expression, keeps internal vars and functions as private, within closure.
- 2 flavors:

```javascript
// Parenthesis around function
var result1 = ( function() {
  ...
} )(); // After parenthesis, () to invoke it

// Parenthesis around entire expression
var result2 = ( function() {
  ...
}() ); // After function, () to invoke it
```

----

## IIFE

```javascript
var getCurrentWeather = (function() {
  function generateResponse(useMetric, data) {
    var unit = `°${useMetric? 'C': 'F'}`;
    console.log(`${data.weather[0].main}. Now ${data.main.temp}${unit}.`);
  }
  return function(city, country, useMetric) {
    var unit = useMetric ? 'metric' : 'imperial';
    var url = `http://api.openweathermap.org/data/2.5/weather?q=${city},${country}&units=${unit}&APPID=c48ddbf104363ec75645b36b470559fc`;
    ajax(url, generateResponse.bind(undefined, useMetric));
  }
})(); // IIFE - declaration and invocation in a single shot
```

```javascript
getCurrentWeather('San Francisco', 'US', false);
getCurrentWeather('Paris', 'FR', true);
```
<p class="center">[view on Plunker](http://plnkr.co/edit/u3xVQh?p=preview)</p>

---

## Challenge 1

- Fork from [http://plnkr.co/edit/u3xVQh?p=preview](http://plnkr.co/edit/u3xVQh?p=preview)
- Take a moment to understand this implementation.
- Write a class *`Location`* whose constructor receives 3 attributes: *`city`*, *`country`* and *`useMetric`*.
- Under *`Location`*, write a method *`getMyCurrentWeather(printFn)`*, which will call *`getCurrentWeather`* behind the scenes.
- Now, replace the 2 invocations to get weather from San Francisco and Paris with 2 instances of *`Location`*, one for each city, where you will call its method *`getMyCurrentWeather`*.

---

## Module

- *Structural* pattern
- Purpose: to define reusable components with private/public attributes and methods.
- Encapsulation through *closures*, using function scope.
- Pre-condition: a chunk of code with a return point
- Post-condition: a module definition that encapsulates that chunk of code

----

## Module

```javascript
  var Zoo = (function() {
    var getBarkStyle = function(isHowler) {
      return isHowler? 'woooooow!': 'woof, woof!';
    };
    return {
      Dog: function(name, breed) {
        this.name = name;
        this.bark = getBarkStyle(breed === 'husky');
      },
      Wolf: function(name) {
        this.name = name;
        this.bark = getBarkStyle(true);
      }
    };
  })(); // IIFE
```

```javascript
var myDog = new Zoo.Dog('Sherlock', 'beagle');
console.log(myDog.name + ': ' + myDog.bark); // Sherlock: woof, woof!
```
<p class="center">[view on Plunker](http://plnkr.co/edit/JEgEpo?p=preview)</p>

----

## Revealing Module

- Flexibility to switch items from private to public scope.
```javascript
  let Zoo = (function() {
    let getBarkStyle = isHowler => isHowler ? 'woooooow!' : 'woof, woof!';
    let Dog = function(name, breed) {
      this.name = name;
      this.bark = getBarkStyle(breed === 'husky');
    };
    let Wolf = function(name) {
      this.name = name;
      this.bark = getBarkStyle(true);
    };
    return {
      Dog: Dog,
      Wolf: Wolf
    };
  })(); // IIFE
```

<p class="center">[view on Plunker](http://plnkr.co/edit/wptpQ5?p=preview)</p>

----

## Why to use Modules?

- Global JS is bad for performance, reusability, readability, code organization and allows side-effects.
- A module can be delivered as a dependency for another module.
- Modules can be packaged and deployed separately from each other, mitigating the "butterfly effect".
- Modules bring cohesion up and coupling down.

----

## Module loaders

- On the previous example, *Zoo* is a global var, which is:
  - fragile (any posterior code can modify/redefine it)
  - not scalable (what if you define 100 modules?)
  - counter-productive (you have to manually resolve your dependencies)
- Enter *module loaders*:
  - Container for module registration under aliases
  - Dependency injection
  - Modules loading on demand
- Included with your Module standard of choice!

---

## CommonJS

- A standard for *synchronous* modules.
- Pros:
  - Official Node.js and NPM format.
  - Simple and convenient syntax.
  - Guaranteed order of execution of modules.
- Cons:
  - Doesn't naturally work on the browser (there are solutions for that as *Browserify* and *Webpack*).
  - Sequential loading of modules is slower than asynchronous.
  - NPM dependency tree can skyrocket easily.

----

## CommonJS

- Export your module interface with *module.exports*
- Import on the client using *require(dependency)*
```javascript
  // zoo.js
  let getBarkStyle = isHowler => isHowler ? 'woooooow!' : 'woof, woof!';
  let Dog = function(name, breed) {
    this.name = name;
    this.bark = getBarkStyle(breed === 'husky');
  };
  let Wolf = function(name) {
    this.name = name;
    this.bark = getBarkStyle(true);
  };
  module.exports = {
    Dog: Dog,
    Wolf: Wolf
  };
```

----

## CommonJS

```javascript
// client.js
let Zoo = require('./zoo');

let myDog = new Zoo.Dog('Sherlock', 'beagle');
console.log(`${myDog.name}: ${myDog.bark}`); // Sherlock: woof, woof!
```
<p class="center">[view on Plunker](http://plnkr.co/edit/m0UXQg?p=preview)</p>

---

## AMD

- A standard for *asynchronous* modules.
- Pros:
  - Multiple modules can be loaded in parallel.
  - It naturally works on the browser.
  - Easy to lazy-load modules.
- Cons:
  - Complex, easy to create race conditions.
  - Not guaranteed order of execution of modules.
  - Dependencies array gets hard to read easily.

----

## AMD (Require.js)

- Export your module interface with *define()*
- Import on the client using *require()*
```javascript
  // zoo.js
  define('zoo', [], function() {
    let getBarkStyle = isHowler => isHowler ? 'woooooow!' : 'woof, woof!';
    let Dog = function (name, breed) {
      this.name = name;
      this.bark = getBarkStyle(breed === 'husky');
    };
    let Wolf = function (name) {
      this.name = name;
      this.bark = getBarkStyle(true);
    };
    return {
      Dog: Dog,
      Wolf: Wolf
    };
  });
```

----

## AMD

```javascript
// client.js
require(['zoo'], function(Zoo) {
  let myDog = new Zoo.Dog('Sherlock', 'beagle');
  console.log(`${myDog.name}: ${myDog.bark}`); // Sherlock: woof, woof!
});
```

<p class="center">[view on Plunker](http://plnkr.co/edit/VVmdjt?p=preview)</p>

---

## ES2015 Modules

- ES2015 offers native Modules which are quite a bit similar to CommonJS.
```javascript
  // zoo.js
  let getBarkStyle = isHowler => isHowler ? 'woooooow!' : 'woof, woof!';

  export function Dog(name, breed) {
    this.name = name;
    this.bark = getBarkStyle(breed === 'husky');
  };
  export function Wolf(name) {
    this.name = name;
    this.bark = getBarkStyle(true);
  };
```

----

## ES2015 Modules

```javascript
// client.js
import { Dog, Wolf } from './zoo';

console.log(`${myDog.name}: ${myDog.bark}`); // Sherlock: woof, woof!
```

<p class="center">[view on Plunker](http://plnkr.co/edit/yxkn9i?p=preview)</p>

- ES2015 Modules are not yet natively supported by browsers & Node.js.
- You can use it right now with a transpiler as [Babel.js](https://babeljs.io/).
- Consider a module loader such as [System.js](https://github.com/systemjs/systemjs), which supports AMD, CommonJS, ES2015 and global.

---

## Challenge 2

- Fork from [http://bit.ly/2e4jfGw](http://bit.ly/2e4jfGw) (ES2015 setup).
- Copy over the *`main.js`* from your Challenge 1 solution or [http://bit.ly/2e6nKmu](http://bit.ly/2e6nKmu)
- Break *`main.js`* down in 3 ES2015 modules, each module in a separated file:
  - *`location.js`*
  - *`getCurrentWeather.js`*
  - *`main.js`*
- When exporting/importing a single item:
  - Export: *`export default Module`*
  - Import: *`import Module from ...`*

---

## Observer/Event

- *Behavioral* pattern
- Purpose: to reduce coupling between objects, when an object (*Subject*) maintains a list of other objects depending on it (*Observers*), automatically notifying them of any changes to state/behavior.
- Pre-condition: a list of Observer objects interested in a specific Subject object.
- Post-condition: the Subject will be able to notify its Observers all at once.

----

## Observer/Event

<img src="img/observer.svg" class="diagram">

----

## Observer/Event

- Scenario: DOM click event

```javascript
// Add Dog button is the Subject
let addDogButton = document.getElementById('addDogButton');

// Print Another Dog function is the Observer
let printAnotherDog = e => {
  let content = document.getElementById('content');
  content.innerHTML += 'Sherlock: woof, woof!<br/>';
}

// Registering the Observer with the 'click' event of the subject
addDogButton.addEventListener('click', printAnotherDog);
```

<p class="center">[view on Plunker](http://plnkr.co/edit/8e3B9E?p=preview)</p>

----

## Observer/Event

- Scenario: DOM custom event
- Any DOM element can trigger custom events:

```javascript
// My Square is the Subject
let mySquare = document.getElementById('mySquare');

// Registering the Observer with the custom event of the Subject
mySquare.addEventListener('move', moveMySquare);

// Dispatching the custom event on the Subject
let customEvent = new Event('move');
mySquare.dispatchEvent(customEvent);
```

<p class="center">[view on Plunker](https://plnkr.co/edit/n7OPiG?p=preview)</p>

----

## Observer/Event

- Scenario: Object events
- There are many implementations as [Node.js EventEmitter](https://nodejs.org/api/events.html), [jQuery Events](http://www.clock.co.uk/blog/use-jquery-events-on-plain-javascript-objects), [Backbone.Events](http://backbonejs.org/#Events)...

```javascript
// My Square is the Subject
let mySquare = new MySquare('mySquare');

// Registering the Observer with the custom event of the Subject
mySquare.on('move', moveMySquare);

// Dispatching the custom event on the Subject
mySquare.emit('move', left, top);
```

<p class="center">[view on Plunker](https://plnkr.co/edit/YNFCzo?p=preview)</p>

----

## Challenge 3

- Fork from your Challenge 2 ot [http://bit.ly/2eEKBF2](http://bit.ly/2eEKBF2).
- We will replace the callback to *`Location.getCurrentWeather`* to an *Observer*:
  - Modify *`Location`* to extend from *`EventEmitter`*
  - Remove the callback from *`Location.getCurrentWeather`*
  - Instead of passing *`printFn`*, write you own callback that will *`emit`* your custom event, passing along the *`response`* received.
  - On *`main.js`*, register *`printToDOM`* as an observer for each instance of *`Location`*.

---

## Learn more

- [Check out the examples code](https://github.com/tiagorg/design-patterns-examples)
- [Javascript Patterns - Stoyan Stefanov](http://shop.oreilly.com/product/9780596806767.do)
- [Learning JavaScript Design Patterns - Addy Osmani](http://addyosmani.com/resources/essentialjsdesignpatterns)
- [The mind-boggling universe of JavaScript Module strategies](https://www.airpair.com/javascript/posts/the-mind-boggling-universe-of-javascript-modules)

---

# Questions?

<img src="img/questions.jpg" />

---

# Thanks!

- Special thanks to Avenue Code, Women Who Code crew and most importantly all the attendees!
- Talk: [tiagorg.com/talk-design-patterns-for-javascript-featuring-modules](http://tiagorg.com/talk-design-patterns-for-javascript-featuring-modules)
- Github: [github.com/tiagorg/talk-design-patterns-for-javascript-featuring-modules](https://github.com/tiagorg/talk-design-patterns-for-javascript-featuring-modules)
- More talks at [tiagorg.com](http://tiagorg.com)
- Follow me at [@tiagooo_romero](https://twitter.com/tiagooo_romero)
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

## Avenue Code

<p class="ac-column-logo">
  <img src="img/ac-logo-big.png" class="ac-logo-big" />
</p>

<ul class="ac-column-text">
  <li>Offices in San Francisco, São Paulo, Belo Horizonte</li>
  <li>Primary verticals: <br/>Retail & Financial services</li>
  <li>Partners with MuleSoft, Adobe, Chef, Oracle and AWS</li>
  <li>Project Management, Business Analysis, Development, QA, DevOps, Coaching</li>
  <li>*[www.avenuecode.com/careers](https://www.avenuecode.com/carreers)*</li>
</ul>

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
    - Observer aka Custom Event
    - Mediator aka Pub-Sub

---

## Patterns

- Pattern:
    - Proven *solution* to a certain class of *problems* in a specific *context*.
    - Pre-conditions -> requirements.
    - Post-conditions -> consequences.
- Pattern *language*: a common vocabulary.
    - *Network* of connected patterns -> reuse.

----

## Design Patterns

- Design Patterns address common OOP problems.
- 1994: *Gang Of Four* publishes [Design Patterns](http://www.amazon.com/Design-Patterns-Object-Oriented-Professional-Computing/dp/0201634988), a big watershed in the history of OOP and the debut of the term.
   - Features 23 classic design patterns, categorized in *creational*, *structural* and *behavioral*.
   - Many people believe that "Design Patterns" term is exclusive to those 23 patterns, which is not the case.

----

## Design Patterns

- Back in 1994, programming languages' native support to Design Patterns was hazy.
- Nowadays, many of those patterns have been incorporated into programming languages and frameworks.

----

## Remember Uncle Ben!

<img src="img/uncle-ben.png">

---

## JS Design Patterns

- JS code is more complex today than ever before.
   - Dazzling experience & interaction
   - ASYNC (AJAX/AJAJ) & Callbacks / Promises
   - Single Page Applications
- Good reasons to use Design Patterns in JS:
   - Avoid "spaghetti code" (unstructured/hard to read).
   - Better overall maintainability, making it clear where to change the code.
   - Far more objective unit tests.
- Today we are covering some of the work of [Stoyan Stefanov](https://twitter.com/stoyanstefanov) and [Addy Osmani](https://twitter.com/addyosmani).


---

## Constructor

- A special method used to initialize a newly created object once memory has been allocated.
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
- Returns a the new instance, no need for *return* as in a function.
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
<p class="center">[source code](https://github.com/tiagorg/design-patterns-examples/blob/master/constructor/basic.js)</p>

----

## Prototype

- Defining functions in the constructor is not ideal, as a new function will be defined for each new instance.
- By writing on the function's *prototype*, all instances can share functions and attributes.
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
<p class="center">[source code](https://github.com/tiagorg/design-patterns-examples/blob/master/constructor/prototype.js)</p>

---

## Fa&ccedil;ade

- Provides a convenient higher-level interface to a component, hiding its complexity and simplifying the API.
- Example: printing out a JSON from the server
```javascript
  // Vanilla JS
  var xhr = new XMLHttpRequest();
  xhr.onreadystatechange = function() {
    if (this.readyState == XMLHttpRequest.DONE && this.status === 200) {
      console.log(JSON.parse(this.responseText));
    }
  };
  xhr.open('GET', 'http://swapi.co/api/planets/1/', true);
  xhr.send();

  // jQuery
  $.getJSON('http://swapi.co/api/planets/1/', function(data) {
     console.log(data);
  });
```

----

## Fa&ccedil;ade

```javascript
var factorial = (function() {
  var intermediateResults = [0, 1];
  function calculate(n, stats) {
    if (intermediateResults[n] === undefined) {
      stats.ops++;
      intermediateResults[n] = n * calculate(n - 1, stats);
    }
    return intermediateResults[n];
  }
  return function(n) {
    var stats = { ops: 0 },
        result = (n > 0 && n % 1 === 0)? calculate(n, stats): 0;
    return n + '! = ' + result + ' (' + stats.ops + ' operations)';
  };
})(); // IIFE
```

```javascript
console.log(factorial(18)); // 18! = 6402373705728000 (17 operations)
console.log(factorial(20)); // 20! = 2432902008176640000 (2 operations)
```
<p class="center">[source code](https://github.com/tiagorg/design-patterns-examples/blob/master/facade/factorial.js)</p>

---

## Module

- Applies classic OO ideas for building reusable components by supporting  private/public functions and attributes in objects.
- Encapsulation achieved through *closures* -> function scope.
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

----

## Module

```javascript
var myDog = new Zoo.Dog('Sherlock', 'beagle');
console.log(myDog.name + ': ' + myDog.bark); // Sherlock: woof, woof!

var myWolf = new Zoo.Wolf('Werewolf');
console.log(myWolf.name + ': ' + myWolf.bark); // Werewolf: woof, woof!
```
<p class="center">[source code](https://github.com/tiagorg/design-patterns-examples/blob/master/module/basic.js)</p>

----

## Revealing Module

- Flexibility to switch items from private to public scope.
```javascript
  var Zoo = (function() {
    var getBarkStyle = function(isHowler) {
      return isHowler? 'woooooow!': 'woof, woof!';
    };
    var Dog = function(name, breed) {
      this.name = name;
      this.bark = getBarkStyle(breed === 'husky');
    };
    var Wolf = function(name) {
      this.name = name;
      this.bark = getBarkStyle(true);
    };
    return {
      Dog: Dog,
      Wolf: Wolf
    };
  })(); // IIFE
```
<p class="center">[source code](https://github.com/tiagorg/design-patterns-examples/blob/master/module/revealing.js)</p>

----

## Why to use Modules?

- Global JS is bad for performance, reusability, readability, side-effects and code organization.
- Literal objects' attributes and functions are all public.
- A module can be delivered as a dependency for other module.
- Modules can be packaged and deployed separately from each other, mitigating the "butterfly effect".
- Modules bring cohesion up and coupling down.

----

## Module standards

- On the previous example, *Zoo* is a global variable, which is:
  - fragile (any posterior code can modify/redefine it)
  - not scalable (what if you define 100 modules?)
  - counter-productive (you have to manually resolve your dependencies)
- You can write your own module loader!
  - Container for module registration under aliases
  - Dependency injection
  - Factory for instantiation
- OR you can just use a Module standard!

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

- Export your module interface assigning to *module.exports*.
- Import on the client using *require(dependency)*.
```javascript
  // zoo.js
  var getBarkStyle = function(isHowler) {
    return isHowler? 'woooooow!': 'woof, woof!';
  };
  var Dog = function(name, breed) {
    this.name = name;
    this.bark = getBarkStyle(breed === 'husky');
  };
  var Wolf = function(name) {
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
var Zoo = require('./zoo');

var myDog = new Zoo.Dog('Sherlock', 'beagle');
console.log(myDog.name + ': ' + myDog.bark); // Sherlock: woof, woof!

var myWolf = new Zoo.Wolf('Werewolf');
console.log(myWolf.name + ': ' + myWolf.bark); // Werewolf: woof, woof!
```
<p class="center">[source code zoo.js](https://github.com/tiagorg/design-patterns-examples/blob/master/module/zoo.js)</p>
<p class="center">[source code client.js](https://github.com/tiagorg/design-patterns-examples/blob/master/module/client.js)</p>

---

## AMD

- A standard for *asynchronous* modules.
- Pros:
  - Multiple modules can be loaded in parallel.
  - It naturally works on the browser.
  - Easy to do lazy-loading of modules.
- Cons:
  - Complex, easy to create race conditions.
  - Not guaranteed order of execution of modules.
  - Dependencies array declaration gets bloated easily.

----

## AMD

- On *Require.js*, export your module interface using *define()*.
- Import on the client using *require()*.
```javascript
  // zoo.js
  define('zoo', [], function() {
    var getBarkStyle = function (isHowler) {
      return isHowler? 'woooooow!': 'woof, woof!';
    };
    var Dog = function (name, breed) {
      this.name = name;
      this.bark = getBarkStyle(breed === 'husky');
    };
    var Wolf = function (name) {
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
  var myDog = new Zoo.Dog('Sherlock', 'beagle');
  console.log(myDog.name + ': ' + myDog.bark); // Sherlock: woof, woof!

  var myWolf = new Zoo.Wolf('Werewolf');
  console.log(myWolf.name + ': ' + myWolf.bark); // Werewolf: woof, woof!
});
```

----

## Interop?

- CommonJS and AMD are not naturally compatible!
- Some approaches to perform interop: *UMD*, *SystemJS* and *uRequire*.
- *Almond*: AMD implementation for both sync and async loading.

---

## ES6 Modules

- ES6 got approved on June and is getting rolled out as we speak.
- ES6 offers native Modules which are quite a bit similar to CommonJS.
```javascript
  // zoo.js
  var getBarkStyle = function(isHowler) {
    return isHowler? 'woooooow!': 'woof, woof!';
  };
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

## ES6 Modules

```javascript
// client.js
import { Dog, Wolf } from './zoo';

var myDog = new Dog('Sherlock', 'beagle');
console.log(myDog.name + ': ' + myDog.bark); // Sherlock: woof, woof!

var myWolf = new Wolf('Werewolf');
console.log(myWolf.name + ': ' + myWolf.bark); // Werewolf: woof, woof!
```

- ES6 modules will support both synchronous and asynchronous loading within the same syntax.
- Will work the same way both on the browser and on the server!
- You can use it right now with a good transpiler as [Babel.js](https://babeljs.io/).

---

## Observer/Custom Event

- A *Subject* object maintains a list of interested *Observer* objects, automatically notifying them of its changes.

<img src="img/observer.svg" class="diagram">

----

## Observer/Custom Event

```javascript
// subject.js
var Subject = function() {
  this.observers = [];
};

Subject.prototype.notify = function(message) {
  this.observers.forEach(function(observer) {
    observer.call(observer, message);
  });
};

Subject.prototype.addObserver = function(observer) {
  this.observers.push(observer);
};

module.exports = Subject;
```

----

## Observer/Custom Event

```javascript
var Subject = require('./subject');
var ball = new Subject();
var human = new Subject();

// register the animals (observers)
var dog = function(message) {
  if (message === 'ball') {
    console.log('The dog ran after the ' + message);
  }
};
ball.addObserver(dog); human.addObserver(dog);

var cat = function(message) {
  console.log('The cat looked and ignored the ' + message);
};
ball.addObserver(cat); human.addObserver(cat);
```

----

## Observer/Custom Event

```javascript
// throw a ball (subject)
console.log('Throwing a ball...');
ball.notify('ball');
// The dog ran after the ball
// The cat looked and ignored the ball

// throw a human (subject)
console.log('Throwing a human...');
human.notify('human');
// The cat looked and ignored the human
```
<p class="center">[source code subject.js](https://github.com/tiagorg/design-patterns-examples/blob/master/observer/subject.js)</p>
<p class="center">[source code client.js](https://github.com/tiagorg/design-patterns-examples/blob/master/observer/client.js)</p>

- Notifier is called -> all the observers will execute.
- *Reduced coupling:* Observers and Subject can live without each other, but the Subject has references to the Observers.

---

## Mediator/Pub-Sub

- Exposes an unified interface through which the different parts of a system may communicate.
- Observer can cause Garbage Collection issues: if removing a Subject, you must remember to remove all the Observer references from it.
- A *Mediator* can completely decouple Subject and Observers by introducing a intermediate layer in between.

----

## Mediator/Pub-Sub

<img src="img/mediator.svg" class="diagram">

----

## Mediator/Pub-Sub

```javascript
// mediator.js
var channels = {};
var subscribe = function(channel, subscriber) {
  if (!channels[channel]) channels[channel] = [];
  channels[channel].push(subscriber);
};
var publish = function(channel, message){
  if (!channels[channel]) return;
  channels[channel].forEach(function(subscriber) {
    subscriber.call(subscriber, message);
  });
};

module.exports = {
  subscribe: subscribe,
  publish: publish
};
```

----

## Mediator/Pub-Sub

```javascript
// pets.js
var mediator = require('./mediator');

// register the animals (subscribers to 'pets')
var dog = function(message) {
  if (message === 'ball') {
    console.log('The dog ran after the ' + message);
  }
};
mediator.subscribe('pets', dog);

var cat = function(message) {
  console.log('The cat looked and ignored the ' + message);
};
mediator.subscribe('pets', cat);
```

----

## Mediator/Pub-Sub

``` javascript
// client.js
require('./pets');

var mediator = require('./mediator');

// throw a ball (publish to 'pets')
console.log('Throwing a ball...');
mediator.publish('pets', 'ball');
// The dog ran after the ball
// The cat looked and ignored the ball

// throw a human (publish to 'pets')
console.log('Throwing a human...');
mediator.publish('pets', 'human');
// The cat looked and ignored the human
```
<p class="center">[source code mediator.js](https://github.com/tiagorg/design-patterns-examples/blob/master/mediator/mediator.js)</p>
<p class="center">[source code pets.js](https://github.com/tiagorg/design-patterns-examples/blob/master/mediator/pets.js)</p>
<p class="center">[source code client.js](https://github.com/tiagorg/design-patterns-examples/blob/master/mediator/client.js)</p>

----

## Mediator/Pub-Sub

- Same Observers, same event triggering.
- The Mediator is required on both sides.
- Observers and Subject hold no references to each other.
- The Mediator is on top of the control. It is serving as a Pub-Sub infrastructure for those components.

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
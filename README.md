<!--

WARNING!! DON'T EDIT THE FILE README.md on the root of the project, that one is a GENERATED FILE!

You should just edit the source file at src/README.md - the one which stars with ## Design patterns for JavaScript

-->

## Design patterns for JavaScript
### Featuring Modules
<br/><br/><br/><br/>

[Tiago Garcia](https://twitter.com/tiagooo_romero) @ [DevCon5](http://www.html5report.com/conference/newyork/)

*http://tiagorg.com*

July 21st, 2015

---

## Tiago Garcia

<img src="http://www.gravatar.com/avatar/5cac784a074b86d771fe768274f6860c?s=250" class="avatar">

- Tech Manager at [Avenue Code](http://www.avenuecode.com) 
- Tech Lead at [Macys.com](http://www.macys.com).
- Organizer of the [Backbone.js Hackers meetup in SF](http://www.meetup.com/Backbone-js-Hackers).
- Has spoken on [HTML5DevConf 2014](http://html5devconf.com/speakers/tiago_romerogarcia.html).

---

## Agenda

- Patterns and Design Patterns
- JavaScript Design Patterns
    - Constructor
    - Fa&ccedil;ade
    - Module
      - CommonJS
      - AMD
      - ES6
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
   - Avoid "spaghetti code" (unstructured and hard to read).
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
      return this.name + ': woof, woof, woof!';
    };
  }
   
  var myDog = new Dog('Sherlock', 'beagle');
  console.log(myDog.bark());
```

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

[See it live on Plunker!](http://plnkr.co/edit/RxgtEUzah6DmC1jnlcim?p=preview)

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
  console.log(myDog.bark); // woof, woof!

  var myWolf = new Zoo.Wolf('Werewolf');
  console.log(myWolf.bark); // woooooow!
```

----

## Revealing Module

- Adds flexibility to switch methods and variables back and forth from public to private scope.
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
  - fragile (any posterior code can modify/redefine your module)
  - not scalable (what if you need to define 100 modules?)
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

- Export your module interface assigning to *module.exports*
- Import on the client using *require(dependency)* 
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
// main.js
var Zoo = require('./zoo');

var myDog = new Zoo.Dog('Sherlock', 'beagle');
console.log(myDog.bark); // woof, woof!

var myWolf = new Zoo.Wolf('Werewolf');
console.log(myWolf.bark); // woooooow!
```

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
// main.js
require(['zoo'], function(Zoo) {
  var myDog = new Zoo.Dog('Sherlock', 'beagle');
  console.log(myDog.bark); // woof, woof!

  var myWolf = new Zoo.Wolf('Werewolf');
  console.log(myWolf.bark); // woooooow!
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
// main.js
import { Dog, Wolf } from './zoo';

var myDog = new Dog('Sherlock', 'beagle');
console.log(myDog.bark; // woof, woof!

var myWolf = new Wolf('Werewolf');
console.log(myWolf.bark; // woooooow!
```

- ES6 modules will support both synchronous and asynchronous loading within the same syntax. 
- Will work the same way both on the browser and on the server!

---

## Observer/Custom Event

- A Subject object maintains a list of interested Observer objects, automatically notifying them of its changes.
```javascript
  // Observers aka Listeners
  var consoleObserver = function(event, msg) {
    if (event === 'goal') {
      console.log(msg.team + ' team just scored! ' + msg.score);
    }
  }
    
  var htmlObserver = function(event, msg) {
    if (event === 'goal') {
      $('h3').text(msg.team + ' team just scored! ' + msg.score);
    }
  }
```

----

## Observer/Custom Event

```javascript
  // Subject aka Event Emitter
  var soccerMatchSubject = (function(teamA, teamB) {
    var scoreTable = {},
        observers = [];

    scoreTable[teamA] = scoreTable[teamB] = 0;   

    function notify() {
      var args = arguments;
      $.each(observers, function(index, observer) {
        observer.callback.apply(observer.context, args);
      });
    }

    //... continues on the next slide
```

----

## Observer/Custom Event

```javascript
  //... from the previous slide
  return {

    addObserver: function(fn) {
      observers.push({ context: this, callback: fn });
    },

    // notifier method
    goal: function(team) {      
      scoreTable[team]++;
      score = teamA + ' ' + scoreTable[teamA] + ' x ' + 
              scoreTable[teamB] + ' ' + teamB;
            
      notify('goal', { team: team, score: score });
    }
  };

})('Brazil', 'Germany');
```

----

## Observer/Custom Event

- Now we just need to add the observers to listen.
```javascript
  soccerMatchSubject.addObserver(consoleObserver);
  soccerMatchSubject.addObserver(htmlObserver);
```
- Notifier method is called -> all its observers will execute.
- *Reduced coupling:* the observers and subject do not have hard dependencies on each other.

<br/>
[See it live on Plunker!](http://plnkr.co/edit/zOYp5LUINc3GzylhxrZH?p=preview)

---

## Mediator/Pub-Sub

- Exposes an unified interface through which the different parts of a system may communicate.
- Using *Observer* reduces the coupling, but notice how the Subject still hold references to the Observers.
- Potential Garbage Collection issues: you must remember to remove the Observer references from all the Subjects.
- A *Mediator* can completely decouple Subject and Observers by introducing a intermediate layer in between.

----

## Mediator/Pub-Sub

```javascript
  var mediator = {
    channels: {},
    subscribe: function(channel, fn) {
      if (!mediator.channels[channel]) mediator.channels[channel] = [];
      mediator.channels[channel].push({ context: this, callback: fn });
    },
    publish: function(channel){
      if (!mediator.channels[channel]) return;
      var args = Array.prototype.slice.call(arguments, 1);
      $.each(mediator.channels[channel], function(index, subscriber) {
        subscriber.callback.apply(subscriber.context, args);
      });
    },      
    installTo: function(publisher){
      publisher.subscribe = mediator.subscribe;
      publisher.publish = mediator.publish;
    }
  };
```

----

## Mediator/Pub-Sub

```javascript
  var soccerMatchSubject = (function(teamA, teamB) {
    var scoreTable = {};
    scoreTable[teamA] = scoreTable[teamB] = 0;
    
    return {
      goal: function(team) {      
        scoreTable[team]++;
        score = teamA + ' ' + scoreTable[teamA] + ' x ' + 
                scoreTable[teamB] + ' ' + teamB;
                  
        this.publish('loggers', 'goal', { team: team, score: score });
      }
    };
  })('Brazil', 'Germany');
    
  mediator.installTo(soccerMatchSubject);
```

----

## Mediator/Pub-Sub

- Again, we just need to add the observers to listen.
```javascript
  soccerMatchSubject.subscribe('loggers', consoleObserver);
  soccerMatchSubject.subscribe('loggers', htmlObserver);
```
- Same Observers, same event triggering.
- Now the Mediator is on top of the control. It is serving as a Pub-Sub infrastructure for components.

<br/>
[See it live on Plunker!](http://plnkr.co/edit/ZZHMn9?p=preview)

---



---

## Conclusion

- Design Patterns provide recommended solutions for common problems in OOP.
- Javascript badly needs that to avoid "spaghetti code".
- Patterns as Module, *Fa&ccedil;ade and Mediator can be combined in a solid solution for Large-Scale JS.
- It is very important to avoid coupling at all costs in order to leverage scalability and maintainability.

---

## Learn more

- [Javascript Patterns - Stoyan Stefanov](http://shop.oreilly.com/product/9780596806767.do)
- [Learning JavaScript Design Patterns - Addy Osmani](http://addyosmani.com/resources/essentialjsdesignpatterns)
- [The mind-boggling universe of JavaScript Module strategies](https://www.airpair.com/javascript/posts/the-mind-boggling-universe-of-javascript-modules)

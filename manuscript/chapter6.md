# Chapter 6. This in JavaScript

## Demystifying "this"

The `this` keyword in JavaScript is a thick mystery for beginners and a constant crux for more experience developers. `this` in fact is a moving target, something that could change during code execution, without any apparent reason. But let's see for a moment how the `this` keyword looks like in other programming languages. First, here's a JavaScript class:

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log("Hello " + this.name);
  }
}
```

We know from chapter 5 that there are no classes in JavaScript yet `this` appears like a reference to the class itself. Also, there seems to be a parallel in Python classes called `self`:


```python
class Person:
    def __init__(self, name):
        self.name = name
    
    def greet(self):
        return 'Hello' + self.name
```

In a Python class `self` represent the class's instance: i.e. the new object that gets created starting from the class:

```python
me = Person('Valentino')
```

There is something similar in PHP too:

```php
class Person {
    public $name; 

    public function __construct($name){
        $this->name = $name;
    }

    public function greet(){
        echo 'Hello ' . $this->name;
    }
}
```

Here `$this` is the actual class instance. If we take again our JavaScript class for creating two new objects from it we can see that whenever I call `object.name` I get the correct string back:

```js
class Person {
  constructor(name) {
    this.name = name;
  }

  greet() {
    console.log("Hello " + this.name);
  }
}

const me = new Person("Valentino");
console.log(me.name); // 'Valentino'

const you = new Person("Tom");
console.log(you.name); // 'Tom'
```

Seems to make sense. JavaScript is like Python, Java, PHP and `this` appears to point to the actual class instance? Not really. Let's not forget that JavaScript is not an object oriented language, plus it's really permissive and dynamic, and there are no real classes. `this` has nothing to do with classes and I can prove the point with a simple JavaScript function (try it a browser):

```js
function whoIsThis() {
  console.log(this);
}

whoIsThis();
```

What's the output? Spoiler: when a JavaScript function runs in the so called global context `this` will be a reference to said global. And when running in a browser the global points to `window`. As you will see JavaScript functions have always a link to some context object: it is an object in which the function is running. The link is not fixed: it can change by accident or could be altered on purpose. But the reassuring fact is that there are still rules for determining where `this` should point to. Let's break them down.

## Rule number 1: falling back to the global "this" (aka default binding)

If you run the following code in a browser:

```js
function whoIsThis() {
  console.log(this);
}

whoIsThis();
```

you'll see the following output:

```js
Window {postMessage: ƒ, blur: ƒ, focus: ƒ, close: ƒ, parent: Window, …}
```

Surprising? We're not inside any class and `this` is still there. That's because `this` is always present in regular functions and could assume many forms depending on how the function is called. When a function is called in the global environment it will always point its `this` to the global object, `window` in our case. That's the rule number 1 of `this` in JavaScript and it's called **default binding**. Default binding is like a fallback and most of the times it's undesirable. Any function running in the global environment can "pollute" global variables and wreck havoc to your code. Consider the following example:

```js
function firstDev() {
  window.globalSum = function(a, b) {
    return a + b;
  };
}

function nastyDev() {
  window.globalSum = null;
}

firstDev();
nastyDev();
var result = firstDev();
console.log(result);

// Output: undefined
``` 

The first developer creates a global variable named `globalSum` and assigns a function to it. Later on another developer assigns `null` to the same variable causing malfunction in the code. Messing with global variables is always risky and for this reason JavaScript gained a "safe mode" years ago: strict mode. Strict mode is enabled by putting `"use strict";` at the top of every JavaScript file. Strict mode has many positive effects and one of them is the neutralization of the default binding. In strict mode `this` is `undefined` when trying to access it from the global context:

```js
"use strict";

function whoIsThis() {
  console.log(this);
}

whoIsThis();

// Output: undefined
```

Strict mode makes your JavaScript code more secure and (almost) free from silly bugs like that. So to recap, default binding is the first rule of `this` in JavaScript: when the engine can't figure out what `this` is it falls back to the global object. But that's only half of the story because there are 3 other rules for `this`. Let's see them together.

## Rule number 2: when "this" is my host object (aka implicit binding)

"Implicit binding" is an intimidating term, but the theory behind it is not so complicated. It all narrows down to objects. It's no surprise that JavaScript objects can contain functions:

```js
var widget = {
  items: ["a", "b", "c"],
  printItems: function() {
    console.log(this.items);
  }
};
```

When a function is assigned as an object's property then that object becomes the "home" in which the function runs. In other words the `this` keyword inside the function will point automatically to that object. That's rule number 2 of `this` in JavaScript and goes under the name of **implicit binding**. Implicit binding is in action even when we call a function in the global context:

```js
function whoIsThis() {
  console.log(this);
}

whoIsThis();
```

You can't tell from the code but the JavaScript engine assigns the function to a new property on the global object `window`. Under the hood it's like:

```js
window.whoIsThis = function() {
  console.log(this);
};
```

You can easily confirm the assumption. Run the following code in a browser:

```js
function whoIsThis() {
  console.log(this);
}

console.log(typeof window.whoIsThis)
```

and you'll get back "function". And this point you might be asking: what's the real rule for `this` inside a global function? It's called default binding but in reality is more like of an implicit binding. Confusing right? It's JavaScript after all! Just remember that the JavaScript engine always falls back to the global `this` when in doubt about the context (default binding). On the other hand when a function is defined inside a JavaScript object and is called as part of that object then there is no other way around: `this` refers to the host object (implicit binding). And now let's see the third rule for `this` in JavaScript.

## Rule number 3: tell me how is "this" (aka explicit binding)

There is no JavaScript developer that at some point in her/his career didn't see code like this:

```js
someObject.call(anotherObject);
Someobject.prototype.someMethod.apply(someOtherObject);
```

That's **explicit binding** in action. Another example of binding: with the rise of React as the UI library of choice it is common to see class methods "bound" to the class itself (more on that later):

```js
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.state = { text: "" };
    // bounded method
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(() => {
      return { text: "PROCEED TO CHECKOUT" };
    });
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.text || this.props.text}
      </button>
    );
  }
}
```

Nowadays React hooks make classes almost unnecessary, yet there are a lot of "legacy" React components out there using ES2015 classes. One of the question most beginners ask is why we re-bind event handler methods in React with "bind"? Those three methods, `call`, `apply`, `bind` belong to `Function.prototype` and are used for the so called **explicit binding** (rule number 3). It's in the name: explicit binding means taking a function and forcing its home, also called context object, to be one provided by you. But why would I explicitly bind or re-bind a function? Consider some legacy JavaScript code wrote by a fictional, less JavaScript-savvy colleague some years ago:

```js
var legacyWidget = {
  html: "",
  init: function() {
    this.html = document.createElement("div");
  },
  showModal: function(htmlElement) {
    var newElement = document.createElement(htmlElement);
    this.html.appendChild(newElement);
    window.document.body.appendChild(this.html);
  }
};
```

`showModal` is a "method" bound to the object `legacyWidget`. It is implicitly bound (rule number 2), almost useless because apparently there is no way to change the HTML element (`this.html`) on which the modal should append the new element. `this.html` is hardcoded inside the method. No worries, we can resort to explicit binding for altering the `this` object on which `showModal` runs. It is a job for `call` which takes the new context object and a list of optional arguments. Now we can create a new "shiny" widget and provide a different HTML element as the starting point:

```js
var legacyWidget = {
  html: "",
  init: function() {
    this.html = document.createElement("div");
  },
  showModal: function(htmlElement) {
    var newElement = document.createElement(htmlElement);
    this.html.appendChild(newElement);
    window.document.body.appendChild(this.html);
  }
};

var shinyNewWidget = {
  html: "",
  init: function() {
    // A different HTML element
    this.html = document.createElement("section");
  }
};
```

At this point you can run `call` on the original method:

```js
var legacyWidget = {
  html: "",
  init: function() {
    this.html = document.createElement("div");
  },
  showModal: function(htmlElement) {
    var newElement = document.createElement(htmlElement);
    this.html.appendChild(newElement);
    window.document.body.appendChild(this.html);
  }
};

var shinyNewWidget = {
  html: "",
  init: function() {
    this.html = document.createElement("section");
  }
};

// Initialize the new widget with a different HTML element
shinyNewWidget.init();

// Run the original method with a new context object
legacyWidget.showModal.call(shinyNewWidget, "p");
```

If you're still confused about explicit binding think of it like a primitive style for reusing code. It looks hacky and buggy to me but that's the best you can do if you've got legacy JavaScript code to refactor. Also, the prototype system is a better candidate for structuring the above code but believe me, bad software like that still exists in production today. At this point you may wonder what `apply` and `bind` are. `apply` has the same effect of using `call` except that the former accepts an array of parameters while the latter expects the parameters to be given as a list. In other words `call` works with a list of parameters:

```js
var obj = {
  version: "0.0.1",
  printParams: function(param1, param2, param3) {
    console.log(this.version, param1, param2, param3);
  }
};

var newObj = {
  version: "0.0.2"
};

obj.printParams.call(newObj, "aa", "bb", "cc");
```

While `apply` expects an array of parameters:

```js
var obj = {
  version: "0.0.1",
  printParams: function(param1, param2, param3) {
    console.log(this.version, param1, param2, param3);
  }
};

var newObj = {
  version: "0.0.2"
};

obj.printParams.apply(newObj, ["aa", "bb", "cc"]);
```

And what about `bind`? That's the most powerful method for binding functions. `bind` still takes a new context object for a given function but it does not just call the function with the new context object. It returns a new function bound to that object permanently:

```js
var obj = {
  version: "0.0.1",
  printParams: function(param1, param2, param3) {
    console.log(this.version, param1, param2, param3);
  }
};

var newObj = {
  version: "0.0.2"
};

var newFunc = obj.printParams.bind(newObj);

newFunc("aa", "bb", "cc");
```

A common use case for `bind` is a permanent re-binding of an original function:

```js
var obj = {
  version: "0.0.1",
  printParams: function(param1, param2, param3) {
    console.log(this.version, param1, param2, param3);
  }
};

var newObj = {
  version: "0.0.2"
};

obj.printParams = obj.printParams.bind(newObj);

obj.printParams("aa", "bb", "cc");
```

From now on `obj.printParams` will always refer `newObj` as the object in which the function is running. At this point should be clear why we re-bind class methods in React with `bind`:

```js
class Button extends React.Component {
  constructor(props) {
    super(props);
    this.state = { text: "" };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(() => {
      return { text: "PROCEED TO CHECKOUT" };
    });
  }

  render() {
    return (
      <button onClick={this.handleClick}>
        {this.state.text || this.props.text}
      </button>
    );
  }
}
```

But the reality is more nuanced and has something to do with "lost binding". When we assign an event handler as a prop to a React element the method is passed as a reference, not as a function. To speak in terms of "vanilla" JavaScript it's like passing the event handler reference inside another callback:

```js
    // BINDING LOST!
    const handleClick = this.handleClick;
    //
    element.addEventListener("click", function() {
      handleClick();
    });
```

The assignment operation breaks the binding. In the example component above the method `handleClick` (assigned to a button element) tries to update the component's state by calling `this.setState()`. When called, the method has already lost its binding which is no longer the class itself: now its context object is the `window` global object. At that point you get the dreaded "TypeError: Cannot read property 'setState' of undefined". React components are most of the times exported as ES2015 modules: `this` is undefined because ES modules use strict mode by default, thus disabling the default binding. Strict mode is enabled also for ES2015 classes and `this` is undefined even when not exporting the component as an ES module. You can test by yourself with a simple class which mimics a React component. There is a `setState` method called by `handleClick` in response to a click event:

```js
class ExampleComponent {
  constructor() {
    this.state = { text: "" };
  }

  handleClick() {
    this.setState({ text: "New text" });
    alert(`New state is ${this.state.text}`);
  }

  setState(newState) {
    this.state = newState;
  }

  render() {
    const element = document.createElement("button");
    document.body.appendChild(element);
    const text = document.createTextNode("Click me");
    element.appendChild(text);

    const handleClick = this.handleClick;

    element.addEventListener("click", function() {
      handleClick();
    });
  }
}

const component = new ExampleComponent();
component.render();
```

The offending line of code is:

```js
const handleClick = this.handleClick;
```

Try to include the class in an HTML file:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Loosing the binding</title>
</head>
<body>

</body>
<script src="this.js"></script>
</html>
```

and click the button. Check the console and you'll see "TypeError: Cannot read property 'setState' of undefined". To solve the problem we can use `bind` for making the method stick to the right context, the class itself:

```js
  constructor() {
    this.state = { text: "" };
    this.handleClick = this.handleClick.bind(this);
  }
```

Click the button again and you'll see an alert as expected. Explicit binding is stronger than both implicit binding and default binding. With `apply`, `call`, and `bind` we can bend `this` at our own will by providing a dynamic context object to our functions. If "context object" is still too abstract for you think of it as a box in which JavaScript functions run. The box is always there, but we can change its coordinates at any time with an explicit bind.

## Rule number 4: "new" binding

In chapter 5 you saw the constructor function pattern which helps encapsulating the act of creating new objects in JavaScript:

```js
function Person(name, age) {
  this.name = name;
  this.age = age;
}

Person.prototype.greet = function() {
  console.log("Hello " + this.name);
};

var me = new Person("Valentino");
me.greet();

// Output: "Hello Valentino"
```

Here we create a blueprint for an entity named "Person". Given that blueprint we can create new objects of type Person by "constructing" them with a "new" call:

```js
var me = new Person("Valentino");
```

There are many ways to bend `this` in JavaScript, but when using `new` on a constructor function the engine has no doubts: `this` will always point to the newly created object. Any function defined on the prototype of the constructor function like the following:

```js
Person.prototype.greet = function() {
  console.log("Hello " + this.name);
};
```

will always know "who is `this`" because most of the times it will operate on the newly host object. In the following example `greet` is called on `me`:

```js
var me = new Person("Valentino");
me.greet();

// Output: "Hello Valentino"
```

Since `me` has been constructed through a constructor call there is no ambiguity about the meaning of `this`. Granted, you can still borrow `greet` from Person and run it against another object:

```js
Person.prototype.greet.apply({ name: "Tom" });

// Output: "Hello Tom"
```

As you can see `this` in JavaScript is highly flexible, but without knowing the rules on which `this` lays down you cannot make educated guesses, neither harness its true power. Long story short `this` is based on four "simple" rules, but with ES 2015 arrow functions you can actually "cheat" a bit if `this` comes to bite you. Let's see how.

## Arrow functions and "this"

As seen on chapter 2 ECMAScript 2015 introduced arrow functions (also called fat arrows). The syntax for an arrow function is convenient and concise, but again, I suggest not abusing them. However, arrow functions have a lot of interesting features. First consider a constructor function called `Post`. As soon as we create a new object from the constructor there's a Fetch request against a REST API:

```js
"use strict";

function Post(id) {
  this.data = [];

  fetch("https://jsonplaceholder.typicode.com/posts/" + id)
    .then(function(response) {
      return response.json();
    })
    .then(function(json) {
      this.data = json;
    });
}

var post1 = new Post(3);
```

The above code is in strict mode, thus default binding (falling back to the global `this`) is forbidden. Try to run the code in your browser and you'll be surprised by: "TypeError: Cannot set property 'data' of undefined at <anonymous>:11:17". Cannot set property data of undefined! Makes sense. The global `this` is `undefined` in strict mode ... wait a minute. Why our function is trying to update `window.data` instead of `post1.data`? The reason is simple: the callback triggered by Fetch runs inside the browser, so `this` points to `window`. Scary. To solve the problem we had an old trick: "that". In other words you can save a reference to `this` in a variable called `that`, but this time outside the callback function:

```js
"use strict";

function Post(id) {
  var that = this;
  this.data = [];

  fetch("https://jsonplaceholder.typicode.com/posts/" + id)
    .then(function(response) {
      return response.json();
    })
    .then(function(json) {
      that.data = json;
    });
}

var post1 = new Post(3);
```

if you run the code now `post1.data` will have our data as expected:

```js
console.log(post1.data.body);

// "et iusto sed quo iure
// voluptatem occaecati omnis eligendi aut ad
// voluptatem doloribus vel accusantium quis pariatur
// molestiae porro eius odio et labore et velit aut"
```

What if instead of this old, hacky trick we don't try to use arrow functions? One of the most interesting feature indeed is how they handle `this`. Instead of regular functions we can use arrow functions as callbacks:

```js
"use strict";

function Post(id) {
  this.data = [];

  fetch("https://jsonplaceholder.typicode.com/posts/" + id)
    .then(response => {
      return response.json();
    })
    .then(json => {
      this.data = json;
    });
}

var post1 = new Post(3);
```

Problem gone. Now `this.data` will point always at `post1`. Why so? The arrow function has `this` pointing at its enclosing environment (the official documentation and most tutorials say "its lexical scope"). In other words the arrow function doesn't care if it's running inside the `window` object. Its enclosing environment is the object `post1` and that will be its home from now on. One of the most interesting use cases for arrow functions, for sure.

## Conclusions

What's `this` in JavaScript? Well, it depends. `this` builds on four rules, called respectively: default binding, implicit binding, explicit binding, and "new" binding. Implicit binding says that when a function refers to `this` and runs as part of a JavaScript object `this` will point to that "host" object. But JavaScript functions always run inside an object, that's the case for example of any global function defined in the so called global scope. When working in a browser the global scope is `window`, the current browser's tab. In this situation any function running in global will see `this` as `window`: it's the default binding of `this`. Most of the times it's not desirable to interact with the global scope so that JavaScript offers a way to neutralize the default binding with strict mode. In strict mode any reference to the global object is undefined, effectively protecting us from silly mistakes. Besides implicit and default binding there is also "explicit binding", used for bending `this` at our own will with three methods: `apply`, `call`, and `bind`. These methods are useful for passing an explicit host object on which a given function should operate. Last but not least there is the "new" binding which takes action when an object is "constructed" through a so called constructed call. For most developers `this` is a scary thing and must be avoided at all the costs. But for those who want to dig deeper it's a powerful and flexible system for reusing your JavaScript code. Embrace it!

### Sharpen up your JavaScript skills

- Take `legacyWidget` and `shinyNewWidget` from the explicit binding section and try to refactor the code to use prototype.
- What's stronger between default and explicit binding?
- **BONUS exercise**: "Creeping into `this`" available in the [paid version](https://leanpub.com/little-javascript/)
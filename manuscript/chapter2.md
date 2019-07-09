# Chapter 2. Prologue: JavaScript fundamentals

## Strings, numbers, variables and all in between

The first draft of this book was aiming at experienced JavaScript developers who wanted to refresh their skills. I utterly skipped JavaScript fundamentals (my bad) and later on based on some precious feedback I decided to add a quick re-introduction to the language. So here we are! JavaScript builds on seven lego bricks called "types". The complete list is:

- `String`
- `Number`
- `Boolean`
- `Null`
- `Undefined`
- `Object`
- `Symbol` (part of ECMAScript 2015)

Except `Object` which is a type, everything else is also called "primitive" of the language. Every JavaScript type has a corresponding representation that can be used in our code, like strings:

```js
var string = "Hello John";
```

Numbers:

```js
var age = 33;
```

Speaking of which, JavaScript has arithmetic operations too:

| Operator | Operation |
| :-----: | :-----: | 
| `+`  | sum |
| `++`  | increment |
| `*`  | multiplication |
| `**` | exponent (part of ECMAScript 2015) |
| `-`  | subtraction |
| `--`  | decrement |
| `/`  | division |
| `%`  | modulo (returns the remainder of a division) |

Then there are booleans:

```js
var not = false;
```

and objects:

```js
var obj = {
  name: "John",
  age: 33
};
```

Values can be stored in a variable with the `var` keyword, the most compatible way for declaring variables:

```js
var greet = "Hello";
var year = 89;
var not = false;
```

I said compatible because with ECMAScript 2015 we have two other options: `let` and `const`. Older browsers may not support these new keywords and unless using a "transpiler" (see chapter 1 for terminology) you may run into errors. Besides stand-alone variables it is also possible to declare list of entities, the array:

```js
var array = ["Hello", 89, false, true];
```

Array elements are accessible by an index, starting at 0:

```js
var array = ["Hello", 89, false, true];
var first = array[0]; // Holds "Hello"
```

Almost all JavaScript entities have some functions attached, called methods. To name two, array has a lot of methods for manipulating itself:

```js
var array = ["Hello", 89, false, true];

array.push(99);
array.shift();

console.log(array); // [ 89, false, true, 99 ];
```

And the same is true for strings:

```js
var string = "John";
console.log(string.toUpperCase()); // JOHN
```

Later in chapter 5 you'll see where these methods come from, but here's a spoiler: they're defined on `Array.prototype` and `String.prototype` respectively. Alongside with methods there are also properties which are useful for extracting info about the length of a string for example:

```js
var string = "John";
console.log(string.length); // 4
```

or an array's length:

```js
var array = ["Hello", 89, false, true];

array.push(99);
array.shift();

console.log(array.length); // 4
```

These properties are somewhat special because they're called "getters"/"setters". Most JavaScript types are objects under the hood (more on this later in the book). You can imagine a given string like an object with a bunch of methods and properties attached. When accessing the length of an array you're just calling the corresponding getter. The setter function kicks in for the set operation:

```js
var array = {
  value: ["Hello", 89, false, true],
  push: function(element) {
    //
  },
  shift: function() {
    //
  },
  get length() {
    // gets the length
  },
  set length(newLen) {
    // sets the length
  }
};

// Getter call
var len = array.length

// Setter call
array.length = 50;
```

And now having laid down the fundamentals let's take a look at Object, one of the most important JavaScript type.

## Standing on the shoulders of a giant Object

`Object` is the most important type in JavaScript so that almost every other entity derives from it. Functions and array for example are specialized objects. Objects in JavaScript are container for key/value pairs, like the following example (literal form):

```js
var obj = {
  name: "John",
  age: 33
};
```

There is also another way for creating objects but it's rare and slow, avoid it:

```js
var obj = new Object({
  name: "John",
  age: 33
});
```

As you can see objects are a convenient way for holding values which can be retrieved later by accessing the corresponding property:

```js
var obj = {
  name: "John",
  age: 33
};

console.log(obj.name); // "John"
```

We can also add new properties, delete, or change them:

```js
var obj = {
  name: "John",
  age: 33
};

obj.address = "Some address";
delete obj.name;
obj.age = 89;
```

Object's keys can also be strings and in this case we access the property with bracket notation:

```js
var obj = {
  name: "John",
  age: 33,
  "complex key": "stuff"
};

console.log(obj["complex key"]); // "stuff"
```

That should cover the basics. In chapter 5 you'll see the true power of JavaScript objects.

## 50 shades of JavaScript functions

Almost every programming language has functions and JavaScript makes no exception. Functions are reusable piece of code. Consider the following examples:

```js
function hello(message) {
  console.log(message);
}

hello("Hello");
```

and

```js
function sum(a, b) {
  return a + b;
}

var sum = sum(2, 6);
```

The first function prints a string while the second returns a value to the external world. As you can see functions can accept parameters, listed in the function "signature":

```js
// a and b are parameters in the function's signature
function sum(a, b) {
  return a + b;
}
```

We can pass values when invoking the function, and in that context they take the name of arguments:

```js
// a and b are parameters in the function's signature
function sum(a, b) {
  return a + b;
}

// 2 and 6 are arguments for the function
var sum = sum(2, 6);
```

A JavaScript function declared with the `function` keyword is a regular function, opposed to arrow functions which don't have a traditional body (more on arrow functions later). Regular functions can assume many shapes:

- named function
- anonymous function
- object method
- object method shortand (ECMAScript 2015)
- IIFE (immediately invoked function expression)

Named functions are the most traditional type of function:

```js
// A named function
function sum(a, b) {
  return a + b;
}
```

Anonymous function on the other hand don't have a name and can be assigned to a variable for later use:

```js
var sum = function(a, b) {
  return a + b;
};
```

or used as callbacks (in the next chapter you'll see callbacks in depth) inside other functions:

```js
var button = document.createElement("button");

button.addEventListener("click", function(event) {
  // do stuff
});
```

Functions can also live inside JavaScript objects. If so we call the function (for good or bad) a method of that object:

```js
var widget = {
  showModal: function() {
    // do stuff
  }
};

widget.showModal();
```

Regular functions also get by default a `this` keyword which can assume different meanings depending on how the function is called. In chapter 6 we'll explore the topic in detail. For now take for granted a simple rule: a function running inside an object has `this` pointing to the host object:

```js
var widget = {
  html: "<div></div>",
  showModal: function() {
    console.log(this.html);
  }
};

widget.showModal(); // "<div></div>"
```

In ECMAScript 2015 you can also use object method shortand and write methods like so:

```js
var widget = {
  showModal() {
    // object method shortand
  }
};

widget.showModal();
```

Last but not least there are IIFE (immediately invoked function expression). An IIFE is a function which immediately calls itself:

```js
var IIFE = (function() {
  // what happens in an IIFE stays in the IIFE
})();
```

The syntax may look a bit weird, but IIFE are really powerful as you'll see them in chapter 4. Alongside with regular functions there are also arrow function, added in ECMAScript 2015. Arrow functions don't use the `function` keyword, but they come in a similar variety of shapes:

- named arrow function
- anonymous arrow function
- object method (not so useful)
- IIFE arrow function (immediately invoked function expression)

Here we'll see just a quick preview. In the next chapter we'll dive into the details. Arrow functions are convenient, but I suggest not overusing them. Here's a named arrow function to start with. We can omit the `return` statement and use parenthesis if there are no parameters:

```js
const arrow = () => console.log("Silly me");
```

and here's an anonymous arrow function passed as a callback to another function:

```js
const arr = [1, 2, 3];

const res = arr.map(element => element + 1);

console.log(res); // [ 2, 3, 4 ]
```

If there is a single parameter there is no need to put parenthesis around it. Besides that arrow functions are so different from regular functions that an entire book won't enough to cover all the details. But worry not, during the book we'll see why and when to use them effectively.

## Conclusions

COMING SOON
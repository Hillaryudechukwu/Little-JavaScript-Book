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

Later in chapter 5 you'll see where these methods come from, but here's a spoiler: they're defined on `Array.prototype` and `String.prototype` respectively.

I> ## The more you know
I>
I> JavaScript strings are immutable. String's methods always return a new string leaving the original unaltered.

Alongside with methods there are also properties which are useful for extracting info about the length of a string for example:

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

`Object` is the most important type in JavaScript so that almost every other entity derives from it. Functions and array for example are specialized objects. Objects in JavaScript are containers for key/value pairs, like the following example (literal form):

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

## Functions in JavaScript

COMING SOON
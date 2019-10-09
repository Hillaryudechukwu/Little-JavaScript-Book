# Chapter 7. Types, conversion, and comparison in JavaScript

## Primitive types in JavaScript

At the beginning of the book we saw some of the JavaScript's fundamental units like strings and numbers:

```javascript
var greet = "Hello";
var year = 89;
```

Strings and numbers are part of the so called "types" of the language, also known as "primitives" (except Object which is a type on its own). Again, the complete list is:

- String
- Number
- Boolean
- Null
- Undefined
- Object
- Symbol

Booleans represent values that could be either `true` or `false`. `null` on the other hand is the intentional absence of a value. Sometimes `null` is assigned to a variable for marking a signpost that will be populated later on.

```javascript
var maybe = null;
```

Speaking of empty values there is also `undefined`, which means that a variable has still nothing attached to it:

```javascript
var name;
console.log(name) // undefined
```

`null` and `undefined` look pretty similar so much that developers most of the times are unsure which one to use. Yet they are two distinct entities. `undefined` is everywhere in JavaScript, that is, values start off always as `undefined` when the engine executes your code.
 
If you want to find out the type of a JavaScript entity you can use the `typeof` operator. Let's try with a string:

```javascript
    typeof "alex" // "string"
```

with a number:

```javascript
    typeof 9 // "number"
```

with a boolean:

```javascript
    typeof false // "boolean"
```

on `undefined`:

```javascript
    typeof undefined // "undefined"
```

and with `null`:

```javascript
    typeof null // "object"
```

which gives us a suprising result! `null` looks like an `object` but in reality it is an historic bug in JavaScript, lying there since the language was born. JavaScript has always had a bad reputation because of these things. And that's just the beginning. There are some strange rules for converting between one type and another. Let me give you a bit of context. Let's make an example in Python. The following instruction in Python:

```python
'hello' + 89
```

gives you a clear error:

```text
TypeError: can only concatenate str (not "int") to str
```

In JavaScript instead only the sky is your limit. The following code:

```javascript
'hello' + 89
```

in fact gives:

```javascript
"hello89"
```

Things become even more weird if we try adding an array to a string. The following code:

```javascript
"hello" + []
```

gives:

```javascript
"hello"
```

and:

```javascript
"hello" + [89]
```

gives a surprising:

```javascript
"hello89"
```

Looks like there is some kind of logic behind this conversion. It works even with more crowded arrays. The following code:

```javascript
"hello" + [89, 150.156, "mike"]
```

gives:

```javascript
"hello89,150.156,mike"
```

These two lines of JavaScript are enough to make a Java developer run away. But this behaviour in JavaScript is 100% intentional. So it is worth exploring the most glaring cases of implicit conversion in JavaScript, also know as type coercion.

## When a number becomes a string

Most programming languages have a concept called type casting which means more or less: if I want to convert an entity to another type then I have to make the conversion clear-cut. It is possible in JavaScript too. Consider the following example:

```javascript
var greet = "Hello";
var year = 89;
```

If I want to convert explicitly I can signal the intention in my code, either with `toString()`:

```javascript
var greet = "Hello";
var year = 89;
var yearString = year.toString();
```

or with `String`:

```javascript
var greet = "Hello";
var year = 89;
var yearString = String(year);
```

`String` is part of the so called built-in JavaScript objects which mirror some of the primitive types: `String`, `Number`, `Boolean`, and `Object`. These built-ins can be used for converting between types. After the conversion I can concatenate the two variables:

```javascript
greet + yearString;
```

Apart from this explicit conversion, in JavaScript there is a subtle mechanic called implicit conversion, kindly offered by JavaScript engines. The language does not prevent us from adding numbers and strings as you saw:

```javascript
"hello" + 89 // "hello89"
```

What's the reasoning behind the conversion? The addition operator `+` in JavaScript converts any of the two operands to a string if at least one of them is ... a string! And you may find even more surprising that this rule is set in stone in the ECMAScript spec. [Section 11.6.1](http://www.ecma-international.org/ecma-262/5.1/#sec-11.6.1) defines the behavior of the addition operator which I'll summarize here for you own sake:

> **The addition operator (+)**
>
> If x is String or y is String then return ToString(x) followed by ToString(y)

Does this trick work only on numbers? Not really. Array and objects are subject to the same fate:

```javascript
"hello" + [89, 150.156, "mike"] 
// "hello89,150.156,mike"
```

And how about:

```javascript
"hello" + { name: "caty" }
```

To find out the solution you can do a quick test by converting the object to a string:

```javascript
String({ name: "caty" })
```

which gives:

```javascript
"[object Object]"
```

So I have a feeling that:

```javascript
'hello' + { name: "caty" }
```

will give:

```javascript
"hello[object Object]"
```

Another question remains: what happens with multiplication, division and subtraction?

## I'm not a number!

We saw how the addition operator tries to convert the operands to a string when at least one of them is a string. But what happens with the other arithmetic operators? If we use one of them (except `+`) against a type that it's not a number then we get a special kind of JavaScript object called `NaN`:

```javascript
89 ** "alex" // NaN
```

`NaN` stands for "not a number" and represents any failed arithmetic operation, like in the following code:

```javascript
var obj = { name: "Caty" } % 508897
```

which gives:

```javascript
console.log(obj) // NaN
```

Pay attention to `typeof` in combination with `NaN`. This code looks ok:

```javascript
typeof 9 / "alex" // NaN
```

But how about the following?

```javascript
var strange = 9 / "alex"
typeof strange // "number"
```

`NaN` becomes `number` when it is assigned to a variable. This opens up a new question. How can I check whether some variable is `NaN` or not? There is a method, `Number.isNaN()`:

```javascript
var strange = 9 / "alex"
Number.isNaN(strange) // true
```

Now let's point our attention to JavaScript comparison operators which are singular as their arithmetic counterparts.

## Equal equal or not?

There are two main families of comparison operators in JavaScript. First we have what I like to call "weak comparison". It's the abstract comparison operator (double equal): `==`. Then there's a "strong comparison" which you can recognize from the triple equal: `===` also called strict comparison operator. Let's see some examples. If we compare two strings with both operators we get consistent results:

```javascript
"hello" == "hello" // true
"hello" === "hello" // true
```

Everything looks fine. Now let's try to compare two different types, a number and a string. First with the "strong comparison":

```javascript
"1" === 1 // false
```

Makes sense! String `"1"` is different from number 1. But what happens with a "weak comparison"?

```javascript
"1" == 1 // true
```

True! JavaScript is saying that the two values are equal. Now it doesn't make any sense. Unless this behaviour has something to do with the implicit conversion we saw earlier. What if the same rules apply? Bingo. The ECMAScript [spec](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3) strikes again. Turns out the abstract comparison operator makes an automatic conversion between types, before comparing them. This is an abstract:

> **Abstract Equality Comparison Algorithm**
>
> The comparison x == y is performed as follows:
> if x is String and y is Number return the result of the comparison ToNumber(x) == y

The spec says: if the first operand is a string and the second operand is a number then convert the first operand to a number. Interesting. The JavaScript spec is full of these weird rules and I highly encourage you digging deeper into it. Unless you have a strong reason to do the opposite, avoid the abstract comparison operator in your JavaScript code. You will thank yourself later. And how about the "strong comparison"? The spec at [Strict Equality Comparison](http://www.ecma-international.org/ecma-262/5.1/#sec-11.9.6) says that no automatic conversion is made before comparing values with the triple equal `===`. Using the Strict Equality Comparison in your code will save yourself from silly bugs.

That's all for the so called "coercion". I won't bother you with all the crazy edge cases, there is already a huge literature online. After all this is a "little JavaScript book".

## Primitives and objects

JavaScript's type names are all capitalized: `String`, `Number`, `Boolean`, `Null`, `Undefined`, `Object` and `Symbol`. This style appears even in the [ECMAScript spec](http://www.ecma-international.org/ecma-262/5.1/#sec-8) and it might be surprising to discover that types have some "twins": the so called built-in objects. The `String` primitive for example has an equivalent `String` which is used in two ways. If called like a function (by passing an argument) it converts any value into a string:

```javascript
var someValue = 555;
String(someValue); // "555"
```

If called as a constructor with `new` instead the result is an object of type string:

```javascript
var someValue = 555;
var newString = new String(someValue);
```

It is worth inspecting the object in the console for seeing how different from a "plain" string is:

![JavaScript String Object](images/js-primitive-obj.png)

You can also use `typeof` for confirming that it is indeed an object:

```javascript
typeof newString // "object"
```

Other "mirrors" of the primitive types are `Number` which can convert (almost) any value to a number:

```javascript
var someValue = "555";
Number(someValue); // 555;
```

I said almost because you get `NaN` when trying to convert an invalid "number":

```javascript
var notValidNumber = "aa555";
Number(notValidNumber); // NaN;
```

Instead when used in the constructor form `Number` returns a new object of type number:

```javascript
var someValue = "555";
new Number(someValue); // Number {555}
```

The `Boolean` built-in behaves the same and can convert any value to a boolean when used as a function

```javascript
var convertMe = 'alex';
Boolean(convertMe); // true
```

The constructor returns an object instead (what a surprise!):

```javascript
var convertMe = 'alex';
typeof new Boolean(convertMe) // "object"
```

`Object` makes no exceptions. It returns an object of any given type if called with the appropriate argument:

```javascript
Object("hello"); // String {"hello"}
Object(1); // Number{1}
```

Instead if called without arguments it returns an empty object:

```javascript
Object() // {}
```

When calling `Object` as a constructor the result is a new object:

```javascript
new Object({
  name: "Alex",
  age: 33
});

{name: "Alex", age: 33}
```

At this point you might be asking: should I use built-in objects for "constructing" new values? As a rule of thumb avoid the constructor call when you need a simple value:

```javascript
// AVOID
var bool = new Boolean("alex");
var str = new String("hi");
var num = new Number(33);
var strObj = new Object("hello");
```

It's noisy and imposes a performance penalty because JavaScript engine nowadays do not optimize this form. You'll be better served with the literal form:

```javascript
// OK
var bool = true
var str = 'hi';
var num = 33;
var obj = {
            name: "Alex",
            age: 33
          };
```           

A good use for `Boolean`, `String`, and `Number` instead is for converting between a type and another:

```javascript
// OK FOR CONVERTING
var boolFromString = Boolean("alex");
var numFromString = Number("22");
```
    
By doing so you can also give more expressiveness to your code.

## Conclusions

There are seven building blocks in JavaScript, namely String, Number, Boolean, Null, Undefined, Object, and Symbol. JavaScript developers can manipulate these types with arithmetic and comparison operators. But you need to pay particular attention to both the addition operator `+` and the abstract comparison operator `==` which by nature tend to convert between types. This implicit conversion in JavaScript is called type coercion and is defined in the ECMAScript spec. Whenever possible in your code use always the strict comparison operator `===` instead of `==`. As a best practice always make clear when you intend to convert between one type and another. For this purpose JavaScript has a bunch of built-in objects which mirror the primitive types: `String`, `Number`, `Boolean`. Built-in can be used for converting explicitly between different types.
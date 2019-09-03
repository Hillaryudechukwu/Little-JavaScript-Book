# Chapter 11. HTML forms and playing with localStorage

## A re-introduction to HTML forms

Web pages are not only meant to display data. Thanks to HTML forms, which have been around for a long time, we can capture and manipulate user data. In this chapter you'll build a simple HTML form for capturing e-mail, name, and message from a user. Later you'll save that data to localStorage. In the process you'll learn more about DOM events, introduced in chapter 8. A `<form>` element is an HTML element which in turn may contain other sub-elements like:
 
 - one or more `<input>` for capturing user choices
 - a `<textarea>` for capturing text
 - a `<button>` for submitting the form
 
In this chapter you'll build a form containing some `<input>`, a `<textarea>` and a `<button>`. Ideally every input should have an attribute called `type` which indicates the input type, for example text, email, number, date and so on. In addition to `type` you may also want to add an `id` attribute to every form element. Later we'll see why it could be useful for retrieving the inputs. Inputs and textarea may also have a `name` attribute. The `name` attribute is important if you want to submit the form without using JavaScript. You'll see why in a minute. It's also common practice to associate every form element with a `<label>`. In the example below you'll see the labels with a `for` attribute corresponding to the `id` attribute of the associated input element. Most of the times it's also a good idea to mark form inputs as required. When an `<input>` has the `required` attribute the user cannot submit the form without filling that input. This simple form of validation is useful for avoiding empty data or for preventing the user from skipping required fields. With this knowledge you're now ready to create an HTML form. Create a new file named **form.html** and build the form:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HTML forms and JavaScript</title>
</head>
<body>
<form>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required>

    <label for="email">E-mail</label>
    <input type="email" id="email" name="email" required>

    <label for="message">Message</label>
    <textarea id="message" name="message" required></textarea>

    <button type="submit">Submit</button>
</form>
</body>
<script src="form.js"></script>
</html>
```

As you can see our form inputs have all the right attributes and from now on we can test the form by filling some data. When writing HTML forms pay particular attention to the `type` attribute. It drives not only what kind of data the user can enter, HTML5 in fact introduced form validation: for example an input of `type` email will only accept e-mail addresses with an "at" sign `@` in it. Unfortunately that's the only check which is applied to the e-mail address: nobody prevents the user from submitting the form with an e-mail like a@a. It has the `@` but it's not entirely valid. Before moving on let's tweak the form inputs. There are a lot of attributes available on `<input>`, and I find `minlength` and `maxlength` two of the most useful. `minlength` can be applied to name and textarea. It helps to stop lazy spammers who take pride from submitting forms with "aa" in the name or "testtest" in the message textarea. A sane default for `minlength` could be 5, while the minimum message length may be 10 for example:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HTML forms and JavaScript</title>
</head>
<body>
<form>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required minlength="5">

    <label for="email">E-mail</label>
    <input type="email" id="email" name="email" required>

    <label for="message">Message</label>
    <textarea id="message" name="message" required minlength="10"></textarea>

    <button type="submit">Submit</button>
</form>
</body>
<script src="form.js"></script>
</html>
```

Another useful check can be applied to the email input. I always enforce a `maxlength` of 254 which is the maximum length for an email message as defined in [RFCs 3696 and 5321](https://tools.ietf.org/html/rfc3696#page-5):

```html
    <input type="email" id="email" name="email" required maxlength="254">
```

And with the form in place we're ready to move further: let's see our form in action.

## HTML forms in action

Our HTML form is an element of type [HTMLFormElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement). As with almost any HTML element it descends from `HTMLElement`, which in turn is a descendant of `EventTarget`. Every HTML element descending from `EventTarget` is able to receive and react to DOM events (as seen on chapter 8). There are many types of DOM events like `click`, `blur`, `change`, and so on. What's interesting for you now is the `submit` event, which is distinctive of HTML forms. The `submit` event is dispatched when an HTML form has an input or a button of `type` submit. That's exactly the case with our form:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HTML forms and JavaScript</title>
</head>
<body>
<form>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required minlength="5">

    <label for="email">E-mail</label>
    <input type="email" id="email" name="email" required maxlength="254">

    <label for="message">Message</label>
    <textarea id="message" name="message" required minlength="10"></textarea>

    <button type="submit">Submit</button>
</form>
</body>
<script src="form.js"></script>
</html>
```

Some developers use an input instead of a button:

```html
<!-- I don't like it -->
<input type="submit">
```

Most of the times it depends on particular CSS constrains. In all honesty I don't like that approach and I prefer using a button whenever I can:

```html
<!-- yes please -->
<button type="submit">Submit</button>
```

Now let's get coding. You're going to write an ES2015 class for controlling our HTML form. Our goal is to intercept all the input user from the form but before doing so we need to listen for the `submit` event. Developers who will use our class should be able to call it like so:

```js
const formSelector = document.querySelector("form");

new Form(formSelector);
```

In addition to `document.querySelector("form");` the DOM offers `document.forms`, a collection of all the form inside the page. We need just the only form the page for now:

```js
const formSelector = document.forms[0];

new Form(formSelector);
```

Given a form selector we can register an event listener on it, a function which is able to react when the form is submitted. For registering the listener we can use `constructor`, called as soon as the class is "instantiated", which in turn should call another method, aptly named `init`. Create a new file named **form.js** in the same folder of **form.html** and let's start off with our simple class:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", this.handleSubmit);
  }
}
```

Our event listener is `this.handleSubmit` and as with every event listener it has access to a parameter called `event`. You should know from chapter 8 that `event` is the actual event being dispatched, carrying a lot of useful information about the HTML element who created the event. Let's implement `this.handleSubmit` then:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", this.handleSubmit);
  }

  handleSubmit(event) {
    console.log(event);
  }
}
```

Our class is ready to take orders. We can "instantiate" it:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", this.handleSubmit);
  }

  handleSubmit(event) {
    console.log(event);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

At this point you should be able to open up **form.html** in a browser, fill the inputs and click on "Submit". What happens? You shouldn't be surprised by the content of your url bar:

```text
http://localhost:63342/little-javascript/form.html?name=valentino&email=valentino%40valentinog.com&message=The+Little+JavaScript+book
```

What's going on? Most DOM events have a so called "default behaviour". The `submit` event in particular tries to send the form data to a fictitious server. That's according to the "old way" of submitting forms, typical of traditional web frameworks like Django, Rails, and friends (I'm not saying there's something wrong with that). Every input value is mapped to the corresponding `name` attribute. We're going to handle the form with JavaScript so there is no need for `name` but it's a good practice to include it anyway as a fall back. Since we want to control the form with JavaScript we'll need to disable the default behaviour by using an handy method of the [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event) interface: `preventDefault`. For that we need to fix `handleSubmit`:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", this.handleSubmit);
  }

  handleSubmit(event) {
    event.preventDefault();
    console.log(event);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

Save and close the file and then refresh **form.html**. Try to fill again the form and click Submit. You'll see the event object printed to the console:

```js
Event {...}
    bubbles: true
    cancelBubble: false
    cancelable: true
    composed: false
    currentTarget: null
    defaultPrevented: true
    eventPhase: 0
    isTrusted: true
    path: (5) [form, body, html, document, Window]
    returnValue: false
    srcElement: form
    target: form
    timeStamp: 8320.840000000317
    type: "submit"
```

Among the many properties on the event object there is `event.target` which suggests that our HTML form is saved there alongside with all the inputs. Let's see if that's really the case.

## Extracting data from an HTML form, and "this" comes to bite again

By the end of the chapter we want our JavaScript code to save form data to localStorage. For doing so we need first to extract every input value from the form. By examining `event.target` you'll find out that there's a property named `elements`. Is what we're looking for? If you try to print it with `console.log(event.target.elements);` you'll see that `elements` is an `HTMLFormControlsCollection` (it looks like an array but it's not). Bingo:

```js
    0: input#name
    1: input#email
    2: textarea#message
    3: button
    length: 4
    email: input#email
    message: textarea#message
    name: input#name
    __proto__: HTMLFormControlsCollection
```

As you can see every form input is represented in the collection. Now, we have two ways for accessing the input's value (ie. the user input):

- via an array-like notation: `event.target.elements[0].value`
- via the id: `event.target.elements.email.value`

In fact if you cared to add the appropriate `id` attribute on every form element now you can access the same element as `event.target.elements.id` where id is the string you assigned earlier. Since `event.target.elements` is first of all internally an object we can also use ES2015 object destructuring:

```js
const { name, email, message } = event.target.elements;
```

This isn't 100% recommended, in TypeScript you'll get an error but you'll be fine as long as you write "vanilla JS". Having the values in hand now we can complete `handleSubmit` and while we're at it let's also provide another method named `saveMessage` which can send the actual request to the API. For now it will just print the values to the console:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", this.handleSubmit);
  }

  handleSubmit(event) {
    event.preventDefault();
    const { name, email, message } = event.target.elements;
    this.saveMessage({
      name: name.value,
      email: email.value,
      message: message.value
    });
  }

  saveMessage(payload) {
    console.log(payload);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

Try to submit the form again and see what happens:

```js
Uncaught TypeError: this.saveMessage is not a function
    at HTMLFormElement.handleSubmit (form.js:16)
```

Looks like `saveMessage` is not a function yet I'm sure it's defined inside the class. What's not defined in reality is `this`. It comes to bite us again! If you remember from chapter 6, ES2015 classes are in strict mode by default. That's the reason for the undefined `this`. What JavaScript is really telling us is "I tried to access `this` but I felt back to the global object". Can you name it? It's default binding in action. But why `handleSubmit` is losing its reference to `this`? Because by the time it runs it's a function running in the `window` object which is global. Worry not, there are two solutions to our problem. Solution number 1, pass `handleSubmit` to an arrow function:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", event =>
      this.handleSubmit(event)
    );
  }

  handleSubmit(event) {
    event.preventDefault();
    const { name, email, message } = event.target.elements;
    this.saveMessage({
      name: name.value,
      email: email.value,
      message: message.value
    });
  }

  saveMessage(payload) {
    console.log(payload);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

Arrow functions have `this` pointing at their enclosing environment (lexical scope). In other words the arrow function above passed as an event handler doesn't care if it's running inside `window`. Its enclosing environment is the object created with `new Form` and that will be its home from now on. And how about solution number 2? It's based on the [class fields proposal](https://github.com/tc39/proposal-class-fields) which makes possible to use arrow functions as object's methods:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", this.handleSubmit);
  }

  handleSubmit = event => {
    event.preventDefault();
    const { name, email, message } = event.target.elements;
    this.saveMessage({
      name: name.value,
      email: email.value,
      message: message.value
    });
  };

  saveMessage(payload) {
    console.log(payload);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

This way `handleSubmit` can remain unaltered as the event handler, while its definition becomes an arrow function inside the class body. Not every developer likes the above syntax, also, it's still unsupported on most browsers. Your mileage may vary but if you want my advice stick with the following code:

```js
"use strict";

class Form {
  constructor(formSelector) {
    this.formSelector = formSelector;
    this.init();
  }

  init() {
    this.formSelector.addEventListener("submit", event =>
      this.handleSubmit(event)
    );
  }

  handleSubmit(event) {
    event.preventDefault();
    const { name, email, message } = event.target.elements;
    this.saveMessage({
      name: name.value,
      email: email.value,
      message: message.value
    });
  }

  saveMessage(payload) {
    console.log(payload);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

And now let's turn our attention to localStorage, it's where our form data will be saved soon.

## Interacting with localStorage

TODO

## Cleaning up the form

COMING SOON

## Conclusions

TODO

### Sharpen up your JavaScript skills

- What's the default behaviour of HTML forms? It's possible to disable it?
- Is there a way to capture HTML form values from a JavaScript function?
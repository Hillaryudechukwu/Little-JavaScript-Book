# Chapter 10. HTML forms and playing with localStorage

## A re-introduction to HTML forms

Web pages are not only meant to display data. Thanks to HTML forms (they've been around for a long time) we can catch and manipulate user data. In this chapter you'll build a simple HTML form for capturing some notes. Later on you'll save that data to the browser. In the process you'll learn more about DOM events, introduced in chapter 8. A `<form>` element is an HTML element which in turn may contain other sub-elements like:
 
 - `<input>` for capturing data
 - `<textarea>` for capturing text
 - `<button>` for sending the form
 
In this chapter you'll build a form containing some `<input>`, a `<textarea>` and a `<button>`. Ideally every input should have an attribute called `type` indicating the input type: for example text, email, number, date and so on. In addition to `type` you may also want to add an `id` attribute to every form element. Later we'll see it turning useful for retrieving data from each input. Input and textarea may also have a `name` attribute. The `name` attribute is important if you want to send the form without JavaScript. More on that in a minute. It's also common practice to associate every form element with a `<label>`. In the example below you'll see each label carry a `for` attribute bond to the `id` attribute of the related input element. Sounds confusing? Don't worry. Most of the times it's also a good idea to mark form inputs as required. A user won't be able to submit the form without filling all the required info. It's a simple validation for avoiding empty data, thus preventing the user from skipping important fields. With this knowledge you're now ready to create an HTML form. Create a new file named **form.html** and build the HTML:


```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HTML forms and JavaScript</title>
</head>
<body>
<h1>What's next?</h1>
<form>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required>

    <label for="description">Short description</label>
    <input type="text" id="description" name="description" required>

    <label for="task">Task</label>
    <textarea id="task" name="tak" required></textarea>

    <button type="submit">Submit</button>
</form>
</body>
<script src="form.js"></script>
</html>
```

As you can see our form inputs have all the right attributes and from now on we can test the form by filling some data. When writing HTML forms pay particular attention to the `type` attribute ([here's why](https://cloudfour.com/thinks/an-html-attribute-potentially-worth-4-4m-to-chipotle/)). It drives what kind of data the user is able to enter. HTML5 also introduced form validation: for example an input of type `email` will only accept e-mail addresses with an "at" sign `@` in it. Unfortunately that's the only check applied to an e-mail address: nobody prevents the user from typing an e-mail like a@a. It has the `@` but it's still invalid (the `pattern` attribute for email inputs can help to mitigate the issue). Before moving on let's tweak the form inputs. There are a lot of attributes available on `<input>`, and I find `minlength` and `maxlength` two of the most useful. In a real app they can stop lazy spammers from sending forms with "aa" or "testtest". A sane default for `minlength` could be 5, while the minimum task length might be 10 (your mileage may vary):

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HTML forms and JavaScript</title>
</head>
<body>
<h1>What's next?</h1>
<form>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required minlength="5">

    <label for="description">Short description</label>
    <input type="text" id="description" name="description" required minlength="5">

    <label for="task">Task</label>
    <textarea id="task" name="tak" required minlength="10"></textarea>

    <button type="submit">Submit</button>
</form>
</body>
<script src="form.js"></script>
</html>
```

And with the form in place we're ready to move further: let's see our form in action.

## HTML forms in action

Our HTML form is an element of type [HTMLFormElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLFormElement). As with almost any HTML element it is connected to `HTMLElement`, which in turn is connected to `EventTarget`. Here's again the prototype in action. DOM elements are represented as JavaScript objects when we reach for them. Try this in a browser:

```javascript
const aForm = document.createElement("form");
console.log(typeof aForm);
```

The output is "object", while entities like `HTMLElement` or `EventTarget` are functions:

```javascript
console.log(typeof EventTarget); // "function"
```

So if any HTML element is connected to `EventTarget` it means that `<form>` is an "instance" of `EventTarget`? Try by yourself:

```javascript
const aForm = document.createElement("form");
console.log(aForm instanceof EventTarget);
```

The result yields "true". The form is a specialized kind of `EventTarget`. Every `EventTarget` can receive and react to DOM events (as seen on chapter 8). There are many types of DOM events like `click`, `blur`, `change`, and so on. What's interesting for you now is the `submit` event, distinctive of HTML forms. The `submit` event is dispatched when the user clicks on an input or on a button of type "submit" (the element must appear inside the form). That's exactly our case:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>HTML forms and JavaScript</title>
</head>
<body>
<h1>What's next?</h1>
<form>
    <label for="name">Name</label>
    <input type="text" id="name" name="name" required minlength="5">

    <label for="description">Short description</label>
    <input type="text" id="description" name="description" required minlength="5">

    <label for="task">Task</label>
    <textarea id="task" name="task" required minlength="10"></textarea>

    <button type="submit">Submit</button>
</form>
</body>
<script src="form.js"></script>
</html>
```

Notice that `<button type="submit">Submit</button>` is right inside the form. Some developers use an input instead of a button:

```html
<!-- I don't like it -->
<input type="submit">
```

Most of the times it depends on particular CSS constrains. In all honesty I don't like the "input" approach. A button is fine for me:

```html
<!-- yes please -->
<button type="submit">Submit</button>
```

Now let's get coding. Our goal is to intercept all the user input on the form but before doing so we need to listen for the `submit` event. Again, we'll structure our code in a class. Class consumers should be able to call it like so:

```javascript
const formSelector = document.querySelector("form");

new Form(formSelector);
```

The DOM also offers `document.forms`, a collection of all the form inside the page. We need just the only one for now:

```javascript
const formSelector = document.forms[0];

new Form(formSelector);
```

Now here's the idea: given a form selector we can register an event listener on it for reacting when the form is sent. For registering the listener we can use `constructor`, and make it call a method named `init`. Create a new file named **form.js** in the same folder of **form.html** and start off with a simple class:

```javascript
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

Our event listener is `this.handleSubmit` and as with every event listener it has access to a parameter called `event`. You should know from chapter 8 that `event` is the actual event being dispatched, carrying a lot of useful information about the action itself. Let's implement `this.handleSubmit`:

```javascript
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

```javascript
const formSelector = document.forms[0];

new Form(formSelector);
```

At this point you should be able to open up **form.html** in a browser. Fill the inputs and click on "Submit". What happens? You shouldn't be surprised by the content of your url bar:

```text
http://localhost:63342/little-javascript/code/ch10/form.html?name=Valentino&description=Trip+to+Spoleto&tak=We%27re+going+to+visit+the+city%21
```

What's going on? Most DOM events have a so called "default behaviour". The `submit` event in particular tries to send the form data to a fictitious server. That's how you send a form without JavaScript, when it is part of an application based on web frameworks like Django, Rails, and friends. Every input value is mapped to the corresponding `name` attribute. There would be no need for `name` in our case but it's a good practice to include it as a fall back. Since we want to control the form with JavaScript we need to disable the default behaviour. We can fix `handleSubmit` by calling `preventDefault`, a method of the [Event](https://developer.mozilla.org/en-US/docs/Web/API/Event) interface:

```javascript
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

Save the file and refresh again **form.html**. Try to fill the form and click Submit. You'll see the event object printed to the console:

```text
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

Among the many properties on the event object there is also `event.target`, suggesting that our HTML form is saved there alongside with all the inputs. Let's see if that's really the case.

## Extracting data from an HTML form

By the end of the chapter we want our JavaScript code to persists some data. For doing so we need first to extract every input value from the form. By examining `event.target` you'll find out that there's a property named `elements`. Is what we're looking for? If you try to print it with `console.log(event.target.elements)` you'll see:

```text
    0: input#name
    1: input#description
    2: textarea#task
    3: button
    length: 4
    description: input#description
    name: input#name
    tak: textarea#task
    task: textarea#task
```

You can notice every form input in the collection. Now, we have two ways for accessing the input's value (ie. the user input):

- via the array-like notation: `event.target.elements[0].value`
- via the id: `event.target.elements.some_id.value`

In fact if you cared to add the appropriate `id` attribute on every form element now you can access the same element as `event.target.elements.some_id` where id is the string you assigned to the attribute. Since `event.target.elements` is first of all an object we can also use ECMAScript 2015 object destructuring:

```javascript
const { name, description, task } = event.target.elements;
```

This isn't 100% recommended, for example in TypeScript you'll get an error but as long as we write "vanilla JS" we're fine. Having the values in hand now we can complete `handleSubmit` and while we're at it let's also create another method named `saveData`. For now it will just print the values to the console:

```javascript
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
    const { name, description, task } = event.target.elements;
    this.saveData({
      name: name.value,
      description: description.value,
      task: task.value
    });
  }

  saveData(payload) {
    console.log(payload);
  }
}

const formSelector = document.forms[0];

new Form(formSelector);
```

Wait a moment. It's not the smartest implementation ever. What if the fields change? Now we have name, description, and task but we may add more inputs in the future. We need to extract those fields dynamically. Screw object destructuring for now and let's peek into `event.target.elements`:

```text
    0: input#name
    1: input#description
    2: textarea#task
    3: button
    length: 4
    description: input#description
    name: input#name
    tak: textarea#task
    task: textarea#task
```

It looks like an array. What if I could transform it into another array containing just name, description, and task (minus the button type submit)? Let's iterate over `event.target.elements` with the [map method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/map) (here's just the relevant bit of code):

```javascript
  handleSubmit(event) {
    event.preventDefault();
    const inputList = event.target.elements.map(function(formInput) {
      if (formInput.type !== "submit") {
        return formInput.value;
      }
    });

    /*
      TODO this.saveData( maybe inputList ?)
     */
  }
```

Give it a shot in the browser and look at the console:

```text
Uncaught TypeError: event.target.elements.map is not a function
    at HTMLFormElement.handleSubmit (form.js:15)
```

Bad news. ".map is not a function". So what's `event.target.elements` really? It seems like an array yet it's a different beast: it is an `HTMLFormControlsCollection`. You got a sneak peak of this stuff in chapter 8 where you saw that some DOM methods return an `HTMLCollection`.

```javascript
// Returns an HTMLCollection
document.chidren;
```

HTML collections may look similar to arrays but they lack methods like `map` or `filter` for iterating over its elements. It's still possible to access every element with the bracket notation but now we want to do things a bit more dynamically. ECMAScript 2015 added a method on the Array built-in object for creating an array from (almost) any source. It's `Array.from`. Let's fix again `handleSubmit`:

```javascript
  handleSubmit(event) {
    event.preventDefault();

    const arrOfElements = Array.from(event.target.elements);

    const inputList = arrOfElements.map(function(formInput) {
      if (formInput.type !== "submit") {
        return formInput.value;
      }
    });

    console.log(inputList);

    /*
      TODO this.saveData( maybe inputList ?)
     */
  }
```

Here we construct an array from `event.target.elements` that gets passed to `map`. But here's another gem. `Array.from` accepts a mapping function as the second argument. We can do all the work in a single step:

```javascript
  handleSubmit(event) {
    event.preventDefault();

    const inputList = Array.from(event.target.elements, function(formInput) {
      if (formInput.type !== "submit") {
        return formInput.value;
      }
    });

    console.log(inputList);

    /*
      TODO this.saveData( maybe inputList ?)
     */
  }
```

Refresh **form.html**, fill the form and press "Submit". You should see the following array in the console:

```javascript
["Valentino", "Trip to Spoleto", "We're going to visit the city!", undefined]
```

As a finishing touch I'd like to generate an array of objects where every object has also the name property of the related form input:

```javascript
  handleSubmit(event) {
    event.preventDefault();

    const inputList = Array.from(event.target.elements, function(formInput) {
      if (formInput.type !== "submit") {
        return {
          name: formInput.name,
          value: formInput.value
        };
      }
    });

    console.log(inputList);

    /*
      TODO this.saveData( maybe inputList ?)
     */
  }
```

Refresh **form.html** again, fill the form and you should see:

```javascript
[
  {
    "name": "name",
    "value": "Valentino"
  },
  {
    "name": "description",
    "value": "Trip to Spoleto"
  },
  {
    "name": "task",
    "value": "We're going to visit the city!"
  },
  undefined
]
```

Great job. Now let's turn our attention to localStorage.

## Interacting with localStorage, and "this" comes to bite again

TODO

## Conclusions

TODO

### Sharpen up your JavaScript skills

**More quizzes available in the [paid version](https://leanpub.com/little-javascript/)**.

- What's the default behaviour of HTML forms? It's possible to disable it?
- Is there a way to capture HTML form values from a JavaScript function?
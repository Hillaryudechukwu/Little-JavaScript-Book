# Chapter 9. Working with asynchronous JavaScript

## REST API and XMLHttpRequest

If you ask me, JavaScript is really useful. As a scripting language running in the browser it can do all sort of things like:
 
- creating elements dynamically 
- adding interactivity

and so on. In chapter 8 you built an HTML table starting from an array. An hardcoded array is a synchronous data source, that is, it's available straight away in our code. No need to wait. But as JavaScript developers most of the time we want to fetch data from the outside: on the network for example. Network communication is always an asynchronous operation, opposed to synchronous data sources: we request the data and the server "might" respond back later. JavaScript has no built-in asynchronicity on its own: it's the "host" environment (the browser or Node.j) that provides external helpers for dealing with time-consuming operations. In chapter 3 you saw `setTimeout` and `setInterval`, two browser APIs for time based work. Browsers offers a lot of API and there's one called `XMLHttpRequest` aiming specifically at making network requests. The naming is really weird: `XMLHttpRequest` sounds so '00. In fact it comes from an era when most web services exposed data in XML format. Nowadays JSON is the most popular communication "protocol" for moving data between web services but the name XMLHttpRequest eventually stuck. `XMLHttpRequest` is also part of a set of technologies called AJAX whose name stands for "Asynchronous JavaScript and XML". AJAX was born for making network requests in the browser as slick as possible. It meant the ability to fetch data from a remote source without causing a page refresh. At that time the idea was almost revolutionary. With the introduction of `XMLHttpRequest` (almost 13 years ago) the platform gained a native method for making network requests. Here's how you make one:

```js
var request = new XMLHttpRequest();

request.open("GET", "https://academy.valentinog.com/api/link/");

request.addEventListener("load", function() {
  console.log(this.response);
});

request.send();
```

In the example we:

- create a new XMLHttpRequest object
- open the request by providing a method and an url
- register the event listeners
- send the request

XMLHttpRequest is based on DOM events (see chapter 8 for a refresh), we can use either `addEventListener` or `onload` for listening on the "load" event, triggered whenever the request succeeds. For failing requests (network errors) we can register a listener on the "error" event:

```js
var request = new XMLHttpRequest();

request.open("GET", "https://academy.valentinog.com/api/link/");

request.onload = function() {
  console.log(this.response);
};

request.onerror = function() {
  // handle the error
};

request.send();
``` 

Armed with this knowledge let's put `XMLHttpRequest` to good use.

## XMLHttpRequest in action: generating lists with HTML and JavaScript

We'll build a simple HTML list after fetching data from a REST API. Create a new file named **build-list.html** in a folder of choice:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>XMLHttpRequest</title>
</head>
<body>

</body>
<script src="xhr.js"></script>
</html>
```

Next up create a file named **xhr.js** in the same folder. In this file we're going to create a new XHR request (we will write ECMAScript 2015 from now on):

```js
"use strict";

const request = new XMLHttpRequest();
```

The above call (called constructor call, check out chapter 5) creates a new object of type XMLHttpRequest. Opposed to asynchronous functions like `setTimeout`, expecting the callback as an argument:

```js
setTimeout(callback, 10000);

function callback() {
  console.log("hello timer!");
}
```

XMLHttpRequest is based on DOM events and the handler callback is registered on the `onload` object. The load event fires when the request succeeds. Follow coding with me in the same file:

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  console.log("Got the response!");
}
```

After registering the callback we can open the request with `open()`. It accepts an HTTP method (GET in our case) and an url to phone:

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  console.log("Got the response!");
}

request.open("GET", "https://academy.valentinog.com/api/link/");
```

Finally we can send the actual request with `send()`:

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  console.log("Got the response!");
}

request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
```

Open up **build-list.html** in a browser and you'll see "Got the response!" in the console. Anyway, printing a string is almost useless. We're interested in the response from the server. If you remember from chapter 6, every regular JavaScript function holds a reference to its host object. Since the callback is running inside a XMLHttpRequest object we can access the server response on `this.response`

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  // this refers to the new XMLHttpRequest
  // response is the server's response
  console.log(this.response);
}

request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
```

Save the file and refresh **build-list.html**. You should see the server's response in the console as a text document. It's still not so useful because we're interested in a JSON document rather that on its textual representation. At this point we have two options:

- option 1: configuring the response type on the XMLHttpRequest object
- option 2: using `JSON.parse()`

The first option is more hacky, I don't like strings hanging around:

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  // this refers to the new XMLHttpRequest
  // response is the server's response
  console.log(this.response);
}

// configure the response type
request.responseType = "json";
//

request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
```

Option 2 instead is more descriptive and idiomatic:

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  const response = JSON.parse(this.response);
  console.log(response);
}

request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
```

Refresh again **build-list.html** and you should see an array of JavaScript objects, each with the same shape:

```javascript
[
  //
  {
    title:
      "JavaScript Engines: From Call Stack to Promise, (almost) Everything You Need to Know",
    url: "https://www.valentinog.com/blog/engines/",
    tags: ["javascript", "v8"],
    id: 3
  }
  //
]
```

We didn't create the array by hand like we did in chapter 8. This time it's coming from a REST API. With this array we're going to populate our HTML page with a list of elements. In the process you'll also see a new feature for JavaScript classes.

## Building HTML lists with JavaScript (and debugging classes)

There are many ways for structuring JavaScript. In chapter 4 we looked at modules as of a mean to give privacy to variables and functions. In chapter 5 instead we took a look at prototype, the de facto "standard" for arranging JavaScript code. We briefly saw ECMAScript 2015 classes as well, a fancier way to write prototype based code, and in all honesty a style which is gaining a lot of traction in recent years. Here we're going to encapsulate our HTML list builder in a JavaScript class so you can make practice with them. We'll also use [private class fields](https://github.com/tc39/proposal-class-fields) (not supported in Firefox at the time of this writing). Before writing any code you may want to think "how is my class going to be used"? For example another developer could take your code and call the class by passing in:
 
- a remote URL for fetching data
- an HTML element to attach the list to

Here's the idea:

```javascript
const url = "https://academy.valentinog.com/api/link/";
const target = document.body;
const list = new List(url, target);
``` 

With these requirements in mind we can start coding our class. For now it should accept a couple of arguments in the constructor and have a dummy method for getting data:

```javascript
class List {
  constructor(url, target) {
    this.url = url;
    this.target = target;
  }

  getData() {
    return "stuff";
  }
}
```

The common wisdom in software development is that class members and methods must be not accessible from the outside, unless there is a good reason to do the opposite (Code complete 2, Good encapsulation). In JavaScript there is no native way for hiding methods and variables unless using a module (chapter 2). Not even classes are immune to information leaking but with private class fields we can finally reach for real privacy. At what cost you might ask? The syntax for private class fields is unusual. What if I tell you it's the same as Bash and Python comments? Here's how you would rewrite your class:

```javascript
class List {
  #url;
  #target;

  constructor(url, target) {
    this.#url = url;
    this.#target = target;
  }

  getData() {
    return "stuff";
  }
}
```

You might not like the syntax but private class fields do their job. Neither url nor target are accessible from the outside:

```javascript
class List {
  #url;
  #target;

  constructor(url, target) {
    this.#url = url;
    this.#target = target;
  }

  getData() {
    return "stuff";
  }
}

const url = "https://academy.valentinog.com/api/link/";
const target = document.body;
const list = new List(url, target);

console.log(list.url); // undefined
console.log(list.target); // undefined
```

It's a sane default. And with the structure in place we can move the data fetching logic inside `getData`. This time the second argument for `request.open` should be `this.#url`:

```javascript
"use strict";

class List {
  #url;
  #target;

  constructor(url, target) {
    this.#url = url;
    this.#target = target;
  }

  getData() {
    const request = new XMLHttpRequest();
    request.onload = function() {
      const response = JSON.parse(this.response);
      console.log(response);
    };

    request.open("GET", this.#url);
    request.send();
  }
}

const url = "https://academy.valentinog.com/api/link/";
const target = document.body;
const list = new List(url, target);
```

It's coming up well! Now to display our links let's add a method named `render`, right after `getData`. `render` will create an HTML list for us, starting from an array passed as a parameter:

```javascript
"use strict";

class List {
  #url;
  #target;

  constructor(url, target) {
    this.#url = url;
    this.#target = target;
  }

  getData() {
    const request = new XMLHttpRequest();
    request.onload = function() {
      const response = JSON.parse(this.response);
      console.log(response);
    };

    request.open("GET", this.#url);
    request.send();
  }

  // The new method
  render(data) {
    const ul = document.createElement("ul");
    for (const element of data) {
      const li = document.createElement("li");
      const title = document.createTextNode(element.title);
      li.appendChild(title);
      ul.appendChild(li);
    }
    this.#target.appendChild(ul);
  }
}
```

Notice `document.createElement()`, `document.createTextNode()`, and `appendChild()`. You saw them in chapter 8 when we talked about DOM manipulation. The private field `this.#target` is used here for appending the HTML list to the DOM. Now to push things a bit further I'd like to:

- call `render` after getting the JSON response
- call `getData` as soon as the user creates a new List "instance"

For doing so we'll call `render` right inside the `request.onload` callback:

```javascript
  getData() {
    const request = new XMLHttpRequest();
    request.onload = function() {
      const response = JSON.parse(this.response);
      // Call render after getting the response
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }
```

`getData` on the other hand should run inside the constructor:

```javascript
  constructor(url, target) {
    this.#url = url;
    this.#target = target;
    // Call getData as soon as the class is used
    this.getData();
  }
```

Here's the complete code:

```javascript
"use strict";

class List {
  #url;
  #target;

  constructor(url, target) {
    this.#url = url;
    this.#target = target;
    this.getData();
  }

  getData() {
    const request = new XMLHttpRequest();
    request.onload = function() {
      const response = JSON.parse(this.response);
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }

  render(data) {
    const ul = document.createElement("ul");
    for (const element of data) {
      const li = document.createElement("li");
      const title = document.createTextNode(element.title);
      li.appendChild(title);
      ul.appendChild(li);
    }
    this.#target.appendChild(ul);
  }
}

const url = "https://academy.valentinog.com/api/link/";
const target = document.body;
const list = new List(url, target);
```

Give it a shot: refresh **build-list.html** in the browser and take a look at the console:

```javascript
Uncaught TypeError: this.render is not a function
```

`this.render` is not a function! What could it be? At this point you may want to reach for chapter 6 or better, you can debug the code. Right after `this.render(response)` in `getData` add the instruction `debugger`:

```javascript
  getData() {
    const request = new XMLHttpRequest();
    request.onload = function() {
      const response = JSON.parse(this.response);
      debugger;
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }
```

`debugger` adds a so called breakpoint and the execution will stop there. Now open up the browser console and refresh **build-list.html**. Here's what you'll see (in Chrome):

PICTURE

Take a closer look at the "Scopes" tab. Inside `getData` there is indeed a `this`, but it points to XMLHttpRequest. In other words we're trying to access  `this.render` on the wrong object. We meant `this.render` as the "render method on the new List object" (remember, classes are just factory functions for creating objects). Why `this` is not matching? It's because the callback passed to `request.onload` runs in an host object of type XMLHttpRequest, the result of calling `const request = new XMLHttpRequest()`. Are we lost at this point? Not yet. Do you remember arrow functions? In chapter 6 we saw they can help in this kind of situations. What if instead of a regular function we use an arrow function as the callback for `request.onload`? Let's try:

```javascript
  getData() {
    const request = new XMLHttpRequest();
    // The arrow function in action
    request.onload = () => {
      const response = JSON.parse(this.response);
      debugger;
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }
```

Refresh **build-list.html** and check it out:

```javascript
Uncaught SyntaxError: Unexpected token u in JSON at position 0
```

Fair enough. The previous error is gone but now there's a problem with `JSON.parse`. We can easily imagine it has something to do with `this`. Move the debugger a line up:

```javascript
  getData() {
    const request = new XMLHttpRequest();
    request.onload = () => {
      debugger;
      const response = JSON.parse(this.response);
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }
``` 

Refresh **build-list.html** and look again at Scopes in the browser console. Now `response` is `undefined` because the `this` we're trying to access is `List`. That's coherent with the behaviour of arrow functions and classes (classes are by default in strict mode). So what't the fix now? You should know from chapter 8, DOM and events, that every callback passed as an event listener has access to an `event` object. Inside that event object there is also a property named `target`, pointing to the object that fired the event. Bingo! We can access the response on `event.target.response`:

```javascript
  getData() {
    const request = new XMLHttpRequest();
    request.onload = event => {
      const response = JSON.parse(event.target.response);
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }
```

Here's the complete code:

```javascript
"use strict";

class List {
  #url;
  #target;

  constructor(url, target) {
    this.#url = url;
    this.#target = target;
    this.getData();
  }

  getData() {
    const request = new XMLHttpRequest();
    request.onload = event => {
      const response = JSON.parse(event.target.response);
      this.render(response);
    };

    request.open("GET", this.#url);
    request.send();
  }

  render(data) {
    const ul = document.createElement("ul");
    for (const element of data) {
      const li = document.createElement("li");
      const title = document.createTextNode(element.title);
      li.appendChild(title);
      ul.appendChild(li);
    }
    this.#target.appendChild(ul);
  }
}

const url = "https://academy.valentinog.com/api/link/";
const target = document.body;
const list = new List(url, target);
```

Refresh **build-list.html** again and everything will work as expected! You should see a list of links in the page! Make sure to remove `debugger` after you catch those pesky bugs. Alternately you can place breakpoints in the source code directly from the browser console. Now let's move on to explore the evolution of XMLHttpRequest: Fetch.

## Asynchronous evolution: from XMLHttpRequest to Fetch

Often overlooked in favor of libraries like Axios, the Fetch API is a native browser method for making AJAX requests. Fetch was born in 2015 side by side with ECMAScript 2015 and the new Promise object. On the other hand AJAX, a set of technologies for fetching data in the browser existed since 1999. Nowadays we take AJAX and Fetch for granted but few know that Fetch is nothing more than a "glorified version" of XMLHttpRequest. Fetch is more concise than a typical XMLHttpRequest request and more important is based on Promises. Here's a simple request:

```javascript
fetch("https://academy.valentinog.com/api/link/").then(function(response) {
  console.log(response);
});
```

If you run it in a browser the console will print a Response object. Depending of the type of content being requested you'll need to convert it to JSON while returning the data:

```javascript
fetch("https://academy.valentinog.com/api/link/").then(function(response) {
  return response.json();
});
```

Contrary to what you may think that call alone doesn't return the actual data. Since `response.json()` returns a Promise as well, one more step is required for getting the JSON payload:

```javascript
fetch("https://academy.valentinog.com/api/link/")
  .then(function(response) {
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  });
```

While Fetch is a lot more convenient and clean than XMLHttpRequest it has a lot of idiosyncrasies. For example you must pay particular attention in [checking the response for errors](https://www.tjvantoll.com/2015/09/13/fetch-and-errors/). In the next section you'll learn more about it while rebuilding Fetch from scratch.

## Rebuilding the Fetch API from scratch (for fun and profit)

As a JavaScript developer most of the times you will just "consume" a Promise created by a library author. That means most developers know about Promises because they've used `then` and `catch` as consumers of other people code. But that's only half of the story. Library creators on the other hand use Promise for wrapping legacy code and offering a more modern API to developers. And that's exactly what we're going to do: we'll create a so called polyfill for Fetch. To start off create a new file named **fetch.html** and save it in a folder of choice:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Building Fetch from scratch</title>
</head>
<body>

</body>
<script src="fetch.js"></script>
</html>
```

Then create another file named **fetch.js** in the same folder and start with the following code:

```js
"use strict";

window.fetch = null;
```

In the first line we ensure we're in strict mode and in the second line we "nullify" the original Fetch API. Now we can start building our own Fetch API. But first let's reverse engineer the real Fetch. The way Fetch works is quite simple. It takes an url and makes a GET request against it:

```js
fetch("https://academy.valentinog.com/api/link/").then(function(response) {
  console.log(response);
});
```

When a function is "chainable" with `then` it means it's returning a Promise. So inside **fetch.js** let's create a function named `fetch`, which takes an url and returns a new Promise. For creating a Promise you can call the Promise constructor and pass in a callback taking resolve and reject:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    // do stuff
  });
}
```

Let's make a smoke test (it should work): 

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    resolve("Fake response!");
  });
}
```

Now try the complete code:

```js
"use strict";

window.fetch = fetch;

function fetch(url) {
  return new Promise(function(resolve, reject) {
    resolve("Fake response!");
  });
}

fetch("https://academy.valentinog.com/api/link/").then(function(response) {
  console.log(response);
});
```

You should get "Fake response!" inside the console. It works! Granted, it's still a useless Fetch because nothing gets returned from the API. Let's implement the real behaviour, with some help from XMLHttpRequest. We already saw how to create a request with XMLHttpRequest:

```js
const request = new XMLHttpRequest();
request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
request.onload = function() {
  console.log(this.response);
};
```

For maximum compatibility we're going to use the legacy object `onload`. What we're going to do now is wrap XMLHttpRequest inside our Promise. Open up **fetch.js** and extend the fetch function to include a new XMLHttpRequest:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // handle the response
    };
    request.send();
  });
}
```

Things start to make sense now, isn't it? We can also register an handler for failures on the object `onerror`:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // handle the response
    };
    request.onerror = function() {
      // handle the error
    };
    request.send();
  });
}
```

At this point it's a matter of resolving or rejecting the Promise depending on the request's fate. That means resolving the Promise inside `onload` with the response we've got from the server:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      resolve(this.response);
    };
    request.onerror = function() {
      // handle the error
    };
    request.send();
  });
}
```

In case of error we reject the Promise inside `onerror`:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      resolve(this.response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    request.send();
  });
}
```

Rejected Promises are handled by the `catch` handler, that means you'll be able to use your "fake" Fetch like so:

```js
fetch("https://acdemy.valentinog.com/api/link/")
  .then(function(response) {
    console.log(response);
  })
  .catch(function(error) {
    console.log(error);
  });
```

Now in case of an incorrect url our Fetch will print "Network error!" to the console. If the url is correct you'll get the response from the server:

![The textual response from the server](images/rebuilding-fetch-server-response.png)

Fantastic! Our Fetch polyfill works but there are some rough edges. First of all we need to implement a function for returning JSON. The real Fetch API yields a response which can be later converted to JSON, blob or text, like so (for the scope of this exercise we'll implement only the JSON function):

```js
fetch("https://academy.valentinog.com/api/link/")
  .then(function(response) {
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
```

It should be easy to implement the functionality. Seems that "response" might be an entity on its own with a json() function attached. The JavaScript prototype system is a good candidate for structuring the code (check out chapter 5). Let's create a constructor function named Response and a method bound to its prototype (in **fetch.js**):

```js
function Response(response) {
  this.response = response;
}

Response.prototype.json = function() {
  return JSON.parse(this.response);
};
```

That's all. Now we can use Response inside Fetch:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // resolve(this.response);
      // After:
      const response = new Response(this.response);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    request.send();
  });
}
```

Here's the complete code so far:

```js
window.fetch = fetch;

function Response(response) {
  this.response = response;
}

Response.prototype.json = function() {
  return JSON.parse(this.response);
};

function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // resolve(this.response);
      // After:
      const response = new Response(this.response);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    request.send();
  });
}

fetch("https://academy.valentinog.com/api/link/")
  .then(function(response) {
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
  .catch(function(error) {
    console.log(error);
  });
```

The above code should print an array of objects into the browser's console. And now let's take care of errors. The real version of Fetch is much more complicated than our polyfill but it's not so difficult to replicate the same exact behaviour. The Response object in Fetch has a property, a boolean called "ok". That boolean is set to true when the request succeeds and to false when the request fails. A developer can check the boolean and alt the Promise handler by throwing an error. Here's how you would check the status with the real Fetch:

```js
fetch("https://academy.valentinog.com/api/link/")
  .then(function(response) {
    if (!response.ok) {
      throw Error(response.statusText);
    }
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
  .catch(function(error) {
    console.log(error);
  });
```

As you can see there's also a "statusText". Seems easy to implement both "ok" and "statusText" in the Response object. In particular `response.ok` should be true when the server replies:

- with a status code equal or less than 200
- with a status code smaller than 300

Open up **fetch.js** and tweak the Response object to include the new properties. Also make sure to change the first line to read the response from `response.response`:

```js
function Response(response) {
  this.response = response.response;
  this.ok = response.status >= 200 && response.status < 300;
  this.statusText = response.statusText;
}
```

And guess what there's no need to make up "statusText" because it's already returning from the original XMLHttpRequest response object. That means we only need to pass the entire response when constructing our custom Response:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // var response = new Response(this.response);
      // After: pass the entire response
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    request.send();
  });
}
```

Here's the complete code again:

```js
"use strict";

window.fetch = fetch;

function Response(response) {
  this.response = response.response;
  this.ok = response.status >= 200 && response.status < 300;
  this.statusText = response.statusText;
}

Response.prototype.json = function() {
  return JSON.parse(this.response);
};

function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // var response = new Response(this.response);
      // After: pass the entire response
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    request.send();
  });
}

fetch("https://academy.valentinog.com/api/link/")
  .then(function(response) {
    if (!response.ok) {
      throw Error(response.statusText);
    }
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
  .catch(function(error) {
    console.log(error);
  });
```

As of now our Fetch polyfill is able to handle errors and success. You can check the error handling by providing a failing url like so:

```js
fetch("https://httpstat.us/400")
  .then(function(response) {
    if (!response.ok) {
      throw Error(response.statusText);
    }
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
  .catch(function(error) {
    console.log(error);
  });
```

You should see "Error: bad request" in the browser's console. Way to go! And now let's extend our polyfill for making POST requests. First of all let's see how to make POST requests with the real Fetch. The entity we're going to create on the server is a "link" with a title and an url:

```js
const link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};
```

Next up we configure the request for Fetch. It's done in an object which should contain at least the HTTP method, the request's content type (JSON in our case) and the request's body (the new link):

```js
const link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};

const requestInit = {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(link)
};
```

Finally we make the request like so:

```js
const link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};

const requestInit = {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(link)
};

fetch("https://academy.valentinog.com/api/link/create/", requestInit)
  .then(function(response) {
    if (!response.ok) {
      throw Error(response.statusText);
    }
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
  .catch(function(error) {
    console.log(error);
  });
```

But now we have a problem with our polyfill. It accepts a single parameter "url" and makes only GET requests against it:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    request.send();
  });
}
```

Fixing the issue should be easy. First we can accept a second parameter named requestInit. Then based on that parameter we can construct a new Request object that:

- makes GET requests by default
- uses body, method, and headers from requestInit if is provided

To start off make a new Request function with some properties named body, method, and headers. Open up **fetch.js** and get coding:

```js
function Request(requestInit) {
  this.method = requestInit.method || "GET";
  this.body = requestInit.body;
  this.headers = requestInit.headers;
}
```

Next up we're going to use the new function inside our polyfill. We prepare a request configuration object just before opening the request:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    const requestConfiguration = new Request(requestInit || {});
    // more soon
  });
}
```

Now the request will use the method defined in the Request object:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    const requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    // more soon
  });
}
```

The logic for handling errors and success (load) remains the same:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    const requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    // more soon
  });
}
```

But on top of that we can add a minimal logic for setting up request headers:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    const requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    // Set headers on the request
    for (const header in requestConfiguration.headers) {
      request.setRequestHeader(header, requestConfiguration.headers[header]);
    }
    // more soon
  });
}
```

`setRequestHeader` is available out of the box on the XMLHttpRequest object. Headers are important for configuring the AJAX request. Most of the times you may want to set application/json or an authentication token in the headers. As the cherry on top we can complete our code to send: 

- an empty request if there is no body
- a request with some payload is the body is provided

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    const requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    // Set headers on the request
    for (const header in requestConfiguration.headers) {
      request.setRequestHeader(header, requestConfiguration.headers[header]);
    }
    // If there's a body send it
    // If not send an empty GET request
    requestConfiguration.body && request.send(requestConfiguration.body);
    requestConfiguration.body || request.send();
  });
}
``` 

That should cover the POST request feature. Here's the complete code for our Fetch polyfill so far:

```js
"use strict";

window.fetch = fetch;

function Response(response) {
  this.response = response.response;
  this.ok = response.status >= 200 && response.status < 300;
  this.statusText = response.statusText;
}

Response.prototype.json = function() {
  return JSON.parse(this.response);
};

function Request(requestInit) {
  this.method = requestInit.method || "GET";
  this.body = requestInit.body;
  this.headers = requestInit.headers;
}

function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    const request = new XMLHttpRequest();
    const requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      const response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    for (const header in requestConfiguration.headers) {
      request.setRequestHeader(header, requestConfiguration.headers[header]);
    }
    requestConfiguration.body && request.send(requestConfiguration.body);
    requestConfiguration.body || request.send();
  });
}

const link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};

const requestInit = {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(link)
};

fetch("https://academy.valentinog.com/api/link/create/", requestInit)
  .then(function(response) {
    if (!response.ok) {
      throw Error(response.statusText);
    }
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
  .catch(function(error) {
    console.log(error);
  });
```

The real Fetch API implementation is much more complex and has support for advanced features. We just scratched the surface here. The code can be improved, for example the logic for adding headers can live on a method on it's own. Plus there's a lot of room for adding new features: support for PUT and DELETE and more functions for returning the response in different formats. If you're curious to see a more complex Fetch API polyfill check out [whatwg-fetch](https://github.com/github/fetch) from the engineers at Github. You'll see a lot of similarities with your handcrafted polyfill.

## Conclusions

AJAX shaped the way we build the web by giving us the chance to build fluid, user-friendly interfaces. Long gone are the days of the classic page refresh. Nowadays we can build elegant JavaScript applications and get the data we need in background. XMLHttpRequest is the good old legacy API for making HTTP requests, used still today, yet in a different incarnation: the Fetch API. It's been a long journey this chapter, we saw the Fetch API in depth and how to use our own custom Promises. Have you ever worked before with jQuery? After finishing this chapter you'll have all the tools at your hand for building your own data fetching module and ditch `jQuery.get()` and `jQuery.getJSON()`.

Exercises available in the [paid version](https://leanpub.com/little-javascript/).


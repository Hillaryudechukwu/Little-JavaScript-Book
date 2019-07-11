# Chapter 10. Working with asynchronous JavaScript

## REST API and XMLHttpRequest

If you ask me, JavaScript is really useful. As a scripting language running in a browser it does all sort of things like creating elements dynamically, adding interactivity, and so on. In chapter 8 you built an HTML table starting from an array. An array is a synchronous data source, that is, it's available straight away in our code. No need to wait. But as JavaScript developers most of the time we want to fetch data from the outside: from a REST API for example. As opposed to synchronous data sources, network communication is always an asynchronous operation: we request the data and the server "might" respond back later. But JavaScript has no built-in asynchronicity: most of the times it's the browser that provides external helpers for dealing with time-consuming operations. In chapter 3 you also saw `setTimeout` and `setInterval`, two browser APIs for time based work. Browsers offers a lot of API and there's one called `XMLHttpRequest` aiming specifically at making network requests. The naming is really weird: `XMLHttpRequest` sounds so '00. In fact it comes from an era when most web services exposed data in XML format. Nowadays JSON is the most popular communication "protocol" for moving data between web services but the name XMLHttpRequest eventually stuck. `XMLHttpRequest` is also part of a set of technologies called AJAX which stands for "Asynchronous JavaScript and XML". AJAX was born for making network requests in the browser as slick as possible. AJAX meant the ability to fetch data from a remote source without causing a page refresh. At that time the idea was almost revolutionary. With the introduction of `XMLHttpRequest` (almost 13 years ago) the platform gained a native method for making network requests. Given a url we can make an HTTP request like so:

```js
var request = new XMLHttpRequest();

request.open("GET", "https://academy.valentinog.com/api/link/");

request.addEventListener("load", function() {
  console.log(this.response);
});

request.send();
```

The act of making an XHR (XMLHttpRequest) goes like that:

- create a new XMLHttpRequest object
- open the request by providing a method and an url
- register event listeners
- send the request

XMLHttpRequest is based on DOM events (seen on chapter 8) thus we can use either `addEventListener` or `onload` for listening to the "load" event which is triggered whenever the request succeeds. Here's the alternative syntax:

```js
var request = new XMLHttpRequest();

request.open("GET", "https://academy.valentinog.com/api/link/");

request.onload = function() {
  console.log(this.response);
};

request.send();
```

For failing requests (network errors) we can register a listener on the "error" event:

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

Now armed with this knowledge let's put `XMLHttpRequest` to good use.

## XMLHttpRequest in action: generating lists with HTML and JavaScript

In the following exercise you'll build a simple HTML list but not before fetching the data from a REST API. Create a new file named **build-list.html** in a folder of choice:

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

Next up create a file named **xhr.js** in the same folder. In this file we're going to create a new XHR request (XHR stands for asynchronous JavaScript and XML):

```js
"use strict";

const request = new XMLHttpRequest();
```

The above call (called constructor call) creates a new object of type XMLHttpRequest which can be used for making HTTP requests. Opposed to asynchronous functions like `setTimeout`, expecting the callback as an argument:

```js
setTimeout(callback, 10000);

function callback() {
  console.log("hello timer!");
}
```

XMLHttpRequest is based on DOM events and the handler callback is registered on the `onload` object. The load event fires when the request succeeds:

```js
"use strict";

const request = new XMLHttpRequest();

request.onload = callback;

function callback() {
  console.log("Got the response!");
}
```

After registering the callback we can open the request with `open()`. It accepts an HTTP method (GET in our case) and an url to contact:

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

If you did everything right you'll see "Got the response!" in the browser console. Anyway, printing a string is almost useless. Instead we're interested in the response from the server. If you remember from chapter 6, every regular JavaScript function holds a reference to the object in which is running: `this`. Since the callback is running inside the XMLHttpRequest we can access the server response on `this.response`

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

Save the file and visit **build-list.html**. You should see the server's response in the console, as a text document. It's still not so useful because we're interested in a JSON document rather that on its textual representation. At this point we have two options:

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
request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
```

Option 2 (JSON.parse) is more descriptive and idiomatic:

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

Now we're ready to use the JSON response for building a list of elements.

COMING SOON

## Asynchronous evolution: from XMLHttpRequest to Fetch

COMING SOON

## Error handling with Fetch

COMING SOON

## Fetch with async/await

COMING SOON

## Rebuilding the Fetch API from scratch (for fun and profit)

Often overlooked in favor of libraries like Axios, the Fetch API is a native browser method. Fetch was born in 2015, the same year which saw the introduction of ECMAScript 2015 and Promise. But AJAX, a set of technologies for fetching data in the browser exists since 1999. Nowadays we take AJAX and Fetch for granted but few know that Fetch is nothing more than a glorified version of XMLHttpRequest. Fetch is similar but looks more concise, and more important is based on Promises:

```js
fetch("https://academy.valentinog.com/api/link/").then(function(response) {
  console.log(response);
});
```

In the following exercise you'll build Fetch from scratch, making use of Promises for wrapping XMLHttpRequest. As a JavaScript developer most of the time you will "consume" a Promise rather than create one from scratch. That means most developers know about Promises because they've used `then` and `catch` as consumers of other people code. But that's only half of the story. Library creators on the other hand use Promise for wrapping legacy code and offering a more modern API to developers. And that's exactly what we're going to do: we'll create a so called polyfill for Fetch. To start off create a new file named **fetch.html** and save it in a folder of choice:

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

Then create another file named **fetch.js** in the same folder and start the code with the following statements:

```js
"use strict";

window.fetch = null;
```

In the first line we ensure we're in strict mode and in the second line we "nullify" the original Fetch API. With everything in place we can start building our own Fetch API. But first let's reverse engineer the real Fetch. The way Fetch works is quite simple. It takes an url and makes a GET request against it:

```js
fetch("https://academy.valentinog.com/api/link/").then(function(response) {
  console.log(response);
});
```

When a function is "chainable" with `then` it means it's returning a Promise. So inside **fetch.js** let's create a function named fetch, which takes an url and returns a new Promise. For creating a Promise you can call the Promise constructor and pass in a callback taking resolve and reject:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    // do stuff
  });
}
```

We know that when a function returns a resolved Promise the `then` handler is triggered. Let's give it a shot by resolving our Promise: 

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    resolve("Fake response!");
  });
}
```

Try the complete code. Now we assign our fake fetch to `window.fetch`:

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

You should get "Fake response!" inside the console. It works! Granted, it's still a useless Fetch because nothing gets returned from the API. Let's implement the real behaviour, with some help from XMLHttpRequest. We saw how to create a request with XMLHttpRequest:

```js
var request = new XMLHttpRequest();
request.open("GET", "https://academy.valentinog.com/api/link/");
request.send();
request.onload = function() {
  console.log(this.response);
};
```

for maximum compatibility we're going to use the legacy `onload` for listening to the load event. Let's put the knowledge to good use. What we're going to do now is wrapping XMLHttpRequest inside our Promise. Open up **fetch.js** and extend the fetch function to include a new XMLHttpRequest:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // handle the response
    };
    request.send();
  });
}
```

Things start to make sense now, isn't it? We can also register an handler for failures on `onerror`:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
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

At this point it's a matter of resolving or rejecting the Promise depending on the request's fate. That means resolving the Promise inside the "load" handler with the response we've got from the server:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
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

In case of error we reject the Promise inside the "error" handler:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
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

Fantastic! Our Fetch polyfill works but there are some rough edges. First of all we need to implement a function for returning JSON. Let's see how. The actual Fetch API yields a response which can be later converted to JSON, blob or text, like so (for the scope of this exercise we'll implement only the JSON function):

```js
fetch("https://academy.valentinog.com/api/link/")
  .then(function(response) {
    return response.json();
  })
  .then(function(json) {
    console.log(json);
  })
```

It should be easy to implement the functionality. Seems that "response" might be an entity on its own with a json() function attached. The JavaScript prototype system is a good candidate for structuring the code (check out chapter 5 for a refresh). Let's create a constructor function named Response and a method bound to its prototype (in **fetch.js**):

```js
function Response(response) {
  this.response = response;
}

Response.prototype.json = function() {
  return JSON.parse(this.response);
};
```

That's all! Now we can use Response inside Fetch:

```js
function fetch(url) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // resolve(this.response);
      // After:
      var response = new Response(this.response);
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
    var request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // resolve(this.response);
      // After:
      var response = new Response(this.response);
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

If you did everything correct the above code should print an array of objects into the browser's console. Good job! And now let's take care of errors. The real version of Fetch is much more complicated than our polyfill but it's not so difficult to replicate the same exact behaviour. The Response object in Fetch has a property, a boolean called "ok". That boolean is set to true when the request succeeds and to false when the request fails. A developer can check the boolean and alt the Promise handler by throwing an error. Here's how you would check the status with the real Fetch:

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
- with a status code less than 300

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
    var request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // var response = new Response(this.response);
      // After: pass the entire response
      var response = new Response(this);
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
    var request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      // Before:
      // var response = new Response(this.response);
      // After: pass the entire response
      var response = new Response(this);
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

You should see "Error: bad request" in the browser's console. Way to go! And now let's extend our polyfill for making POST requests. First of all let's see how to make POST requests with the real Fetch. The entity we're going to create on the server is a "link", that is, made of a title and an url:

```js
var link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};
```

Next up we configure the request for Fetch. It's done in an object which should contain at least the HTTP method, the request's content type (JSON in our case) and the request's body (the new link):

```js
var link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};

var requestInit = {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify(link)
};
```

Finally we make the request like so:

```js
var link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};

var requestInit = {
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
    var request = new XMLHttpRequest();
    request.open("GET", url);
    request.onload = function() {
      var response = new Response(this);
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
- uses body, method, and headers from requestInit if the latter is provided

To start with we make a new "Request" function with some properties named body, method, and headers. Open up **fetch.js** and get coding:

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
    var request = new XMLHttpRequest();
    var requestConfiguration = new Request(requestInit || {});
    // more soon
  });
}
```

Now the request will use the method defined in the Request object:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    var requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    // more soon
  });
}
```

The logic for handling errors and success (load) remains the same:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    var requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      var response = new Response(this);
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
    var request = new XMLHttpRequest();
    var requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      var response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    // Set headers on the request
    for (var header in requestConfiguration.headers) {
      request.setRequestHeader(header, requestConfiguration.headers[header]);
    }
    // more soon
  });
}
```

Headers are important for configuring the AJAX request. Most of the times you may want to set application/json or an authentication token in the headers. As the cherry on top we can complete our code to send: 

- an empty request if there is no body
- a request with some payload is the body is provided:

```js
function fetch(url, requestInit) {
  return new Promise(function(resolve, reject) {
    var request = new XMLHttpRequest();
    var requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      var response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    // Set headers on the request
    for (var header in requestConfiguration.headers) {
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
    var request = new XMLHttpRequest();
    var requestConfiguration = new Request(requestInit || {});
    request.open(requestConfiguration.method, url);
    request.onload = function() {
      var response = new Response(this);
      resolve(response);
    };
    request.onerror = function() {
      reject("Network error!");
    };
    for (var header in requestConfiguration.headers) {
      request.setRequestHeader(header, requestConfiguration.headers[header]);
    }
    requestConfiguration.body && request.send(requestConfiguration.body);
    requestConfiguration.body || request.send();
  });
}

var link = {
  title: "Building a Fetch Polyfill From Scratch",
  url: "https://www.valentinog.com/fetch-api/"
};

var requestInit = {
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

Give it a shot and maybe write a test for it. The real Fetch API implementation is much more complex and has support for more advanced features. We just scratched the surface of Fetch in this exercises but as you can see it's coming up quite well. Of course the code can be improved, for example the logic for adding headers can live on a method on it's own. Plus there's a lot of room for adding new features: support for PUT and DELETE and more functions for returning the response in different formats. I leave that as an exercise for you. If you're curious to see a more complex Fetch API polyfill check out [whatwg-fetch](https://github.com/github/fetch) from the engineers at Github. You'll see a lot of similarities with your handcrafted polyfill! 

## Conclusions

TODO

## Sharpen up your JavaScript skills

- What is a polyfill?
- TODO
- TODO
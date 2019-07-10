# Chapter 1. Getting started with JavaScript

## What is JavaScript?

JavaScript is a scripting language for the web. Born in 1995 from Brendan Eich's hands, JavaScript was created for adding interactivity to web pages. At that time the web was still in its infancy and most of the fancy web pages we can see today were still a dream. Chased by his project manager, Brendan had only 10 days for creating a dynamic, flexible language that could run in a browser. He came up with JavaScript, a somewhat weird programming language which borrowed from Java (the cool kid at the time), C, and Scheme. JavaScript always had a bad reputation because it was full of quirks from the very beginning. But despite that JavaScript conquered a seat in the hall of fame and it's here to stay.

## What can I build with JavaScript?

JavaScript is everywhere and it's used for almost everything. Born as a scripting language for the web is mostly used for adding interactivity to web pages: e.g. you can "listen" for clicks on HTML elements and react to the event with a JavaScript function. But besides these simple interactions JavaScript is also used for creating entire applications called SPA (single page applications). With the rise in usage the JavaScript ecosystem also saw a cambrian explosion of tools and libraries. Most of the frontend tools we use today are written in JavaScript: think of webpack, rollup, grunt, gulp, and friends. JavaScript is also used outside its natural habit. With Node.js we can create server-side and IoT applications, industrial appliances, and much more. But on top of that single page applications are one of the most prominent usage of JavaScript. What' a single page application? Take a look at the following HTML:


```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8">
    <title>My application</title>
<link href="main.css" rel="stylesheet"></head>

<body>
    <div id="app">
    </div>
<script type="text/javascript" 
        src="main.4a4c.js" 
        async="">
</script>
</html>
```

As you can see there is a script tag for loading a JavaScript file. The entire app is there. Single page applications are web apps built entirely with JavaScript. JavaScript takes over the application when the user visits the page and there will be no page refresh on subsequent interactions. From a user's perspective it's a huge improvement over classic applications. But with single page apps comes great responsibility: JavaScript is an heavy burden for most mobile devices and great care should be taken when designing and building JavaScript based web apps.

## Why should I study JavaScript?

JavaScript has always been dismissed as a toy language, but the opposite is true. JavaScript has grown mature with the release of ECMAScript 2015 which brought a lot of nice improvements. The language is getting better and better year after year. Almost every frontend developer job opening requires JavaScript knowledge nowadays. And it's not a "basic jQuery knowledge" that hiring managers are looking for. Speaking of which, jQuery is slowly dying and that's a good thing for the web. It makes almost no sense to learn jQuery in 2019. Instead, frontend developers should know JavaScript quirks and be able to write idiomatic, well structured code, even with pure vanilla JavaScript. JavaScript is spreading fast and even if you don't like the language ignoring it at this point could even be detrimental to your career. Studying JavaScript today does not mean a shallow understanding of variables and functions: there is a lot more. An expert JavaScript developer knows closures, `this`, `new`, the prototype system, and code organization to name some. In this book you'll learn all the "hard parts" of JavaScript without the gimmicky packaging that comes with most of online tutorials and most important without arid academic lucubration like other JavaScript books. I hope this book will be an handy reference even for more experience developers looking forward to solidify their skills.

## ECMAScript, ES5, ES6 and other technical terms

You'll find a lot of technical terms in the book. Expert developers know them all, but I don't want beginners to feel bad because they're afraid to ask what "API" means. Here's a brief review of the most important web-related terms.

**API**: stands for Advanced Programming Interface, but don't bother this strict definition: an API is like an USB socket, really. You, the developer, can interact with the socket and exchange data with it. An API in programming is also a set of tools, a toolbox which is built by other developers and ready for use.

**Native API**: a native API is a built-in function available by default in a programming environment. Speaking of browsers for example we say that `document.querySelector()` is a native API for selecting HTML elements.

**Browser API or Web API**: like native APIs a Web API is a specific functionality available in a web browser. Developers can use these methods out of the box. Examples of Web API are `setTimeout`, `setInterval`, `console.log`. For a complete list check out [Web APIs](https://developer.mozilla.org/en-US/docs/Web/API).

**ECMAScript**: it is the official name for JavaScript. In 1996 JavaScript was donated to ECMA international, a third-party entity which takes care of defining standards for a lot of technology related things.

**ES5**: acronym for ECMAScript 2009, the fifth version of JavaScript. To avoid confusion it's more correct to say ECMAScript + year for denoting a specific JavaScript version.

**ES6**: stands for ECMAScript 2015, the sixth version of JavaScript. Since 2015 the JavaScript committee decided to release new features yearly. From there we had ECMAScript 2016, ECMAScript 2017, ECMAScript 2018, and so on.

**JavaScript engine**: is part of the browser and is able to compile and interpret JavaScript code. Browser vendors build JavaScript engines by following (sometimes not so strictly) a document called JavaScript specification.

**JavaScript specification**: is a formal, written document which outlines how the JavaScript language should behave. Browser vendors read the spec and implement JavaScript engines in a way that JavaScript code is executed as the spec prescribes.

**HTTP request**: is the act of "talking" to a remote web server for fetching or saving data. Most of the times the data is exchanged in JSON format.

**HTTP error**: sometimes things don't go well when talking to web services and the server may respond with an error. Errors are denoted with a numeric code: some common errors are 500 (server error), 404 (not found), 403 (forbidden), and so on.

**JSON**: JSON stands for JavaScript Object Notation, a format for exchanging data between web services and JavaScript applications.

**REST API**: is a web service (local or remote) which exposes data. A REST API makes possible to interact with an underlying database for saving or retrieving entities.

**Transpiler**: older browsers do not support modern JavaScript syntax from ECMAScript 2015 and above. A transpiler is a tool which takes modern JavaScript syntax and spits out a more compatible version (ECMAScript 2009).

**Vanilla JavaScript**: vanilla JavaScript is a term for denoting "pure" JavaScript applications, i.e. those written without the help of a frontend library like React, Vue, or Angular.

**AJAX**: a set of technologies for fetching data in the browser without causing a page refresh. The acronym stands for "Asynchronous JavaScript and XML", coined in 1999.

## A special note on ES2015

ECMAScript 2015 (ES6) is one of the most important releases ever for JavaScript. It introduced a lot of improvements into the language and in this book you'll find the most important ECMAScript 2015 features like Promise, arrow functions, class, and more. I wish I could go through every tiny bit of ECMAScript 2015 here, but another book would not be enough to cover them all. Instead I took another path and I decided to include just the most relevant features strategically, here and there during the chapters. If you want to learn ECMAScript 2015 in depth after reading this book I highly suggest one of my favourite essay on the subject: [Exploring ES6](https://exploringjs.com/es6/) by Axel Rauschmayer.

## Conclusions

JavaScript was born in 1995 as a scripting language for the web. Since then it gained massive traction and today we can say with certainty that JavaScript has become the lingua franca of the web. JavaScript is used in many environments and developers well versed in this language are highly paid nowadays. But jQuery, one of the most used JavaScript library back in the days is not enough anymore as a skill. JavaScript developers should know the intricacies of JavaScript and being able to structure the code well, other than being able to understand legacy JavaScript applications.

## Sharpen up your JavaScript skills

- What is an API?
- What is JavaScript used for?
- How many versions of JavaScript have been released since 1995?
# Chapter 1. Getting started with JavaScript

## What is JavaScript?

JavaScript is a scripting language for the web. Born in 1995 from Brendan Eich's hands, JavaScript was created for adding interactivity to web pages. At that time the web was still in its infancy and most of the fancy web pages we can see today were still a dream. Chased by his project manager, Brendan had only 10 days for creating a dynamic, flexible language that could run in a browser. He came up with JavaScript, a somewhat weird programming language which borrowed from Java (the cool kid at the time), C, and Scheme. JavaScript always had a bad reputation because it was full of quirks from the very beginning. But despite that it conquered a seat in the hall of fame and it's here to stay. Nowadays JavaScript is also used for creating entire applications, called SPA (single page applications). With the rise in usage the JavaScript ecosystem also saw a cambrian explosion. Most of the tools and libraries we use today for developing JavaScript are ... well, written in JavaScript! JavaScript is also used outside its natural habit. With Node.js we can create server-side and IoT applications, industrial appliances, and much more. But on top of that, single page applications are one of the most prominent usage for JavaScript. Single page applications are web apps built entirely with JavaScript. JavaScript takes over the application when the user visits the page and there will be no page refresh on subsequent interactions. From a user's perspective it's a huge improvement over classic web apps. But with great power comes great responsibility: JavaScript is an heavy burden for most mobile devices and great care should be taken when designing and building JavaScript based web apps.

## Why should I study JavaScript?

JavaScript has always been dismissed as a toy language, but that's not the case anymore. It has grown mature with the release of ECMAScript 2015 which brought a lot of nice improvements. The language is getting better and better year after year. Almost every frontend developer job requires JavaScript knowledge nowadays. And it's not a "basic jQuery knowledge" that hiring managers are looking for. Most of the times you should also know TypeScript, the JavaScript "enterprise" sibling. Speaking of jQuery, it appears it's slowly dying and that's a good thing for the web. It makes almost no sense to learn jQuery today. Instead, frontend developers should know JavaScript quirks and be able to write idiomatic, well structured JavaScript code. JavaScript is spreading fast and even if you don't like the language ignoring it at this point could be detrimental to your career. Studying JavaScript today does not mean a shallow understanding of variables and functions: there is a lot more. An expert JavaScript developer knows closures, `this`, `new`, the prototype system, and code organization to name some.

## ECMAScript, and other technical terms

You'll find a lot of technical terms in the book. Expert developers know them all, but I don't want beginners to feel bad because they're afraid to ask what "API" means. Here's a brief review of the most important web-related terms.

**API**: stands for Advanced Programming Interface, but don't bother the strict definition: an API is like an USB socket, really. You, the developer, can interact with the socket and exchange data with it. An API in programming is also a set of tools, a toolbox which is built by other developers and ready for use.

**Native API**: a native API is a built-in function available by default in a programming environment. Speaking of browsers for example we say that `document.querySelector()` is a native API for selecting HTML elements.

**Browser console**: in most web browsers you can access a developer toolbox. In Firefox and Chrome on Mac press Command + Option + I, on Linux (and Windows if I can recall) press F12. That will open a interactive console where you can type and execute JavaScript code. The console will also show errors and other messages from your JavaScript programs.

**Debugger**: debuggers are tools built for helping developers to find why and where a program stops working. In JavaScript there is also the `debugger` instruction which stops the script exactly where it's placed.

**Browser API or Web API**: like native APIs a Web API is a specific functionality available in a web browser. Developers can use these methods out of the box. Examples of Web API are `setTimeout`, `setInterval`, `console.log`. For a complete list check out [Web APIs](https://developer.mozilla.org/en-US/docs/Web/API).

**ECMAScript**: it is the official name for JavaScript. In 1996 JavaScript was donated to ECMA international, a third-party entity which takes care of defining standards for a lot of technology related things.

**ES5**: acronym for ECMAScript 2009, the fifth version of JavaScript. To avoid confusion it's more correct to say ECMAScript + year for denoting a specific JavaScript version.

**ES6**: stands for ECMAScript 2015, the sixth version of JavaScript. Since 2015 the JavaScript committee decided to release new features yearly. From there we had ECMAScript 2016, ECMAScript 2017, ECMAScript 2018, and so on.

**JavaScript engine**: is part of the browser and is able to compile and interpret JavaScript code. Browser vendors build JavaScript engines by following (sometimes not so strictly) a document called JavaScript specification.

**JavaScript specification**: is a formal, written document which outlines how the JavaScript language should behave. Browser vendors read the spec and implement JavaScript engines in a way that JavaScript code is executed as the spec prescribes.

**Node.js**: an environment for running JavaScript outside of the browser. It includes a JavaScript engine, V8, for compiling and executing the code. Node.js is mostly used on the server-side.

**Node package manager**: npm in short, is a tool for managing the workflow of JavaScript projects, from installing thirdy-part packages to shipping code to users.

**HTTP request**: is the act of "talking" to a remote web server for fetching or saving data. Most of the times the data is exchanged in JSON format.

**HTTP error**: sometimes things don't go well when talking to web services and the server may respond with an error. Errors are denoted with a numeric code: some common errors are 500 (server error), 404 (not found), 403 (forbidden), and so on.

**JSON**: JSON stands for JavaScript Object Notation, a format for exchanging data between web services and JavaScript applications.

**REST API**: is a web service (local or remote) which exposes data. A REST API makes possible to interact with an underlying database for saving or retrieving entities.

**Transpiler**: older browsers do not support modern JavaScript syntax from ECMAScript 2015 and above. A transpiler is a tool which takes modern JavaScript syntax and spits out a more compatible version (ECMAScript 2009).

**Vanilla JavaScript**: vanilla JavaScript is a term for denoting "pure" JavaScript applications, i.e. those written without the help of a frontend library like React, Vue, or Angular.

**AJAX**: a set of technologies for fetching data in the browser without causing a page refresh. The acronym stands for "Asynchronous JavaScript and XML", coined in 1999.

## A special note on modern JavaScript

ECMAScript 2015 is one of the most important releases ever for JavaScript. It introduced a lot of improvements into the language and in this book you'll find the most important ECMAScript 2015 features like Promise, arrow functions, class, and more. JavaScript is getting better year after year with new features making their way into the language almost daily. I wish I could go through every tiny bit of modern JavaScript here, but another book would not be enough to cover them all. Instead I took another path and I decided to include just the most relevant features strategically, here and there during the chapters.
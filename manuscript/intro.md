# Intro. Few words about the book

## A brief history of the book

Why another book on JavaScript? As much as I love "You don't know JS" and "Eloquent JavaScript" I feel there is the need for a book which takes the reader by hand, challenging enough, yet concise. The Little JavaScript book saw its light originally in italian as "Il Piccolo Libro di JavaScript" and soon a lot of fellow developers asked for an english version. What I did here however is not a 1 to 1 translation. "The Little JavaScript Book" is a creature on its own, with fresh contents and a different perspective. In this book you'll learn the "hard parts" of JavaScript without the gimmicky packaging that comes with some tutorials, and most important without arid academic lucubration. It aims to be a concise reference, while never peeking too much into the JavaScript spec. Enjoy the reading!

## Structure of the book

Every chapter can be consumed as a stand-alone episode, tackling down a group of topics that in my mind played nicely together. The book is organized in three parts. The first part covers the inner working of JavaScript engines and the "hard parts" of the language: closures, the prototype system, `this` and `new`. The second part of the book has a lot of practical exercises on the DOM with a lot of pages covering code organization and best practices. In chapter 8, 9, 10, and 11 for example you'll build small features while touching Promises, DOM manipulation, JavaScript classes, localStorage, TypeScript, and more. Every chapter ends with quizzes and exercises, useful for making the concepts stick. The third and last part contains solutions and hints for the exercises and future additions to the book. At the last minute I decided to add a chapter on TypeScript + JSDoc. TypeScript is gaining a lot of traction: more and more developers are switching from JavaScript to TS.

## Who this book is for?

While writing the book I had in mind web developers who always worked with jQuery or JavaScript without digging deeper into the language. The book is not a complete intro to programming. But with a little work you should be able to follow along even if you never programmed before. I also hope this book will be an handy reference even for more experience developers looking to solidify their skills. You'll be surprised to discover how much you forgot about JavaScript. Worth mentioning, "The Little JavaScript Book" is not a complete guide to ECMAScript 2015 and newer versions of the language. But we'll see some of the newest JavaScript feature as we go during the chapters.

## Typographic conventions

This book uses JavaScript with code examples written inside a block:

```js
function generateTableHead(table, data) {
  var thead = table.createTHead();
  var row = thead.insertRow();
  for (var i = 0; i < data.length; i++) {
    var th = document.createElement("th");
    var text = document.createTextNode(data[i]);
    th.appendChild(text);
    row.appendChild(th);
  }
}
```

or also inline: `th.appendChild(text)`. You will find both ES5 and modern JavaScript syntax in the book.

## What's the best way for following the examples?

I suggest experimenting hands-on with the code proposed thought the book. For writing HTML and JavaScript you can use any text editor and then run them in a browser. You can also use an online tool like [Js Bin](https://jsbin.com) or [CodeSandbox](https://codesandbox.io/). In chapter 11 you'll need `npm` (Node package manager) for installing some tools. Make sure to have one of the latest version of [Node.js](https://nodejs.org/en/) installed on your system. The `npm` binary is bundled there with Node.js.

## Errata and other requests

This book is not a definitive guide to JavaScript and I might have missed something you'd like to read. The nice thing is that I can update the book whenever I want. If you want me to cover some topics in depth feel free to drop me an email at valentino@valentinog.com.

## About the author

I've been in love with computers since I was 6 years old. In 1999 I was creating the first HTML pages and from there the web became my life. Today I help busy companies to embrace this crazy modern JavaScript stuff. I do training and consulting on JavaScript, React, Redux. Besides JavaScript I'm also active in the Python community. I serve as a coach for [Django Girls](https://djangogirls.org/) and I speak at Pycon Italia. Check out [my talks here](https://www.valentinog.com/talks). In my spare time you can find me in the countryside near Siena. If you want to get in touch for a beer feel free to ping me up at valentino@valentinog.com. Fancy reading some stuff about JavaScript or Python? Head over [my blog](https://www.valentinog.com/blog/)!

## Acknowledgments

This book would not have been possible without the clever questions I've got during the years from dozens of developers. A huge thanks goes also to all the people who helped shape the beta version of this book: Caterina Vicenti, Alessandro Muraro, Cristiano Bianchi, Irene Tomaini, Roberto Mossetto, Ricardo Antonio Piana, Alessio Forti, Valentino Pellegrino, Mauro De Falco, Sandro Cantagallo, Maurizio Zannoni.
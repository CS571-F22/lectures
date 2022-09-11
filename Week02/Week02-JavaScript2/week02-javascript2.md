---
marp: true
footer: CS571 Building User Interfaces | Cole Nelson | Lecture 02: JavaScript 2
paginate: true
theme: uncover
style: |
  section {
    text-align: left
  }
  .center-info {
    text-align: center
  }
---


<br>

# **JavaScript 2**
### CS571: Building User Interfaces


<br>

#### Cole Nelson

---

### What we will learn today?

<div>

* Working with JSON data
* Working with APIs
* Working with other async functions
* Working with Bootstrap and CSS libraries

</div>

---

### What is JSON?
**Definition:** JavaScript Object Notation (JSON) is a structured way to represent text-based data based on JS object syntax.

---

### Refresher: JS Objects
**Definition:** Objects are unordered collection of related data of primitive or reference types defined using key-value pairs.

```javascript
const instructor = {
  firstName: "Cole",
  lastName: "Nelson",
  roles: ["student", "faculty"]
}
```

---
### JSON Equivalent

```json
{
  "firstName": "Cole",
  "lastName": "Nelson",
  "roles": ["student", "faculty"]
}
```

**What's the difference?** A JS Object is executable code; JSON is a language-agnostic representation of an object. There are also slight differences in syntax.

---

You can write comments in JS Objects...
```javascript
const drinks = [
  {
    name: "Mimosa",
    ingredients: [
      {name: "Orange Juice", hasAlcohol: false},
      {name: "Champagne", hasAlcohol: true}
    ]
  },
  {
    name: "Vesper Martini", // shaken, not stirred
    ingredients: [
      {name: "Gin", hasAlcohol: true},
      {name: "Vodka", hasAlcohol: true},
      {name: "Dry Vermouth", hasAlcohol: true},
    ]
  }
]
```

---

... but not in JSON!
```json
[
  {
    "name": "Mimosa",
    "ingredients": [
      { "name": "Orange Juice", "hasAlcohol": false },
      { "name": "Champagne", "hasAlcohol": true }
    ]
  },
  {
    "name": "Vesper Martini",
    "ingredients": [
      { "name": "Gin", "hasAlcohol": true },
      { "name": "Vodka", "hasAlcohol": true },
      { "name": "Dry Vermouth", "hasAlcohol": true }
    ]
  }
]
```

---

### Conversion
Because JS Objects and JSON are so similar, it is easy to convert between them.

<div>

 - `JSON.parse` JSON String → JS Object
 - `JSON.stringify` JS Object → JSON string

</div>

---

### Conversion Examples
Using `JSON.parse` and `JSON.stringify`.

```javascript
const myObj = JSON.parse('{"name": "Cole", "age": 24}');
const myStr = JSON.stringify(myObj);

console.log(typeof myObj);
console.log(typeof myStr);
```

```
object
string
```

---
### Data Copying
`json.parse` and `json.stringify` can also be useful for deep data copying.^


<sub><sup>^ [lodash](https://lodash.com/) is the preferred way to copy.</sup></sub>



---

### Data Copying - Reference Copy

```javascript
let myBasket = {
  basketId: 154,
  items: ["Apples", "Bananas", "Grapes"]
};
let myRefCopyBasket = myBasket;
myRefCopyBasket.basketId = 999;

console.log(myBasket);
console.log(myRefCopyBasket);
```

```
{basketId: 999, items: ['Apples', 'Bananas', 'Grapes']}
{basketId: 999, items: ['Apples', 'Bananas', 'Grapes']}
```

---

### Data Copying - Deep Copy

```javascript
let myBasket = {
  basketId: 154,
  items: ["Apples", "Bananas", "Grapes"]
};
let myRefCopyBasket = JSON.parse(JSON.stringify(myBasket));
myRefCopyBasket.basketId = 999;

console.log(myBasket);
console.log(myRefCopyBasket);
```

```
{basketId: 154, items: ['Apples', 'Bananas', 'Grapes']}
{basketId: 999, items: ['Apples', 'Bananas', 'Grapes']}
```

---

**Reference Copy Visualization**

![bg right 100%](figures/reference_copy.png)

<sub><sup>[Interactive Example](https://pythontutor.com/render.html#code=let%20myBasket%20%3D%20%7B%0A%20%20basketId%3A%20154,%0A%20%20items%3A%20%5B%22Apples%22,%20%22Bananas%22,%20%22Grapes%22%5D%0A%7D%3B%0Alet%20myRefCopyBasket%20%3D%20myBasket%3B%0AmyRefCopyBasket.basketId%20%3D%20999%3B%0A%0Aconsole.log%28myBasket%29%3B%0Aconsole.log%28myRefCopyBasket%29%3B&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=js&rawInputLstJSON=%5B%5D&textReferences=false)</sup></sub>

---

**Deep Copy Visualization**

![bg right 100%](figures/deep_copy.png)

<sub><sup>[Interactive Example](https://pythontutor.com/render.html#code=let%20myBasket%20%3D%20%7B%0A%20%20basketId%3A%20154,%0A%20%20items%3A%20%5B%22Apples%22,%20%22Bananas%22,%20%22Grapes%22%5D%0A%7D%3B%0Alet%20myRefCopyBasket%20%3D%20JSON.parse%28JSON.stringify%28myBasket%29%29%3B%0AmyRefCopyBasket.basketId%20%3D%20999%3B%0A%0Aconsole.log%28myBasket%29%3B%0Aconsole.log%28myRefCopyBasket%29%3B&cumulative=false&curInstr=0&heapPrimitives=nevernest&mode=display&origin=opt-frontend.js&py=js&rawInputLstJSON=%5B%5D&textReferences=false)</sup></sub>

---

### What is an API?
**Definition:** An application programming interface (API) is a set of definitions and protocols for communication through the serialization and de-serialization of objects.

JSON is a language-agnostic medium that we can serialize to and de-serialize from!

---

### Request for JSON

- Requests can be `synchronous` or `asynchronous`. 
- `asynchronous` requests are recommended as they are *non-blocking*. Typically, they use a *callback* when the data is received and lets the browser continue its work while the request is made.

<sub><sup>More on [synchronous/asynchronous requests](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests)</sup></sub>

---

### Making Asynchronous Requests

Two key methods: `XMLHttpRequest` (old) and `fetch` (new). `fetch` is a promise-based method. 

- `Promise` objects represent the eventual completion/failure of an *asynchronous* operation and its resulting value. 
- `async` / `await` — keywords to indicate that a function is *asynchronous* 👈 preferred method
- We'll cover these in-depth in React.

---

### `fetch()`

```javascript
fetch(url)
  .then(response => response.json())
  .then(data => {
    // Do something with the data
  })
  .catch(error => console.error(error)) // Print errors
```

[Fetching Jokes API](https://v2.jokeapi.dev/joke/Any?safe-mode)

[Fetching Jokes Code](https://stackblitz.com/edit/js-7qag38?file=index.html,index.js)

---

### Callback Functions

`then` and `catch` take a *callback function* as an argument.

**Definition:** A *callback function* (sometimes called a *function reference*) is passed into another function as an argument, which is then invoked inside the outer function to complete a routine or action.


<sub><sup>More on [callback functions](https://developer.mozilla.org/en-US/docs/Glossary/Callback_function)</sup></sub>

---

### Callback Functions

```javascript
function greeting1(name) {
  alert('Hello ' + name);
}

function getting2(name) {
  alert('Welcome ' + name);
}

function processUserInput(callback) {
  const name = prompt('Please enter your name.');
  callback(name);
}

processUserInput(greeting1);
processUserInput(greeting2);
```

---

### Using `fetch` with Callbacks

Compare the following...

<div>

- [Fetching Jokes with Anonymous Function](https://stackblitz.com/edit/js-7qag38?file=index.html,index.js)
- [Fetching Jokes with Callback Function](https://stackblitz.com/edit/js-rugafr?file=index.html,index.js)

</div>

###

Why use one versus the other?

---

### Other `async` Functions

 - `setInterval(callback, interval)` perform a callback function every interval milliseconds.*
 - `setTimeout(callback, timeout)` perform a callback function in timeout milliseconds.*

[Fetch Jokes (w/ `setInterval`)](https://stackblitz.com/edit/js-wfgh3j?file=index.html,index.js)

[Fetch Jokes (w/ `setInterval` and `setTimeout`)](https://stackblitz.com/edit/js-1g42b8?file=index.html,index.js)

<sub><sup>\* approximately</sup></sub>

---

### Your turn!

Use the HW2 API to display information about book(s)!
https://www.coletnelson.us/cs571/f22/hw2/api/book
https://www.coletnelson.us/cs571/f22/hw2/api/books?amount=12

Explore the API, then create a bookstore landing page!

---

### The JS Event Loop

Wait, but JavaScript is a high-level, **single-threaded**, garbage-collected, interpreted, prototype-based, multi-paradigm, dynamic language with **a non-blocking event loop**^...

So... how can we have this *async* behavior?!

We'll come back to this!

<sub><sup>^ [JavaScript in 100 seconds](https://www.youtube.com/watch?v=DHjqpvDnNGE)</sup></sub>

---

<div class="center-info">

![w:800](figures/eventloop.png)


[What the hell is the event loop anyway?](https://www.youtube.com/watch?v=8aGhZQkoFbQ)

</div>



---

# Working with CSS Libraries

---

### What are CSS Libraries?

**Definition:** Software libraries that abstract away the low-level CSS implementation of user-facing elements.

Some popular libraries include...
<div>

* [Bootstrap](https://getbootstrap.com/)
* [Foundation](https://foundation.zurb.com/)
* [Semantic UI](https://semantic-ui.com/)
* [Pure](https://purecss.io/)
* [UIkit](https://getuikit.com/v2/)

</div>

---

## Bootstrap

![bg right 85%](figures/bootstrap.png)

[getbootstrap.com](https://getbootstrap.com/)

---

## How Bootstrap Works

Bootstrap provides us with...

<div>

* Layouts
* Content
* Components
* Utilities

</div>

###

There is much more!

---

#### Responsive Design
**Definition:** Responsive design adapts content to a variety of devices and screen sizes.

Width breakpoints determine whether the design will scale or be reorganized.

![bg right 85%](https://s3.amazonaws.com/www-assets.invisionapp.com/uploads/2018/10/INV_Responsive_Image_One_L1R1.png)

<sub><sup>[InVision](https://s3.amazonaws.com/www-assets.invisionapp.com/uploads/2018/10/INV_Responsive_Image_One_L1R1.png)</sup></sub>

---

### Bootstrap Categories: **Layouts**

<div>

**Containers** are the most basic element of layouts.

```html
<div class="container">
  ...
</div>
```

```html
<div class="container-fluid">
  ...
</div>
```

```html
<div class="container-{breakpoint}"> <!-- xs, sm, md, lg, xl-->
  ...
</div>
```

</div>

---

### Containing a Grid

Basic usage of a grid...

```html
<div class="container">
  <div class="row">
    <div class="col-*-^"></div>
    <div class="col-*-^"></div>
  </div>
</div>
```

Where `*` is *grid class* and `^` is *column size*.

---

![bg 90%](figures/Screen%20Shot%202020-09-09%20at%2012.43.11%20PM.png)

---

### Bootstrap Categories: **Content**

Content styling includes basic HTML elements, typography, code, images, tables, figures.

Basic HTML examples:

```html
<h1></h1>
<ul></ul>
<input/>
<button></button>
```

These will get the default Bootstrap styling.

---

### Styling of other elements

```html
<img src="..." class="img-fluid">
```

```html
<table class="table">
  <thead class="thead-dark">
    <tr>
      <th scope="col">...</th>
      ...
```

```html
<div class="table-responsive-sm">
  <table class="table">
  ...
```

---

### Bootstrap Categories: **Components**

Components include all other visual/interactive elements that make up the design, e.g., buttons, forms, navbar, tooltips, etc. 

```html
<button type="button" class="btn btn-primary">Fill button</button>

<button type="button" class="btn btn-outline-primary">Outline button</button>

<div class="btn-group-toggle" data-toggle="buttons">
  <label class="btn btn-secondary active">
    <input type="checkbox" checked autocomplete="off"> Switch
  </label>
</div>
```

---

### Bootstrap Categories: **Utilities**

Utilities are not elements themselves, but they modify/control other elements, e.g., adding rounded corners to an image.

```html
<img src="..." class="rounded">
```

```html
<div class="shadow p-3 mb-5 bg-white rounded">Shadow</div>
```

---

# Example Home Page
[See in CodePen](https://codepen.io/yuhangz/pen/jOwazjv?editors=1111)

---

### Additional Resources

<div>

* [Bootstrap Documentation](https://getbootstrap.com/docs/4.3/getting-started/introduction/)
* [Tutorial Republic](https://www.tutorialrepublic.com/twitter-bootstrap-tutorial/)
* [W3 Schools](https://www.w3schools.com/bootstrap/default.asp)

</div>

---

### Assets
Asset libraries, e.g., icons, are usually used in conjunction with frameworks such as Bootstrap.

![bg right 100%](figures/Icons.png)

See [icon libraries](https://getbootstrap.com/docs/4.3/extend/icons/).

<sub><sup>[Image Source](https://fontawesome.com/)</sup></sub>


---

<div>

### What did we learn today?

* Working with JSON data
* Working with APIs
* Working with other async functions
* Working with Bootstrap and CSS libraries

</div>

---

# On to Prototyping! 🚀
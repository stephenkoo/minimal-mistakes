---
title: "JavaScript ES6 Condensed: Part 3/3 - Classes, symbols, promises, iterators & generators"
categories:
    - lessons
tags:
    - Javascript/ECMAScript
    - ES6
---
## ES6 Condensed Series
- [**Part 0** - Running ES6 On The Front-end and Back-end][ES6-part-0]
- [**Part 1** - Strict mode, variables, arrow functions][ES6-part-1]
- [**Part 2** - Arrays, objects, maps & sets][ES6-part-2]
- -> **Part 3** - Classes, symbols, promises, iterators & generators

## 8.0 Classes
In ES5, we used to emulate classes with `.prototype`.

```js
"use strict"

class Person{
  constructor() {
    this.height = 180
  }
}
const stephen = new Person()
console.log(stephen) // :: [object Object] { height: 180 }
```

```js
class Person{
  constructor(name, age) {
    this.name = name
    this.age = age
    this.location = { x:0, y:0 }
  }

  get age() { // You can use getters & setters too
    return this.age
  }

  walk() {
    this.location.x += x;
    this.location.y += y;
  }
  speak() {
    console.log(`${this.name} says hi.`)
  }
}


let tony = new Person("Tony", 28, "6 feet")
console.log(this.age)
tony.walk(3,14)
console.log(tony)
tony.speak()
```

### 8.1 Extending classes
`super` grabs the constructor/functions of the parent class. Used to append/alter existing constructor/functions.

```js
class Hulk extends Person { // extend classes. Inherits properties from Person class.
  speak() {
    super.speak() // ::"Tony says hi." Executes original function
    console.log(`${this.name} says HULK SMASH.`)
  }

  constructor(){
    super() // Necessary if you want to append (i.e. rewrite) constructor for the extended class.
    this.strength = 10
  }
}

let greenGuy = new Hulk("Tony", 28, "6 feet")
greenGuy.speak()

```

### 8.2 Static methods
Usually you need to instantiate a new instance of a class to use a class method.
Not so with static methods.

```js
class Person{
  constructor() {
    this.name = "Emily"
  }

  speak(){
    return "Hello there."
  }
  static sayHi() { // Static function.
    return "Hi there."
  }
  static get sayHowdy() { // This creates a method. You can use get & set in static methods.
    return "Howdy."
  }
}

console.log(Person.speak()) // ::speak is not a function
console.log(Person.sayHi()) // ::"Hi there." Calls a function.
console.log(Person.sayHowdy) // ::"Howdy." Runs a method.
```

## 9.0 Symbols
Symbol is the 7th JS type (other types: undefined, null, boolean, number, string, object).
Used to uniquely tag your code.

```js
let sym = Symbol("foo")

console.log(Symbol("foo") === Symbol("foo")) // ::false. Why? "foo" is merely the symbol's description.
console.log(Number(1) === Number(1)) // ::true.

// Practical use: You want to add CSS animation to an element by attaching .isMoving key you created
element.isMoving
// The problem is .isMoving might run into naming collisions (.isMoving already exists), especially if element is created by someone else's API (e.g. jQuery element, CSS animation library). So it's bad practice to add your own key to someone else's object.
// The old workaround is to use a random string generator to ascribe a unique name to the key to avoid naming collisions.
let isMoving = Whatever.generateString(48151623)

// With symbols, you don't have to.
let isMoving = Symbol("isMoving")
```

## 10.0 Promises
Read [Dave Atchley's post on Promises][atchley-promise] for a good explanation of Promises.

Promises are alternatives to callbacks for delivering the results of an asynchronous computation.

A promise has 3 states:

1. Pending (final value not yet available)
2. Fulfilled/resolved (final value available)
3. Rejected (error prevents final value)

### 10.1 Anatomy of a promise
```js
"use strict"

let p = new Promise((resolve, reject) => {
  resolve('An immediately fulfilled promise.')
})
// or alternatively:
let p = Promise.resolve(42);

let p = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve('An timed fullfilled promise.')
  }, 1000)

  setTimeout(() => {
    reject('An timed rejected promise.')
  }, 500) // Because this runs earlier, this promise is rejected (cannot change state once settled.)
})

let p = new Promise(function(resolve, reject) {
  if (/* condition */) {
    resolve (/* value */)
  } else {
    reject (/* reason */)
  }
})
// new Promise(): Constructor. Instantiates/creates a promise.
// Executor: The callback that runs when promise is instantiated.
// The executor has 2 arguments: resolve, reject.
```

### 10.2 Consuming a promise
Once a promise is settled (fulfilled or rejected), its variable becomes its final value.
Use `.then()` method to consume/use this value.

```js
// .then()'s 1st param is fulfilled, 2nd parameter is rejected
p.then((val) => console.log("fulfilled:", val),
       (err) => console.log("rejected: ", err))

// So, same as:
p.then((val) => console.log("fulfilled:", val))
  .then(null, (err)) => console.log("rejected: ", err))

// .catch() is same as .then(null,(err)). This is the best method for handling errors because you can add just 1 .catch() at the end of any chain to handle rejections/exceptions.
p.then((val) => console.log("fulfilled:", val))
  .catch((err)) => console.log("rejected: ", err))
```

`.then()` & `.catch()` can be used on `fetch()`.

```js
fetch('http://api.icndb.com/jokes/random/10')
  .then((res) => {
    console.log(res) // res returns a 'Response' object with JSON inside (view in Chrome Dev Tools). So what you need actually is res.json().
    res.json().then((data)) => {
      console.log(data)
    })
  })
  .catch((err) => {
    console.log(err) // If you mistyped URL, etc.
  })
```

### 10.3 Throwing an error
A handler that throws an exception/error causes a chain to treat the promise as rejected, but does not affect the original promise's state. Thus, chain will treat the same promise as fulfilled (if it is fulfilled).

```js
// A fulfilled promise
let p = new Promise((resolve, reject) => {
  resolve(4);
})

p .then((val) => val + 2)
  .then((val) => { throw new Error ("step 2 failed") }) // Turns resolved promise into rejected
  .then((val) => console.log("got", val))
  .catch((err) => console.log("error: ", err.message)) // :: error: step 2 failed!

p .then((val) => val + 4) // But in another chain referring to the same promise, promise remains fulfilled.
  .then((val) => console.log("got", val))
  .catch((err) => console.log("error: ", err.message)) // :: got: 8
```

```js
// A promise designed to throw error rather than explicitly reject.
let p = new Promise((resolve, reject) => {
  if (true)
    throw new Error("rejected!") // treats as rejection
  else
    resolve(4)
})
// No need to throw an error in handlers, since promise already does so.
p.then((val) => val + 2)
 .then((val) => console.log("got", val))
 .catch((err) => console.log("error: ", err.message)) // :: error: rejected!
```

### 10.4 Handling multiple promises
Again, funfunfunction is amazing on Promises:
<iframe width="560" height="315" src="https://www.youtube.com/embed/2d7s3spWAzo?list=PL0zVEGEvSaeEd9hlmCXrk5yUyqUag-n84" frameborder="0" allowfullscreen></iframe>

`Promise.all()` fulfills if & when all promises in an array are fulfilled;
`Promise.race()` fulfills as soon as 1 promise in the array fulfills.


```js
let promise1 = new Promise((resolve, reject) => {
  resolve("Promise 1 ready!")
})
let promise2 = new Promise((resolve, reject) => {
  resolve("Promise 2 ready!")
})
Promise.all([promise1,promise2]).then((data) => {
  console.log(data) // :: ["Promise 1 ready!", "Promise 2 ready!"]
})
```

Practical example of displaying 3 images.

```js
// Callback method
// In app.js:
import loadImg from './cat-image'

let addImg = (src) => {
  let imgElement =
    document.createElement("img")
  imgElement.src = src
  document.body.appendChild(imgElement)
}

loadImg("img/cat1.jpg",(err, img1) => {
  if(err) throw err;
  addImg(img1.src)
  loadImg('img/cat2.jpg'),(err, img2) => {
    if(err) throw err;
    addImg(img2.src)
    loadImg('img/cat3.jpg'),(err, img3) => {
      if(err) throw err;
      addImg(img3.src)
    })
  })
})

// In cat-image.js
function loadImage(url, callback) {
  let image = new Image() // Creates image

  image.onload = function() { // Wait for image to load
    callback(null, image) // When it does, calls a callback with null because no error, & the image
  }

  image.onerror = function() { // If there is an error
    let message = `Could not load image at ${url}`
    callback(new Error(msg)) // Pass in error as 1st argument (the one that's null above)
  }

  image.src = url // Asssign .src to url
}
export default loadImage

// Rewrite cat-image.js with promises:
function loadImage(url) { // No longer references a callback
  return new Promise((resolve, reject) => {
    let image = new Image()

    image.onload = function() {
      resolve(image) // Instead of callback, use resolve. No need to leave 1st argument as `null` for error. Just takes successful argument.

    image.onerror = function() {
      let message = `Could not load image at ${url}`
        reject(new Error(msg)) // Similarly, insteaf of callback, use reject.
      }
    }

    image.src = url
  })
}
export default loadImage


// Rewrite app.js:
import loadImg from './cat-image'

let addImg = (src) => {
  /* Same */
}

// loadImg('img/cat1.jpg'),(err, img2) => { // Compare with callback format
loadImg("img/cat1.jpg").then((img1) => {
  addImg(img1.src)
  loadImg("img/cat2.jpg").then((img1) => {
    addImg(img2.src)
    loadImg("img/cat3.jpg").then((img1) => {
      addImg(img3.src)
    })
  })
})

// We can further condense it with promise.all
Promise.all([
  loadImg("img/cat1.jpg"),
  loadImg("img/cat2.jpg"),
  loadImg("img/cat3.jpg")
]).then((imgs) => {
  imgs.forEach(img => addImg(img.src))
}).catch((error) => {
  /* handle error */
})
```

Here's a function wrapper for jQuery's `.getJSON()` method which fetch JSON results from a url which returns a promise.

```js
var fetchJSON = function(url) {
  return new Promise((resolve, reject) => {
    $.getJSON(url)
      .done((json) => resolve(json))
      .fail((xhr, status, err) => reject(status + err.message))
  })
}

var itemUrls = {
    'http://www.api.com/items/1234',
    'http://www.api.com/items/4567'
  },
  itemPromises = itemUrls.map(fetchJSON)
```

Setup an array of promises which will fulfill with the JSON results of fetching the response from each of the urls in our itemUrls array.

```js
Promise.all(itemPromises)
  .then(function(results) { // we only get here if ALL promises fulfill
     results.forEach(function(item) {
       /* process item */
     })
  })
  .catch(function(err) { // Catches error of first failed promise
    console.log("Failed:", err)
  });
```

Setup that states if url is too slow to respond to our request, use a default value.

```js
// A Promise that times out after ms milliseconds
function delay(ms) {
  return new Promise((resolve, reject) => {
    setTimeout(resolve, ms)
  })
}

// Whichever promise fulfills first is the result passed to our handler
Promise.race([
  fetchJSON('http://www.api.com/profile/currentuser'),
  delay(5000).then(() => { user: "guest" })
])
.then(function(json) {
   // this will be 'guest' if fetch takes longer than 5 sec.
   console.log("user:", json.user)
})
.catch(function(err) {
  console.log("error:", err)
})
```

### 11.0 Iterators & Generators
Generators are a special type of function that pause & resume states (useful for asynchronous code).

```js
"use strict"

function* alwaysEnding() { // This is a generator function. It uses * & yield
  let index=0

  while(index < 2){ // while(true) makes it never stop (always {done: false})
    yield index++ // The function yields (i.e. returns) a generator (object). To be a generator, the object must be iterable.
  }
}
let gen = alwaysEnding() // Instantiates the generator (like a usual function requires)

console.log(gen.next()) // ::{ value: 0, done: false }
console.log("Interrupting!")
console.log(gen.next()) // ::{ value: 1, done: false }. Note it maintains state & continues where it's left off.
console.log(gen.next()) // ::{ value: undefined, done: true }
console.log(gen.throw(new Error("Oops!"))) // ::Can throw error with generator.

// You can call generators from inside a function by using yield* (much like function* calls a generator function, yield* calls a generator yield).
function* aGenerator(i) {
  yield i + 1 // Step 3
  yield i + 2 // Step 4
  yield i + 3 // Step 5
}

function* bGenerator(i) {
  yield i // Step 1: i is 10
  yield* aGenerator // Step 2: passes i to aGenerator
  yield i + 10 // Step 6
}

let gen = bGenerator(10)
console.log(gen.next().value) // ::10. Step 1
console.log(gen.next().value) // ::11. Step 2+3
console.log(gen.next().value) // ::12. Step 4
console.log(gen.next().value) // ::13. Step 5
console.log(gen.next().value) // ::20. Step 6
```

[atchley-promise]: http://www.datchley.name/es6-promises/

[ES6-part-0]: https://stephenkoo.github.io/notes/ES6-condensed-part-0/
[ES6-part-1]: https://stephenkoo.github.io/notes/ES6-condensed-part-1/
[ES6-part-2]: https://stephenkoo.github.io/notes/ES6-condensed-part-2/


---
title: "ES6 Condensed: Part 1/3 - Strict mode, variables, arrow functions"
categories:
    - notes
tags:
    - Javascript/ECMAScript
    - ES6
---
## ES6 Condensed Series
- [**Part 0** - Running ES6 On The Front-end and Back-end][ES6-part-0]
- -> **Part 1** - Strict mode, variables, arrow functions
- [**Part 2** - Arrays, objects, maps & sets][ES6-part-2]
- [**Part 3** - Classes, symbols, promises, iterators & generators][ES6-part-3]

## Let's Learn ES6 In 3 Hours.
This series attempts to condense everything you'll cover from these amazing YouTube courses (as well some other resources) into easily digestible posts:

**Nodecasts' "Javascript ES6" YouTube series**
<iframe width="560" height="315" src="https://www.youtube.com/embed/HIS8juawTmM?list=PLVHlCYNvnqYouIVj3IgK3RmzpnWMaoqkw" frameborder="0" allowfullscreen></iframe>

**Ryan Christiani's "Let's Learn ES6" YouTube series**
<iframe width="560" height="315" src="https://www.youtube.com/embed/LTbnmiXWs2k?list=PL57atfCFqj2h5fpdZD-doGEIs0NZxeJTX" frameborder="0" allowfullscreen></iframe>

If you do find Ryan's videos helpful like I did, take a look at [Ryan's upcoming book "Let's Learn ES6"][lets-learn-es6-book].

## 0.1 Strict mode
Technically introduced in ES5, using strict mode is recommended.

Strict mode:

1. Changes some silent errors by changing into throw errors.
2. Can sometimes be faster.
3. Stops you from accidentally creating global variables, duplicating parameter names, using JS keywords, etc.

```js
"use strict"; // Add to top of file
```

## 1.0 var, let, const

### 1.1 var or let?
Use `let` instead of `var`.

`var` is function-scoped (which is strange);
`let` is block-scoped (exists inside `{}`).

```js
function varFunction() {

  if(true) {
    var name = "Stephen"
  }
  console.log(name)// ::Stephen

  for(var i = 0; i < 5; i++) {
    console.log(i) // ::1,2,3,4
  }
  console.log(i) // ::1,2,3,4
}

varFunction()
```

```js
function letFunction() {

  if(true) {
    let name = "Stephen"
  }
  console.log(name) // ::name is not defined

  for(let i = 0; i < 5; i++) {
    console.log(i) // ::1,2,3,4
  }
  console.log(i) // ::i is not defined
}

letFunction()
```

### 1.2 const
In ES5, we used all caps to denote constants, but in practice it functions as a normal `var` which you is easily changed. Now use `const` instead.

```js
var API_KEY = "S8p3rL337H4k3r"
API_KEY = "See? I changed your API key."

const google_key = "S8p3rL337H4k3r"
google_key = "Huh? I can't change it." // ::"API_KEY" is read-only
```

Note, `const` is unreassignable, but not immutable.
I.e. You can change a `const` object's properties.

```js
const myConst = {
  unreassignable:  "true",
  immutable: "false",
  confusing: "very"
}

myConst = 123 // ::myConst is read-only
myConst.confusing = "not at all"
console.log(myConst) // ::{ ... confusing: 'not at all' }
```

### 2.0 Template literals
Use ``template literals`` to interpolate strings.

```js
var name = "Stephen"
var hobby = "dancing"

console.log("Did you know?\n" + 
name + " likes " + hobby + ".") // Multi-line text used to be imppossible without \n - ::Unterminated string constant

console.log(`Did you know?
${name} likes ${hobby}.`) // You can write multi-line texts with template literals.
```

## 3.0 Functions

### 3.1 Arrow functions
Use arrow functions instead of normal functions.

```js
// Function expression
let add = function(a,b){ return a + b }
// Function declaration
function add(a,b){ return a + b }
// Arrow function
let add = (a,b) => { return a + b }
// If it's only a single-line function, you can further condense it:
let add = (a,b) => a + b

console.log( add(2,3) ) //::5
```

More examples:

```js
let numArray = [1,2,3,4,5,6]

let newArray = numArray.map(function(i) {
  return i * 2
})

let newArray = numArray.map(i => i * 2)

console.log( newArray ) //::[ 2, 4, 6, 8, 10, 12 ]
```

### 3.2 `this` in arrow functions
`this` works differently in arrow functions.

In ES5, we had to use a workaround when using `this` in functions (which refers to different things depending on how a function is called http://javascriptplayground.com/blog/2012/04/javascript-variable-scope-this/.) by putting it in `var self`.

```js
function Person() {
  var self = this
  this.age = 0 // this = Person

  setInterval(function growUp() {
    // this.age++ // this = window (global object), so it won't work.
    self.age++
  }, 1000)
}

var person = new Person();
```
Arrow functions has lexical scope. Think of it as 'parent scope' - `this` refers to the thing 3 steps (instead of 2 steps) above it.

```js
// With arrow functions
function Person() {
  this.age = 0

  setInterval(growUp() => {
    this.age++ // this = growUp > setInterval > Person(). Correct.
  }, 1000)
}

var person = new Person();
```
```js
// Without arrow functions
let person = {
  name: 'Stephen',
  sayName() { // Identical to `sayName: function() {}`
    console.log(`I'm ${this.name}.`) // this = sayName() > person. Correct.
  }
  hobbies: ['Anime', 'Piano', 'Coding'],
  showHobbies() {
    this.hobbies.forEach(function(hobby){ // this = showHobbies() > person. Correct.
      console.log(`${this.name} likes ${hobby}`) // this = this.hobbies.forEach() > showHobbies()/window (global object). Wrong.
    })
  }
}

// With arrow functions
let person = {
  name: 'Stephen',
  sayName: () => {
    console.log(`I'm ${this.name}.`) // this = sayName() > person > window (global object). Wrong.
  },
  hobbies: ['Anime', 'Piano', 'Coding'],
  showHobbies() {
    this.hobbies.forEach((hobby) => { // this = showHobbies() > person. Correct.
      console.log(`${this.name} likes ${hobby}`) // this = this.hobbies.forEach() > showHobbies() > person. Correct.
    })
  }
}

person.sayName()
person.showHobbies()
```

### 3.3 Currying
Instead of many arguments in 1 function, currying is nesting functions with 1 argument all the way down.

Reason why'd you curry is to allow your function to gradually receive the arguments it needs instead of requiring all arguments at once.

funfunfunction explains this well:
<iframe width="560" height="315" src="https://www.youtube.com/embed/iZLP4qOwY8I?list=PL0zVEGEvSaeEd9hlmCXrk5yUyqUag-n84" frameborder="0" allowfullscreen></iframe>

```js
let guy = (name, job, skill) =>
  `${name} is a ${job} that can ${skill}.`

console.log(guy("Stephen", "web developer", "eat 50 burritos"))

// Curried version
let guy =
  name =>
    job =>
      skill =>
        `${name} is a ${job} that can ${skill}.`

console.log(guy("Stephen")("web developer")("eat 50 burritos"))

// A use case would be when name, job & skill were only available later in the code as variables
let stephenGuy = guy("Stephen")
let jobGuy = stephenGuy("web developer")

console.log(jobGuy("eat 50 burritos"))
```

This example was easily curriable. For other functions, functionable libraries like lodash make non-curriable functions curriable.

```js
import _ from "lodash"

let guy = (name, job, skill) =>
  `${name} is a ${job} that can ${skill}.`

guy = _.curry(guy)

console.log(guy("Stephen")("web developer")("eat 50 burritos"))
```

```js
import _ from "lodash"

let dragons =[
  { name: "fluffykins", element: "lightning" },
  { name: "noomi", element: "lightning" },
  { name: "karo", element: "fire" },
  { name: "doomer", element: "timewarp" },
]

// Usual way of filtering dragons by element
let hasElement =
  (element, obj) => obj.element === element

let lightningDragons =
  dragons.filter(x => hasElement("lightning", x)) // hasElement() takes "lightning" as `element`, & x (the filtered object) as `obj`. 2nd part is redundant.

console.log(lightningDragons)

// Currying hasElement with lodash
let hasElement =
  _.curry(element, obj) => obj.element === element

let lightningDragons =
  dragons.filter(hasElement("lightning"))

console.log(lightningDragons)
```

### 3.4 Recursion
<iframe width="560" height="315" src="https://www.youtube.com/embed/k7-N8R0-KY4?list=PL0zVEGEvSaeEd9hlmCXrk5yUyqUag-n84" frameborder="0" allowfullscreen></iframe>
Recursion is a function that calls itself forever (or until you stop it)

```js
let countDownFrom = (num) => {
  if (num === 0) return // This stops recursion when num reaches 0.
  console.log(num)
  countDownFrom(num - 1) // countDownFrom() function in countDownFrom() function. Recursion.
}
countDownFrom(10)
```

```js
// Practical example. Turn this:
let categories = [
  { id: "animals", "parent": null },
  { id: "mammals", "parent": "animals" },
  { id: "cats", "parent": "mammals" },
  { id: "dogs", "parent": "mammals" },
  { id: "chihuahua", "parent": "dogs" },
  { id: "labrador", "parent": "dogs" },
  { id: "persian", "parent": "cats" },
  { id: "siamese", "parent": "cats" },
]
// Into this:
{
  animals: {
    mammals: {
      dogs: {
        chihuahua: {},
        labrador: {}
      },
      cats: {
        persian: {},
        siamese: {}
      }
    }
  }
}

// How:
let makeTree = (rawObj, parent) => {
  let node = {}
  rawObj
    .filter(c => c.parent === parent) // First look for biggest parent (root), i.e. "animals". This line produces an array with parent that is null.
    .forEach(c =>
      node[c.id] = makeTree(categories, c.id)) // Get subtree by calling makeTree() recursive. This time it's not going to get the parent, but the id.
  return node
}
console.log(makeTree(categories)) // Messy, so we stringify it
console.log(
  JSON.stringify(
    makeTree(categories, null),
    null, 2)
)
```

[lets-learn-es6-book]: http://letslearnes6.com/
[ES6-part-0]: https://stephenkoo.github.io/notes/ES6-condensed-part-0/
[ES6-part-2]: https://stephenkoo.github.io/notes/ES6-condensed-part-2/
[ES6-part-3]: https://stephenkoo.github.io/notes/ES6-condensed-part-3/
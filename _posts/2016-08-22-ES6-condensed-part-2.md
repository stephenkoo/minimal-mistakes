---
title: "JavaScript ES6 Condensed: Part 2/3 - Arrays, objects, maps & sets"
categories:
    - lessons
tags:
    - Javascript/ECMAScript
    - ES6
---
## ES6 Condensed Series
- [**Part 0** - Running ES6 On The Front-end and Back-end][ES6-part-0]
- [**Part 1** - Strict mode, variables, arrow functions][ES6-part-1]
- -> **Part 2** - Arrays, objects, maps & sets
- [**Part 3** - Classes, symbols, promises, iterators & generators][ES6-part-3]

## 4.0 Arrays

### 4.1 Rest parameters
Rest parameters converts arguments into an array.

Stop using `.prototype.reduce.call` or `.prototype.slice.call` from ES5; use `...` rest parameters.
Note: `reduce` is a custom array manipulator if `map`, `filter`, `reject` or `find` don't suffice.

```js
function restFunc(...args) { // ... tells code `args` is a rest parameter (puts it in array).
  console.log(args)
} // ::[ 2, 3, 4, 5, 6 ]

function restFunc(...theArgs) {
  return args.reduce((prev,curr) =>  prev + curr)
} // :: 20 (2+3+4+5+6)

function restFunc(a, b, ...nums) { // uses 1st & 2nd argument separately from rest of array.
  return nums.map((i) =>  (a+b) * i)
} // :: [20, 25, 30] ((2+3)*[4,5,6])

console.log( restFunc(2,3,4,5,6) )
```

### 4.2 Spread operators
Use spread operators to turn arrays into arguments.
Stop using `.prototype.apply` from ES5; use spread operators.

```js
let numArray = [2,3,4,5,6]

// Old-school 'apply' method:
// let max = Math.max(numArray) // Will not work because same as Math.max([2,3,4,5,6]) :: NaN
let max = Math.max.apply(null, numArray) // Same as Math.max(2,3,4,5,6) :: 6

// ES6 spread operator method:
let max = Math.max(...numbers)

console.log(max)
```

```js
// Old-school 'concat' method:
let numArray = [2,3,4,5,6]
let newArray = [7,8,9]

let concatArray = newArray.concat(numArray)
console.log(concatArray)

// ES6 spread operator method:
let numArray = [2,3,4,5,6]
let newArray = [7,8,...numArray,9,...numArray] // ::[ 7, 8, 2, 3, 4, 5, 6, 9, 2, 3, 4, 5, 6 ]

console.log(newArray)
```

### 4.3 Destructuring
Used to concisely open arrays & objects.

```js
let myArray = ["longing", "rusted", "seventeen"]
let myObject = {
  keyD: "daybreak",
  keyE: "furnace",
  keyF: "nine"
}
// Old-school destructuring:
let a = myArray[0]
let b = myArray[1]
let c = myArray[2]

let d = myObject.keyD
let e = myObject.keyE
let f = myObject.keyF

console.log(a,b,c) // :: longing, rusted, seventeen
console.log(d,e,f) // :: daybreak, furnace, nine

// ES6 destructuring:
let [a,b,c] = myArray
let {keyD,keyE,keyF} = myObject

console.log(a,b,c) // :: longing, rusted, seventeen
console.log(keyD,keyE,keyF) // :: daybreak, furnace, nine

let { keyD: whatsD } = myObject
console.log(whatsD) // ::daybreak. Shorthand version uses keyD directly as above.

// Remember, you can use rest params in destructuring statements
let [a,...theRest] = myArray
console.log(a,theRest) // :: longing [ "rusted", "seventeen"]
```

#### Practical uses for destructuring (importing JS libraries)
Importing JS library modules with one line.

```js
import React from 'react';
import ReactDom from 'react-dom';
import { Router, Route, Link } from 'react-router'; // ES6 destructuring shorthand

// Non-ES6 method:
let Router = require('react-router').Router
let Route = require('react-router').Route
let Link = require('react-router').Link
```

### 4.4 The for...of loop
You can now iterate over arrays with the for...of loop.

```js
let myLetters = ["A","B","C"]

// Old way
for(let i=0; i < myArray.length; i++) {
  console.log(`I'm ${myLetters[i]}`)
}

// ES6 for...of loop
for(let letter of myLetters){
  console.log(`I'm ${letter}`)
}
```

### 4.5 Array.from() method
Turns array-like objects or iterable objects (like strings) into an array.

```js
function sum() {
  arguments = Array.from(arguments) // This line turns 1,2,3 into [1,2,3] for .reduce() to work
  return arguments.reduce((prev,cur) => prev + cur)
}

console.log( sum(1,2,3) ) // ::6
console.log( Array.from("Hello")) // :: ["H", "e", "l", "l", "o"]
```

### 4.6 Array.find() method
```js
let developers = [
  { name: "Mark",
    skill: "Ruby" },
  { name: "Steve",
    skill: "Swift" },
  { name: "Rose",
    skill: "Ruby" }
]

let rubyDev = developers.find((dev) => { // Finds the first object that matches criteria
  return dev.skill === "Ruby"
})
let rubyDevs = developers.filter((dev) => { // Finds all that matches criteria
  return dev.skill === "Ruby"
})
let rubyDevIndex = developers.findIndex((dev) => { // Finds all that matches criteria
  return dev.skill === "Ruby"
})
console.log(rubyDev) // :: [object Object] { name: "Mark", skill: "Ruby"}
console.log(rubyDevs) // :: [[object Object] { name: "Mark", skill: "Ruby"}, [object Object] { name: "Rose", skill: "Ruby"}]
console.log(rubyDevIndex) // :: 0
```

## 5.0 Objects

### 5.1 Shorthand methods
```js
var obj = {
  // Previously:
  foo: function() {},
  // Now, use shorthand method:
  foo() {}
}
```

### 5.2 Shorthand property assignments
```js
let height = 200
let weight = 120

let cat = {
  // Previously:
  height: height,
  weight: weight
  // Now, use shorthand assignment:
  height,
  weight
}

function modulePattern() {
  function add() {
    /* some code */
  }
  return {
    // Previously:
    add: add
    // Now:
    add
  }
}
```

### 5.3 Object.assign()
`Object.assign()` copies 1 object's property values to another. Used to create copies of objects & possibly alter those copies.

Why is `.assign()` necessary? You can't create object copies just by assigning it to a new variable:

```js
let guy1 = {
  size: "S"
}

let guy2 = guy1
guy2.size = "L"

console.log(guy1) // :: [object Object] { size: "L"}, even though we only changed guy2.
// Why? Because guy2 refers to the same object as guy1. You've just given it another name.

// How to actually create a copy:
let guy2 = Object.assign({}, guy1)
guy2.size = "M"
// OR
let guy2 = Object.assign({}, guy1, {size: "M"})

console.log(guy1) // :: [object Object] { size: "S"}
```

```js
let person = {
  name: 'Stephen',
  height: 200,
  strength: 5
}

let superSaiyan = {
  height: 300,
  strength: 9001
}

let mySaiyan = Object.assign({}, person, superSaiyan) // Note, put target first i.e. {}, then source objects (person, superSaiyan) in the order you want to overwrite them.

console.log(mySaiyan) // :: [object Object] { name: 'Stephen', height: 300, strength: 9001 }
```

### 5.4 Proxies
Proxies allow you to customise handlers for operations on an object (e.g. get, set, delete, apply, construct, getPrototypeOf, enumerate).

```js
let handler = {
  get: function(target, name) { // Here we're altering `get`. "target" is the proxy you're calling it on, "name" is the thing we're searching for.
    // custom logic:
    if (name in target) {
      return target[name]
    } else {
      return "Error."
    }
  }
}

let p = new Proxy({}, handler)

p.foo = "bar"
console.log(p.foo) // ::"bar"
console.log(p.baz) // ::"Error."
```

A practical use of a proxy is if you use get on an extremely nested array & mistype a key, JS throws this unhelpful ugly "TypeError" which doesn't help you identify which key it gets stuck on retrieving.

```js
let example = {}
console.log(example.foo) // ::undefined. No worries, just referred to 1 non-existent key
console.log(example.foo.bar.baz.hi) // ::typeError. You mis-referred to >1 non-existent key & you have no idea which one's wrong.
```

So, use a get proxy which states the last known correct key.

```js
let handler = {
  get: function(target, name) {
    // custom logic:
    if (name in target) {
      return target[name]
    } else {
      return target // this displays the target
    }
  }
}

let example = new Proxy({}, handler)

example.a = "1"
example.b = "2"
console.log(example.a.bar.baz.hi) // ::"1"
```

## 6.0 Maps
Maps are similar to objects but have no prototypes (methods & keys inherited by all objects). (Thus, there is no need to do .hasOwnProperty() checks with maps.)

Object keys must be strings or symbols; but you can have numbers, functions, objects etc. as keys with maps.

```js
let aObject = { // A normal object
  "fruit" : "banana"
}

let aMap = new Map()
console.log(aMap.size) // ::0
aMap.set("fruit","banana")
console.log(aMap) // ::[object Map] { ... }
console.log(aMap.size) // ::1
console.log(aMap.get("fruit")) // ::"banana"

aMap.set(function(){}, "foo") // Can use function as a key.
aMap.set(12, "bar")
aMap.set({"greeting":"hi"}, "baz")
```

### 6.1 WeakMaps
Can't use `.size()` or `.forEach()` (i.e. can't enumerate) on weakmaps, but advantage is it gets garbage collected immediately if there's no other reference.

```js
let aMap = new Map()
let aWeakMap = new WeakMap()

let object1 = {"foo":"bar"}
let object2 = {"bar":"baz"}

aMap.set(object1, "hi")
aWeakMap.set(object2, "hi")
console.log(aMap.get(object1)) // ::"hi"
console.log(aWeakMap.get(object2)) // ::"hi"

object1 = null
object2 = null

aMap.forEach((key,val) => { // List key value pairs
  console.log(key,val) // :: hi [object Object] { foo: "bar" }. Remains. Must be explicitly deleted if desired.
})

// Not can't use forEach on WeakMaps, so list it manually instead:
console.log(aWeakMap.get(object2)) //::undefined. If nothing else is pointing at the reference, WeakMap is garbage collected.
```

## 7.0 Sets & WeakSets
Maps are similar to objects; sets are similar to arrays (without prototypes).
Use maps & sets if you just want to make iterable things without functions of objects & arrays.

```js
var aSet = new Set()

aSet.add(10)
aSet.add("foo")
aSet.add({"foo":"bar"}) // like a usual array, this is treated as a value.
console.log(aSet.size) // ::2
console.log(aSet.has(10)) // ::true
aSet.delete("foo")

for (let item of aSet) {
  console.log(item) // :: 10, { foo: "bar"}
}

```

WeakSets, like WeakMaps, can't enumerate & is weakly referenced.

[ES6-part-0]: https://stephenkoo.github.io/notes/ES6-condensed-part-0/
[ES6-part-1]: https://stephenkoo.github.io/notes/ES6-condensed-part-1/
[ES6-part-3]: https://stephenkoo.github.io/notes/ES6-condensed-part-3/


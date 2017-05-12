---
title: "ES6 Condensed: Part 0/3 - Running ES6 On The Front-end and Back-end"
categories:
    - lessons
tags:
    - Javascript/ECMAScript
    - ES6
---
## ES6 Condensed Series
- -> **Part 0** - Running ES6 On The Front-end and Back-end
- [**Part 1** - Strict mode, variables, arrow functions][ES6-part-1]
- [**Part 2** - Arrays, objects, maps & sets][ES6-part-2]
- [**Part 3** - Classes, symbols, promises, iterators & generators][ES6-part-3]

## Short note on how I write
I don’t like to read superfluous text when learning (including this section). I assume you don’t too.

I try to be succinct so that this can be used as a quick reference guide. This might mean you might sometimes need a
more detailed explanation of concepts I’ve discussed.

If so, I highly recommend reading up documentation ([MDN][mdn] especially) or watching the included videos to help
deepen your understanding of the concepts you learn here.

## Let's Learn ES6 In 3 Hours.
This series attempts to condense everything you'll cover from these amazing YouTube courses (as well some other resources) into easily digestible posts:

**Nodecasts' "Javascript ES6" YouTube series**
<iframe width="560" height="315" src="https://www.youtube.com/embed/HIS8juawTmM?list=PLVHlCYNvnqYouIVj3IgK3RmzpnWMaoqkw" frameborder="0" allowfullscreen></iframe>

**Ryan Christiani's "Let's Learn ES6" YouTube series**
<iframe width="560" height="315" src="https://www.youtube.com/embed/LTbnmiXWs2k?list=PL57atfCFqj2h5fpdZD-doGEIs0NZxeJTX" frameborder="0" allowfullscreen></iframe>

If you do find Ryan's videos helpful like I did, take a look at [Ryan's upcoming book "Let's Learn ES6"][lets-learn-es6-book].

## A. Execute ES6 files in bash (babel-cli)
If you have a .js file written in ES6, you can run it by installing babel.

```bash
npm i -g babel-cli
babel-node test.js
```

## B. Front-end transpiler (gulp + babelify)
If you have a front-end project using an ES6 .js file, use gulp and babelify to transpile ES6 into a browser-friendly ES5 file.

```bash
npm i -g gulp
npm init
npm i --D gulp babelify browserify vinyl-source-stream vinyl-buffer
npm i --S babel-preset-es2015
mkdir build
mkdir src
touch .gitignore
touch app.js
touch gulpfile.js
subl .
```

In gulpfile.js:

```js
const gulp = require("gulp")
const babelify = require("babelify")
const browserify = require("browserify") // Works with babelify
const source = require ("vinyl-source-stream") // Use conventional text streams at the start of your gulp or vinyl pipelines
const buffer = require ("vinyl-buffer") // Convert streaming vinyl files to use buffers

gulp.task("es6", () => {
  browserify("src/app.js")
    .transform("babelify", {
      presets: ["es2015"]
    })
    .bundle()
    .pipe(source("app.js"))
    .pipe(buffer())
    .pipe(gulp.dest("build/"))
})

gulp.task("default", ["es6"], () => {
  gulp.watch("src/app.js", ["es6"])
})
```

In .gitignore:

```
node_modules/
build/
```

```bash
gulp
```

### Creating modules
In `/src` create `myModule.js`:

```js
export default function(text) { // default export (there can only be 1)
  console.log(`This is your text: ${text}`)
}
```

In `/src` create `mathModule.js`:

```js
export function add(a,b) { // named export
  return a+b
}

// export const subtract = function(a,b) { return a-b } // Remember can make function a constant
export const subtract = (a,b) => { // Shortened arrow function
  return a-b
}

export default function(a,b) {
  return a*b
}
```

### Importing and using modules
For single-function modules.
In `app.js`:

```js
import theMod from "./myModule" // You can ommit myModule.js because it knows it's a js file
theMod('burgers') // In dev tools: "This is your text: burgers"

```

Importing multiple-function modules.

```js
// OPTION 1:
// import { add, subtract } from "./mathModule" // If you don't have a default
import multiply, { add, subtract } from "./mathModule" // If you have a default, name it here.

let addNums =  add(6,2) // ::8
let subtractNums =  subtract(6,2) // ::4
let multNums = multiply(6,2) // :: 12

// OPTION 2:
import * as mathMod from "./mathModule"

let addNums =  mathMod.add(6,2)
let subtractNums =  mathMod.subtract(6,2)
let multNums = mathMod(6,2)
```

## C. Back-end (node.js)
As per [Creationix's node version manager Github instructions](https://github.com/creationix/nvm), install the script:

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.31.4/install.sh | bash
nvm i node
node --v8-options | grep "harmony" # View ES6 options
node --harmony_destructuring app.js # E.g. Using ES6 destructuring option when executing file
```
[mdn]: https://developer.mozilla.org
[lets-learn-es6-book]: http://letslearnes6.com/
[ES6-part-1]: https://stephenkoo.github.io/notes/ES6-condensed-part-1/
[ES6-part-2]: https://stephenkoo.github.io/notes/ES6-condensed-part-2/
[ES6-part-3]: https://stephenkoo.github.io/notes/ES6-condensed-part-3/

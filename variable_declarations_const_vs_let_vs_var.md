# Variable Declarations (const vs let vs var)

That's right, three different ways to declare a variable. But right off the bat we can eliminate `var` from our source code. Why? Below I'll try to illustrate some of the quirks of `var` and show you how `const` and `let` aim to correct them.

### Ok, what's wrong with var?
Let's start with the quintessential `var` gotcha. Open up your console, paste in this code sample, and see what you get.

```js
(function() {
  for (var i=0; i < 5; i++) {
    setTimeout(function() {
      console.log('i is ' + i);
    }, 1000);
  }
})();
```

Did you think you'd see `5` five times? Why does this happen? `var` variables are function scoped, so what the JS parser sees is more like this:

```js
(function() {
  var i;
  for (i=0; i < 5; i++) {
    setTimeout(function() {
      console.log('i is ' + i);
    }, 1000);
  }
})();
```

Declaring `var` like we did meant we were actually mutating it on each pass. Ack!

Let's illustrate another issue with `var`:

```js
var myName = 'Phil';
function returnMyName(lastName) {
  if (lastName) {
    var myName = 'Phil ' + lastName;
    return myName;
  }
  return myName;
}

returnMyName();
```

If I don't pass in `lastName`, what does this return? Try it out yourself. If you're surprised that it's `undefined`, let's stop and figure out why.

Remember, any time we used `var`, we create a variable that is function scoped. The JS parser is actually looking at that function and effectively doing something like this:

```js
var myName = 'Phil';
function returnMyName(lastName) {
  var myName;        // Ruh oh. `myName` just got set to undefined
  if (lastName) {
    myName = 'Phil ' + lastName;
    return myName;
  }
  return myName;
}
```

This is called hoisting and it's the source of a lot of confusion for newbie and master Javascripters alike.

### Introducing const and let
Now, let's be clear here. `const` and `let` don't remove hoisting. Hoisting is actually a very useful thing but was weirdly implemented back in '95. `const` and `let` simply correct some side effects.

Let's try those same examples with `let` before we go into the difference between the two.

```js
(function() {
  for (let i=0; i < 5; i++) {
    setTimeout(function() {
      console.log('i is ' + i);
    }, 1000);
  }
})();
```

This now prints out what we expect it to. What changed? Scoping. Both `const` and `let` are block scoped. Any time there's a set of curlies, the code in-between gets a new scope. In this example, each loop and each `setTimeout` callback gets its own `i` reference. No mutating.

Let's try our second example.

```js
let myName = 'Phil';
function returnMyName(lastName) {
  if (lastName) {
    let myName = 'Phil ' + lastName;
    return myName;
  }
  return myName;
}

console.log(returnMyName());
console.log(returnMyName('Aquilina');
```

Awesome! We've fixed that issue too.

### When you should use const and let
While `const` and `let` are similar in terms of their block scoping, they have different constraints - with various implications falling out of those constraints.

`const` has two constraints - 1. You must set it to a value at declaration time and 2. You can't redefine it as another value. Once it's set, it's set for life.

`let` is a little more flexible. You can choose to set it at a later time and you can redefine it whenever you want.

```js
const foo;        // SyntaxError

const foo = 'bar';
foo = 'baz';      // TypeError

let x = 'y';
x = 'z';
// ... no problems there ...

```

Generally speaking, you're going to be using `const` the vast majority of the time. `const` implies the intention that you won't be redefining or mutating this value. Now, this isn't the complete truth but it's close enough to give your readers some more semantic value.

Here's some examples where this isn't the case.
```js
const mutableObject = {};
mutableObject['key'] = 'value';      // not a problem
```

We're going to talk about why we try to avoid mutability in general in 
[the chapter on merging](spread_operator.md).

### Hoisting with const and let
Contrary to popular belief, const and let **are** hoisted. All declarations (`var`, `let`, `const`, `function`, `class`) are hoisted in Javascript.

Why is this important?

This lets us do something like


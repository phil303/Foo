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
  }
  return myName;
}

returnMyName();
```

Guess what this returns if I don't pass in `lastName`. Try it out yourself. If you're surprised at the `undefined` that is returned, let's stop and figure out why.

Remember, any time we used `var`, we create a variable that is function scoped. The JS parser is actually looking at that function and effectively doing something like this:

```js
var myName = 'Phil';
function returnMyName(lastName) {
  var myName;        // Ruh oh. `myName` just got set to undefined
  if (lastName) {
    myName = 'Phil ' + lastName; 
  }
  return myName;
}
```

This is called hoisting and it's the source of a lot of confusion for newbie and master Javascripters alike.

### Introducing const and let
Now, let's be clear here. `const` and `let` don't remove hoisting. Hoisting is actually a very useful thing but was weirdly implemented in JS.

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
  }
  return myName;
}
```
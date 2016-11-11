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

Did you think you'd see 5 five times? Why does this happen? `var` variables are function scoped. What the JS parser sees is more like this:

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

Declaring `var` inside the loop the first time meant we were mutating it on each pass. It's the same variable at the function scope.

Let's illustrate another issue with var:

```js
var myName = 'Phil';
function returnMyName(lastName) {
  if (lastName) {
    var myName = 'Phil ' + lastName; 
  }
  return myName;
}
```

Guess what this returns if I don't pass in `lastName`. Try it out yourself. If you're surprised at the `undefined` that is returned, let's stop and figure out why.

Remember, any time we used `var`, we create a variable that is function scoped. The JS parser is actually looking at that function and effectively doing something like this:

```js
var myName = 'Phil';
function returnMyName(lastName) {
  var myName;    // the source of the bug
  if (lastName) {
    myName = 'Phil ' + lastName; 
  }
  return myName;
}
```

This is called hoisting and it's the source of a lot of confusion for newbie and master Javascripters alike.

### Introducing const and let
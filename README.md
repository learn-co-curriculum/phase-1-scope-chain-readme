# Scope chain

## Overview
Every function in JavaScript has access to a _scope chain_, which includes references to the function's parent scope, the parent scope's parent scope, and so on. In this lesson, we'll discuss how the _scope chain_ allows us to access variables and functions declared in parent scopes within an inner function.

## Objectives
1. Create nested functions.
2. Explain that the environment in which a function is created gets added to its scope chain.
3. Describe how the scope chain makes variables and functions declared in the outer environment available within a nested function.

## Nested scopes and the scope chain
![Nested elevators](https://user-images.githubusercontent.com/17556281/29254327-586f877c-8061-11e7-8059-3416ce5b8960.gif)

In addition to the `#engineering` channel, every software engineer is a member of Flatbook's `#general` channel. Engineers can see all of the messages sent in both channels. If a message in `#general` piques our interest, we can refer to the message in `#engineering` despite the fact that it was posted in `#general`. To bend the analogy back towards functions and scope, everything _declared_ in `#general` is accessible in `#engineering`. `#general`, our global execution context, is effectively a _parent scope_ for `#engineering`.

When we declare a function, a new scope is created, but the code inside the new scope retains a reference to the outer scope. All of the variables and functions declared in the outer environment (the global scope) are accessible within the function. This is called the _scope chain_, and we can see it in action:
```js
const globalVar = 1;
// => undefined

function firstFunc () {
  const firstVar = 2;

  return globalVar + firstVar;
}
// => undefined

firstFunc();
// => 3
```

First, `globalVar` is declared in the global scope. The declaration of `firstFunc()` creates a new scope, but in that new scope we can still reference variables created in the outer (global) scope.

We can think of JavaScript scopes as a nested system:

![Scope chain](https://user-images.githubusercontent.com/17556281/29369662-4093f8ce-8271-11e7-92e4-ba46136eb5f2.png)

All variables and functions declared in outer scopes are available in inner scopes via the scope chain. This can go on ad infinitum, with functions nested in functions nested in functions, each new level creating a new scope that can reference functions and variables declared in its parent scopes through the scope chain:
```js
const globalVar = 1;
// => undefined

function firstFunc () {
  const firstVar = 2;

  function secondFunc () {
    const secondVar = 3;

    return globalVar + firstVar + secondVar;
  }

  return secondFunc();
}
// => undefined

firstFunc();
// => 6
```

Inside `firstFunc()`, we've defined a second function, `secondFunc()`. That second function creates yet another execution context, and in it we can reference `globalVar` and `firstVar` via the scope chain:

![Nested scope chain](https://user-images.githubusercontent.com/17556281/29369663-4094b6ba-8271-11e7-8f7a-e49c79aa9e09.png)

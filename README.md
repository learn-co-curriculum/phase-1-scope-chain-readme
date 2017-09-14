# Scope chain

## Overview
Every function in JavaScript has access to a _scope chain_, which includes references to the function's outer scope (the scope in which the function was declared), the outer scope's outer scope, and so on. In this lesson, we'll discuss how the scope chain allows us to access variables and functions declared in outer scopes within an inner function.

## Objectives
1. Create nested functions.
2. Explain that the environment in which a function is created gets added to its scope chain.
3. Describe how the scope chain makes variables and functions declared in the outer environment available within a nested function.

## Nested scopes and the scope chain
<picture>
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/principles/scope-chain-readme/nested_elevators.webp" type="image/webp">
  <source srcset="https://curriculum-content.s3.amazonaws.com/web-development/js/principles/scope-chain-readme/nested_elevators.gif" type="image/gif">
  <img src="https://curriculum-content.s3.amazonaws.com/web-development/js/principles/scope-chain-readme/nested_elevators.gif" alt="Nested elevators">
</picture>

In addition to the `#engineering` channel, every software engineer is a member of Flatbook's `#general` channel. Engineers can see all of the messages sent in both channels. If a message in `#general` piques our interest, we can refer to the message in `#engineering` despite the fact that it was posted in `#general`. To bend the analogy back towards functions and scope, everything _declared_ in `#general` is accessible in `#engineering`. `#general`, our global scope, is effectively the _outer scope_ for `#engineering`.

For a function declared at the top level of our JavaScript file (that is, not declared inside of another function), its outer scope is the _global scope_. When that new function is invoked, it can access all of the variables and functions declared in the global scope. Upon invocation, the function creates a new scope and **retains a reference to the _outer scope_ in which it was declared**. Inside the new function's body, in addition to variables and functions declared in that function, **we also have access to variables and functions declared in the outer scope**. Let's see that in action:
```js
const globalVar = 1;

function firstFunc () {
  const firstVar = 2;

  return firstVar + globalVar;
}

firstFunc();
// => 3
```

`firstVar` is declared inside the function, and `globalVar` is declared in the outer scope, but we have access to **both** inside `firstFunc()`.

When we invoke `firstFunc()`, the first line of code inside the function, `const firstVar = 2;`, runs first, creating a new local variable. When the JavaScript engine reaches the function's second line, it sees the reference to `firstVar` and says, "Great, I know what that means: it's a local variable!" Then, the engine encounters the reference to `globalVar` and says, "What the heck is this?! That's not declared locally!"

When the engine can't find a local match, it then goes looking in the outer scope and — voilà! — finds a match there. Because of the way functions can look up variables declared in outer scopes, we say they have access to a _scope chain_. Through the scope chain, a function has access to all variables and functions declared in its outer scope.

***Top Tip***: What matters for the scope chain is where the function is declared — not where it is invoked.

We can think of JavaScript scopes as a nested system:

![Scope chain](https://curriculum-content.s3.amazonaws.com/web-development/js/principles/scope-chain-readme/scope_chain.png)

All variables and functions declared in outer scopes are available in inner scopes via the scope chain. This can go on ad infinitum, with functions nested in functions nested in functions, each new level creating a new scope that can reference functions and variables declared in its outer scopes through the scope chain:
```js
const globalVar = 1;

function firstFunc () {
  const firstVar = 2;

  function secondFunc () {
    const secondVar = 3;

    return secondVar + firstVar + globalVar;
  }

  const resultFromSecondFunc = secondFunc();

  return resultFromSecondFunc;
}

firstFunc();
// => 6
```

Inside `firstFunc()`, we've defined a second function, `secondFunc()`. That second function creates yet another scope, and in it we can reference `firstVar` **and** `globalVar` via the scope chain:

![Nested scope chain](https://curriculum-content.s3.amazonaws.com/web-development/js/principles/scope-chain-readme/nested_scope_chain.png)

Inside `secondFunc()`, `firstVar` is accessible via the outer scope, and `globalVar` is accessible via the outer scope's outer scope. Head spinning? Just remember that the scope chain is [scopes all the way down](https://en.wikipedia.org/wiki/Turtles_all_the_way_down). If `a()` is declared inside `b()` and `b()` is declared inside `c()`, `a()` has access to functions and variables declared in its own scope, `b()`'s scope, and `c()`'s scope. That's the scope chain in action!

***NOTE***: The scope chain only goes in one direction. An outer scope **does not have access to things declared in an inner scope**. In the previous code snippet, `firstFunc()` **cannot access `secondVar`**. In addition, two functions declared in the same scope do not have access to anything declared in the other's scope:
```js
const fruit = 'Apple';

function first () {
  const vegetable = 'Broccoli';

  console.log('fruit:', fruit);
  console.log('vegetable:', vegetable);
  console.log('legume:', legume);
}

function second () {
  const legume = 'Peanut';

  console.log('fruit:', fruit);
  console.log('legume:', legume);
  console.log('vegetable:', vegetable);
}
```

Both `first()` and `second()` have access to `fruit`, but `first()` cannot access `legume` and `second()` cannot access `vegetable`:
```js
first();
// LOG: fruit: Apple
// LOG: vegetable: Broccoli
// ERROR: Uncaught ReferenceError: legume is not defined

second();
// LOG: fruit: Apple
// LOG: legume: Peanut
// ERROR: Uncaught ReferenceError: vegetable is not defined
```

## Conclusion
This topic might feel a bit esoteric, but it's critical to understanding how identifier lookups happen in JavaScript. That is, when the JavaScript engine encounters a variable or function, how it knows what value or function to retrieve from memory. If the engine finds the identifier declared locally, it uses that value. However, if it doesn't find a local match, it then looks up (or down, depending on your perspective) the scope chain until it either finds a match in an outer scope or throws an `Uncaught ReferenceError`.

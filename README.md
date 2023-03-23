# Scope Chain

## Learning Goals

- one
- two
- three

## Introduction

Every variable, every function defined in JavaScript belongs to a scope -
whether it's global, functional, or block. Previously, we've learned how to
deduct what scope a variable belongs to. As programmers, when we read or write
code, we can tell what scope a variable belongs to by just looking at its
surroundings. If it's inside a function, it's scoped to that function, and so
on.

Subsequently, we can tell what parts of our code have access to any given
variable. If it's inside a function, only anything _inside_ that function has
access to it. If it's at the top level of a file, it's globally scoped,
therefore any part of our code, after the variable has been defined, has access
to it.

Again, we know this by just looking at everything around it. So, how does a
computer do it? When a JavaScript file runs, it has access to something called
the **scope chain**. This chain provides all the scopes currently accessible by
a given section of code, such as a function, or an `if` statement.

In this lesson, we'll discuss how the scope chain allows us to access variables
and functions declared in outer scopes within an inner function.

## Nested Scopes

Before we further examine the scope chain, there's an elephant in the room we
must discuss. **Scopes can be nested**.

This should not sound surprising. After all, we have already seen examples of
nested scopes. You've even written code with nested scopes. The levels of scopes
themselves demonstrate how they can be nested.

There is always a global scope, and any function or block statement you write
within it will always create a nested one. For example:

```js
const outside = "Hello, I'm in the global scope!";

function foo() {
  const inside = "Hello, I'm in the function scope!";
}
```

In the above code, there are two scopes: the global, and the `foo` function
scope. The `foo` function itself belongs to the global scope, just like the
`outside` variable. However, it also creates its own scope within its function
body, where the `inside` variable belongs. Let's look at it as a bulletpoint
list:

- Global Scope
  - `outside`
  - `foo()`
    - `foo()` scope
      - `inside`

Here, we can see how the `foo()` function scope is nested within the global one.
Because it is nested within another scope, it is considered an _inner scope_,
while the global is the _outer scope_.

The nesting doesn't have to stop there. Theoretically, we can nest as many
scopes as we want.

Let's see it in action:

```js
const outside = "Hello, I'm in the global scope!";

function foo() {
  const inside = "Hello, I'm in the function scope!";

  if (true) {
    const bar = "Hello, I'm in the if block scope!";

    for (let i = 0; i < 10; i++) {
      const baz = "Hello, I'm in the for block scope!";

      if (i < 5) {
        const fuzz = "Wait... where am I?";
      }
    }
  }
}
```

As a bullet point list, the scopes for the above example look like:

- Global Scope
  - `outside`
  - `foo()`
    - `foo()` scope
      - `inside`
      - `if` block scope
        - `bar`
        - `for` block scope
          - `i`
          - `baz`
          - `if` block scope
            - `fuzz`

It's scopes all the way down!

Say, this is starting to look a little bit like a _chain of scopes_, isn't it?
Why, yes. It is.

> **Note**: Just because you _can_ doesn't mean you always _should_. Nesting too
> many things can quickly become unruly. Your code can begin to look messy and
> hard to read and maintain. Nesting is fine and sometimes necessary, just
> beware to not do too much!

## Scope Chain

Now that we've seen how scopes can chain together, we can examine how JavaScript
traverses this chain during variable lookup.

Let's use an example to step through how JavaScript utilizes scope:

```js
const player = "Ohtani";

function isOut(strikes) {
  let atBat = true;
  const availableStrikes = 3;

  function addStrike() {
    const totalStrikes = strikes + 1;
    console.log(player + " has " + totalStrikes + " strike(s)!");

    return totalStrikes;
  }

  if (strikes >= availableStrikes) {
    atBat = false;
    const message = player + " is out!";
    console.log(message);
  } else {
    return addStrike();
  }
}

isOut(1);
```

> <details><summary>Bonus: Before we step through the code or run it, can you determine what the above code will output?</summary><p>
> Ohtani has 2 strike(s)!

</p></details>

Breaking down the above example into our bulletpoint scope chain, we get:

- Global scope
  - `player`
  - `isOut()`
    - `strikes`
    - `atBat`
    - `availableStrikes`
    - `addStrike()`
      - `totalStrikes`
    - `if` block
      - `message`
    - `else` block

Let's walk through the code line by line, scope by scope, starting from the top.

First, we have two things in our global scope, the outermost scope. The `player`
variable, and the `isOut` function.

```js
const player = "Ohtani";

function isOut(strikes) {
  ...
}

isOut(2);
```

At the bottom of the code, `isOut()` is invoked. When the code executes and gets
to that line, it will look for that function within the current scope. The
current scope, where the function is trying to be accessed, is global. Because
`isOut()` is a globally scoped function, it is accesible and will run
successfully.

As we previously learned, the `isOut()` function also creates its own scope. In
our example, it has several things inside its scope.

```js
function isOut(strikes) {
  let atBat = true;
  const availableStrikes = 3;

  function addStrike() {
    ...
  }

  if (strikes >= availableStrikes) {
    ...
  } else {
    ...
  }
}
```

First, it has the parameter `strikes`. Although the parameter isn't defined
inside of the function body, between `{}`, it is part of the function scope. It
also has two variables, a function, and `if/else` blocks.

Outside of these definitions, the `isOut()` function scope does not try to
access any variables. There's no explicit use of the scope chain here. Yet.

Nested inside the `isOut()` scope, however, is the function `addStrike()`.
Again, as it is a function, it creates its own scope.

```js
function addStrike() {
  const totalStrikes = strikes + 1;
  console.log(player + " has " + totalStrikes + " strike(s)!");

  return totalStrikes;
}
```

When declaring `totalStrikes`, another variable is utilized in its definition,
`strikes`. JavaScript will see this variable, and first try to look for it
within the current scope of `addStrike()`. When it doesn't find it anywhere in
`addStrike()`, it will go look for it in the closest outer scope in the chain.
In this case, the `isOut()` function scope. Once it discovers that a `strikes`
variable does exist in that scope, it will use its value and stop traversing the
chain.

This is the general process of how JavaScript uses the scope chain to resolve
variable lookup.

A similar process happens in the next line of code. In the `console.log()`, the
`player` variable is used, but it does not exist within `addStrike()`. When
JavaScript looks for it in the closest outer scope, it cannot find it in
`isOut()` either. So, it goes one step further out to the global scope, where it
successfully finds `player`'s value.

**When looking up variables, JavaScript will always look at its current scope
first. Then, if unfound, it will continue moving up the scope chain until the
the variable's value is found.**

Next, let's take a look at the `if` statement block.

```js
if (strikes >= availableStrikes) {
  atBat = false;
  const message = player + " is out!";
  console.log(message);
}
```

For the conditional, it uses variables from the `isOut()` scope that it has
access to. It also uses a variable, `player`, from the global scope when
declaring `message`. In all these cases, the process is as we just discussed. It
will keep going up the scope chain until it finds the variables needed.

The new point we want to pay attention to here is the value reassignment of the
`atBat` variable.

As we previously learned, when _declaring_ a variable with no keyword, it will
become a globally scoped variable, regardless of where it's defined. In this
case, we intuitively know, from what we've learned about JavaScript so far, that
isn't what's happening here. `atBat` isn't being _declared_, it's being
_reassigned_ a new value.

We can attribute parts of this behavior to the scope chain. When a variable is
written as such, `atBat = false`, without a keyword, JavaScript will look
through the scope chain. It will check if the variable was defined anywhere else
accessible first. If it finds it, it treats it as a reassignment. If it does not
find it anywhere, it will then declare it as a new global variable.

In this case, `atBat` is found in the `isOut()` function scope. Thus, it gets
reassigned rather than globally declared.

Those are the basics to how the scope chain works! If the concept is still
fuzzy, that is OK. Your sense for scope will develop the more code you write,
and the more `ReferenceError` bugs you run into and fix.

As a matter of fact, let's do some of that together.

## Trying to Break the Scope Chain

To further understand the scope chain, we must try to break it. That will let us
see how much wiggle room the scope chain has, if any.

What would happen if we try to invoke `addStrike()` _outside_ of `isOut()`?

```js
const player = "Ohtani";

function isOut(strikes) {
  let atBat = true;
  const availableStrikes = 3;

  function addStrike() {
    const totalStrikes = strikes + 1;
    console.log(player + " has " + totalStrikes + " strike(s)!");

    return totalStrikes;
  }

  if (strikes >= availableStrikes) {
    atBat = false;
    const message = player + " is out!";
    console.log(message);
  } else {
    return addStrike();
  }
}

addStrike(); // <= New addition
isOut(1);
```

It throws a `ReferenceError`, telling us that `addStrike` does not exist. This
is because the **scope chain can only go in one direction**. The chain can only
be traversed starting from inwards and going outwards. It cannot be traveled
from the outside in.

In other words, **inner scopes _can_ access anything in its outer scopes. But
outer scopes _cannot_ access anything in its inner scopes.**

Let's try one more wacky thing. What if we try to access `totalStrikes` within
our `if` block?

```js
const player = "Ohtani";

function isOut(strikes) {
  let atBat = true;
  const availableStrikes = 3;

  function addStrike() {
    const totalStrikes = strikes + 1;
    console.log(player + " has " + totalStrikes + " strike(s)!");

    return totalStrikes;
  }

  if (strikes >= availableStrikes) {
    atBat = false;
    const message = player + " is out with " + totalStrikes + " strikes!"; // <= New addition
    console.log(message);
  } else {
    return addStrike();
  }
}

isOut(3); // <= Changed argument to 3
```

Again, we get a `ReferenceError`, telling us that `totalStrikes` is not defined.
This is because **scopes on the same level do not have access to anything within
each other's scopes**. Again, the scope chain only goes _one way_, from inner to
outer. Or, in this context, it only goes up. It can not go sideways.

## Conclusion

Let's summarize the rules we've discovered throughout this scope chain journey.
You can refer back to this list as a cheatsheet:

- Scopes can be nested, which creates a scope chain.
  - Each function or block of code has its own scope chain.
- When looking up variables, JavaScript will always look at its current scope
  first.
  - If a variable is not found within the current scope, it will move _up_ the
    scope chain until it finds it.
- When a variable is given a value without a keyword, JavaScript will first try
  to look for the variable in its scope chain.
  - If the variable exists in the chain, it will _reassign_ the value.
  - If the variable does not exist in the chain, it will _declare_ the variable
    and it will become globally scoped.
- The scope chain can only be traversed in one direction.
  - From the inner scope to the outermost scope, never the reverse, nor
    sideways.

This topic might feel a bit esoteric, but it's critical to understanding how
identifier lookups happen in JavaScript. That is, when the JavaScript engine
encounters a variable or function, how it knows what value or function to
retrieve from memory. If the engine finds the identifier declared locally, it
uses that value. However, if it doesn't find a local match, it then looks up the
scope chain until it either finds a match in an outer scope or throws a
`ReferenceError`.

## Resources

- [Bubbl.es: The Scope Chain Visualized](https://jsbubbl.es/)

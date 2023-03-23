# Scope Chain

## Learning Goals

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

Here, we can clearly see how the `foo()` function scope is nested within the
global one.

The nesting doesn't have to stop there. Theoretically, we can nest as many
scopes as we want. Let's try it out:

```js
const outside = "Hello, I'm in the global scope!";

function foo() {
  const inside = "Hello, I'm in the function scope!";

  if (true) {
    const bar = "Hello, I'm in the if block scope!";

    for (let i = 0; i < 10; i++) {
      const baz = "Hello, I'm in the for block scope!";

      if (i == 1) {
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

It's scopes all the way down! Say, this is starting to look a little bit like a
chain of scopes, isn't it? Why, yes. It is.

> **Note**: Just because you _can_ doesn't mean you always _should_. Nesting too
> many things can quickly become unruly. Your code can begin to look messy and
> hard to read and maintain. Nesting is fine and sometimes necessary, just
> beware to not do too much!

## Scope Chain

## Resources

- [Bubbl.es: The Scope Chain Visualized](https://jsbubbl.es/)

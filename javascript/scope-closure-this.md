# Scope, Closure, and `this` in JavaScript

> These concepts form the mental model of JavaScript.
> Most bugs and interview questions trace back to misunderstanding them.

---

## Why These Concepts Matter

Before learning frameworks, I wanted to understand **how JavaScript actually thinks**.
Scope, closure, and `this` explain:

-   Why variables are accessible (or not)
-   How functions remember data
-   Why `this` sometimes behaves unexpectedly

---

# Scope

### What I Thought Initially

Variables declared inside a function are only available there.
Global variables are accessible everywhere.

### What I Learned

JavaScript uses **lexical scope**, meaning:

> Scope is determined by **where code is written**, not where it’s called.

There are three main scopes:

-   Global scope
-   Function scope
-   Block scope (`let`, `const`)

### Example (Wrong Assumption)

````js
function foo() {
  let x = 10;
}

console.log(x); // ❌ ReferenceError

### Correct Understanding

let x = 5;

function foo() {
  console.log(x); // ✅ 5 (from outer scope)
}

foo();
````
Key Rule
Inner scopes can access outer scopes, but not the other way around.

# Closure
### What I Thought Initially
Closure is an advanced or rare concept.

### What I Learned
A closure is created every time a function remembers variables from its lexical scope, even after the outer function has finished executing.

### Example
````js
function counter() {
  let count = 0;

  return function () {
    count++;
    return count;
  };
}


const increment = counter();

increment(); // 1
increment(); // 2
````
### Why This Works

-    counter() has finished execution
-    But the inner function still has access to count
-    JavaScript keeps the scope alive

### Real Usage

-    React hooks
-    Event handlers
-    Encapsulation without classes

# this

### What I Thought Initially
>    this refers to the function itself.

### What I Learned
this is determined by how a function is called, not where it’s defined.

````js
const user = {
  name: "Aref",
  sayName() {
    console.log(this.name);
  },
};

user.sayName(); // ✅ "Aref"
````

````js
const say = user.sayName;
say(); // ❌ undefined (or window in non-strict mode)
````
### Arrow Functions

Arrow functions do not bind this.
They inherit this from the surrounding scope.

### Common Mistakes I Made

-    Expecting this to be stable
-    Forgetting closures exist implicitly
-    Confusing block scope with function scope

### Rule of Thumb

-    Scope → where variables live
-    Closure → functions remember scope
-    this → decided at call time

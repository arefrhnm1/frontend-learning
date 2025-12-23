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

## Scope

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

## Closure
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

## this

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


# Controlling `this` (call, apply, bind)

## Why do we need to control `this`?

### What I Thought Initially
-    JavaScript always knows what `this` should be
-    I don't need to manually change `this`

### What I Learned

-    `this` depends on how a function is called
-    When a function is detached from its object, `this` is often lost -    JavaScript provides call, apply, and bind to manually set `this`

>    `call`, `apply`, and `bind` do not change the function
>    They only change how it is executed

## `call` - invoke immediately with a custom `this`

````js
function greet(){
    return `Hello, ${this.name}`
}

const user = {name: 'John'}

greet();
//Hello, undefined (or window.name)

greet.call(user);
//Hello, John
````
###    `call`:
-    Executes the function immediately
-    First argument = value of `this`
-    Next arguments = function parameters

##    `apply` - same as call, different arguments

````js
function sum(a, b){
    return a + b;
}

sum.apply(null, [2, 3]); // 5
sum.call(null, 2, 3); //5
````
### `apply` VS `call`

-    `call` -> arguments separated by comma
-    `apply` -> arguments as an array
-    In modern JS, `apply` is less common because of spread(...)


## `bind` - does NOT execute immediately

````js
function greet(){
    return `Hello, ${this.name}`;
}

const user = {name: 'john'}

const boundGreet = greet.bind(user);

boundGreet();    //Hello, John
````

### `bind`:
-    Returns a new function
-    Permanently binds `this` to that function
-    Does not execute, just prepares

> bind is mostly used for callbacks and event handlers

##    Real-world example
````js
const user = {
    name: 'John',
    sayName(){
        setTimeout(function(){
            console.log(this.name);
        }, 1000);
    },
};

user.sayName(); // undefined

````
### Solve with `bind`:
````js
setTimeout(function(){
    console.log(this.name);
}.bind(this), 1000)

````

### Common Mistakes
- Thinking `bind` executes the function
- Using arrow functions when `this` is needed
- Forgetting that `call` and `apply` execute immediately

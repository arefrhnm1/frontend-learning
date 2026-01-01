# Array, Object & Immutability in JavaScript

## Why This Topic Matters
Working with arrays and objects is unavoidable in JavaScript.
Understanding how data is copied, mutated, or preserved is critical
to avoid bugs, especially in React and modern frontend development.

---

## 1. Arrays and Objects Are Reference Types

### What I Thought Initially
I thought arrays and objects are copied just like numbers or strings.

### What I Learned
- Arrays and objects are **stored by reference**
- Assigning them to another variable does NOT create a copy
- Both variables point to the same memory location

### Example
````js
const a = [1, 2, 3];
const b = a;

b.push(4);

console.log(a); // [1, 2, 3, 4]
````

---

## 2. Primitive vs Reference Types (Quick Recap)
### Primitive Types

- number

- string

- boolean

- null

- undefined

- symbol

- bigint

> **They are copied by value**
```js
let greet = "Hello";
greet += ", World";
```
#### What we think:
 **The string `"Hello"` is changed**
#### What actually happens:
1. `"Hello"` is created
2. `"Hello, World"` is a completely new string
3. The `"greet"` variable only chages its reference

**No strings are changed in place**

> Result: Primitives are truly **immutable**


### Reference Types

- object

- array

- function

> They are copied by reference

---

## 3. What Is Mutation?
### Definition

> Mutation means changing the original data directly.

#### Example (Mutation)
```js
const user = { name: "John" };

user.name = "Ali";

console.log(user.name); // Ali
```

> This modifies the original object.

---

## 4. What Is Immutability?
### Definition

> Immutability means not changing the original data, but instead
creating a new copy with changes applied.
- Data should not change after creation.
- If it is to change -> a new copy must be created.

Not "unchanged",but:
- The old copy must remain intact.
- A new copy must be created.

### Why It Matters

- Predictable State

- Easier debugging

- Required in React state updates / Redux-Friendly

- Prevents hidden side effects

- Functional Programming

---

## 5.Why are Arrays Mutable?
### Example:
```js
let ages = [42, 22, 35];
ages.push(8)
```
### Very important point:
#### The `ages` variable:
- Does not hold the array itself
- It holds the memory address of the array

#### What does push do?
- It modifies the same previous array
  at the same memory address.

#### Meaning:
- Adress: Fixed
- Content: Changed

---

## 6. Where exactly is the Mutability problem?
### Here's the crux of the matter

#### Assume:

- You have Redux

- You have React state

- You have Change Detection
```js
ages.push(8);
```

#### From the system's perspective:
```js
oldAddress === newAddress // true
```
#### So:

- React says: "Nothing has changed"

- Redux says: "state is the same"

- setter doesn't execute

**But the data has actually changed**
**And that means dirty and invisible bugs**

---

## 7. Immutability with Array (the right way)
### The right way:
```js
ages = [...ages, 8];
```
### What happens here?

- `[...ages]` → new copy

- 8 is added

- ages points to the new address

#### Result:

- Address: new

- Content: new

**React / Redux / Angular notice the change**

---

## 8. Mutable vs Immutable Array Methods
### Immutable (good):
- map
- filter
- slice
- concat
### Mutable (caution ⚠️):
- push
- pop
- splice
- sort
- reverse
#### Rule of thumb:

> Any method that "returns something" is usually safer
  Any method that "manipulates the same array" is dangerous

---

## 9. Immutable Updates with Arrays
### Add item
```js
const numbers = [1, 2, 3];

const newNumbers = [...numbers, 4];
```

### Remove item
```js
const filtered = numbers.filter(n => n !== 2);
```

### Update item
```js
const updated = numbers.map(n =>
  n === 2 ? 20 : n
);
```

---

## 10. Immutable Updates with Objects
### Using Spread Operator
```js
const user = { name: "john", age: 25 };

const updatedUser = {
  ...user,
  age: 26
};

console.log(user.age); // 25
console.log(updatedUser.age); // 26
```

---

## 11. Real World Example (React Mindset)
### Wrong (Mutation)
```js
state.items.push(newItem);
```

### Correct (Immutable)
```js
setState({
  items: [...state.items, newItem]
});
```

---

## 12. Common Mistakes

- Thinking spread operator creates a deep copy

- Mutating state directly in React

- Forgetting nested objects are still references

---

## 13. Interview Notes

- Explain reference vs value clearly

- Know why immutability is important in React

- Be able to spot mutation bugs
# 01-javascript-fundamentals

## Concept (Simple)
JavaScript is the language that makes websites interactive. You use it to store values, make decisions, and run steps.

## Key Ideas
- `const` is for values that do not change
- `let` is for values that can change
- Functions are reusable steps
- `if/else` helps your code make decisions

## Your Code (Where to Look)
- Prototype file: `quizzical-hodgkin/src/App.jsx`
- In your main frontend: `assure-frontend-live/src/`

## Mini Example
```js
const name = "Acharya";
function greet(user) {
  return "Hello " + user;
}

if (name === "Acharya") {
  console.log(greet(name));
}
```

## Build Exercise
Write a function that takes an MCC code and returns a category name.

## Questions to Answer Later
- Where are the category names stored in my system?
- Which file is responsible for loading MCC codes?

https://developer.mozilla.org/en-US/docs/Learn
# Javascript objects

# Object prototypes
Every object in JavaScript has a built-in property, which is called its prototype.  
The prototype is itself an object, so the prototype will have its own prototype, making what's called a prototype chain.

# Setting a prototype
```javascript
const personPrototype = {
    greet() {
        console.log("hello!");
    },
};
const carl = Object.create(personPrototype);
carl.greet();
```
```javascript
const personPrototype = {
    greet() {
        console.log(`hello, my name is ${this.name}!`);
    }
};
function Person(name) {
    this.name = name;
}
Object.assign(Person.prototype, personPrototype);
```
It's common to see this pattern, in which methods are defined on the prototype, but data properties are defined in the constructor.  

# Classes and constructors
```javascript
class Person {
    name;
    constructor(name) {
        this.name = name;
    }
    introduceSelf() {
        console.log(`Hi! I'm ${this.name}`);
    }
}

const giles = new Person("Giles");
giles.introduceSelf();
```
# Inheritance
```javascript
class Professor extends Person {
    teaches;
    constructor(name, teaches) {
        super(name);
        this.teaches = teaches;
    }
    introduceSelf() {
        console.log(`My name is ${this.name}, and I will be your ${this.teaches} professor.`);
    }
    grade(paper) {
        const grade = Math.floor(Math.random()*(5-1)+1);
        console.log(grade);
    }
}
```
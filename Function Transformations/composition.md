## Function Composition

[problem](https://leetcode.com/problems/function-composition/?envType=study-plan-v2&envId=30-days-of-javascript)

solution:

```
var compose = function(functions) {
	return function(x) {
        if (functions.length === 0) return x;

        for (let i = functions.length -1; i >= 0; i--){
        // execute first callback passed to outer function on x, store result
        // call each subsequent callback on previous result
            x = functions[i](x);
        }
        return x;
    }
};
```

The function accepts an array of functions and composes them from an initial value x

- we first return a new function that will accept the initial value as an argument
- we still maintain reference to the array of functions due to closure, so we can iterate over the functions from _right to left_.
- For example if we were given the functions [a, b, c], we would want to return **a(b(c(x)))**
- we invoke each function, passing x as an argument, an reassign x to the new value each time, therefore, x is updated on each iteration as each function is called with the result of the previous one

## Real world use cases of composition

In JavaScript, composition is commonly used as an alternative paradigm to constructor functions/classes for instantiating new objects.

(insert section on drawbacks of constructors/classes)

First to understand how composition is used to create objects, lets briefly cover factory functions

### Factory Functions

A factory function is simply a function that returns an object like so

```
function person(name, age){
     return {
        intro() {
            console.log(`hi, my name is ${name}, I am ${age} years old`)
        }
    }
}

const nirav = person('Nirav', 24);
// {intro: ƒ}

nirav.intro()
// logs: hi, my name is Nirav, I am 24 years old
```

- we don't need to have name and age as properties on the returned object sine they are accessible via Closure - this also ensures that they are kept private and only accessible via the defined object methods
- notice the intro method above does not need to use the 'this' keyword, since the method is stored directly on the returned object - rather than its prototype as with a class

The same goal could be achieved with a class (syntax sugar over a constructor function) like so

```
class Person {
    constructor(name, age){
        this.name = name;
        this.age = age;
    }
    intro(){
        console.log(`hi, my name is ${name}, I am ${age} years old`)
    }
}

const nirav = new Person('nirav', 24)
// Person {name: 'nirav', age: 24}
```

Say we want to create some different types of people objects that inherit from the person class

For example, a frontend dev:

```
class frontEndDev extends Person  {
    constructor(name, age){
        super(name, age)
        this.title = 'frontEndDev'
    }
    buildReactComponent(){
        console.log(`${this.name} is creating the component`);
    }
}
```

and also a backend dev:

```
class backEndDev extends Person  {
    constructor(name, age){
        super(name, age)
        this.title = 'backEndDev'
    }
    runServer(){
        console.log(`${this.name} is spinning up a server`);
    }
}
```

You may see where this is going. We will also want a fullStackDev that can build React components and also run a server. However, the buildReactComponent() and runServer() methods are mutually exclusive between the the frontEndDev and backEndDev classes.

- To give a fullStackDev class access to both methods, one option is to duplicate those methods and place both on the new fullStackDev class (code duplication bad).
- The other option is to have both methods defined on the Person object ('god object' with all required methods) and have every developer class inherit from it. However then each developer class will also be inheriting methods that it has no use for (frontEndDev will inherit runServer() method, backEndDev will inherit buildReactComponent())
- by tightly coupling data and its related methods together (OOP paradigm with classes), we encounter issues with scalability because the available methods are dictated by what objects are, rather than what they do

Here is an alternative solution using composition:

First we define the intro() buildReactComponent() and runServer() methods as their own individual functions that act on a shared state

```
const intro = (state) => console.log(`hi, my name is ${state.name}, I am ${state.age} years old`)

const buildReactComponent = (state) => console.log(`${state.name} is creating the component`);

const runServer = (state) => console.log(`${state.name} is spinning up a server`);

```

and now, we can have three different factory functions instead of classes, that can use these methods as needed. Designed based on what the objects DO

a person can only intro

```

function person(name, age){
    return {
        intro: () => intro({name, age})
    }
}

const bob = person('bob', 24)
bob.intro()
// hi, my name is bob, I am 24 years old

```

A frontEndDev can intro and buildReactComponent

```
function frontEndDev(name, age){
    return {
        intro: () => intro({name, age}),
        buildReactComponent: () => buildReactComponent({name})
    }
}
```

A backEndDev can intro and runServer

```
function backEndDev(name, age){
    return {
        intro: () => intro({name, age}),
        runServer: () => runServer({name})
    }
}
```

A fullStackDev can do it all

```
function fullStackDev(name, age){
    return {
        intro: () => intro({name, age}),
        buildReactComponent: () => buildReactComponent({name}),
        runServer: () => runServer({name})
    }
}
```

As you can see, each of the objects returned by the factory functions can use the functions that they were composed of

```
brynn.buildReactComponent();
// Brynn is creating the component

danae.runServer();
// Danae is spinning up a server

nirav.buildReactComponent();
// Nirav is creating the component

nirav.runServer();
// Nirav is spinning up a server
```

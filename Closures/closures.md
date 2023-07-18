# Closures

A closure is created by a function having a persistent reference to its lexical environment.

Lets unpack that:
Function - procedure that is given an input, performs a series of instructions, and returns an output based on the input.

Lexical Environment - the surrounding data/variables that a function has access to - its scope.

Basic Example:

we have an outer function 'counter' which accepts a number and returns an inner function 'increment' which adds 1 to the value of that number

Lets walk through the code, line by line:

```
function outer(num){
    return function increment(){
        num = num + 1;
        return num;
    }
}

const add1 = outer(5); // definition of increment function

add1() // 6

```

- in the global scope, we encounter a function definition _outer_, we store in global memory
- next we encounter a variable _add1_ set to the evaluated result of invoking outer with the argument 5
- the invocation of _outer_ creates a new function execution context in which the argument of _5_ is stored under the label _num_ in local memory
- inside the function body we encounter the **return** keyword followed by a function definition, which means the thread of execution leaves the function and stores the body of increment function under the variable add1
- move to the next line and we encounter another function invocation _add1()_ which evaluates to invoking the increment function that was returned into global memory, we push it onto the call stack and open a new execution context
- inside increment's local variable environment we see a reference to num being re-assigned to num + 1
- Here's what interesting: the function body of increment was returned into global memory under the label add1, which is now outside the scope of the outer function where the num variable exists. However, because increment was originally defined inside the outer function, it was given a persistent reference to all the variables within its lexical scope - a.k.a _closure_
- the num parameter that evaluated to 5 is still accessible to the increment function even after being invoked from the global scope because it brought the reference to num along with it.
- a function's closure is determined lexically (where it was defined), as opposed to dynamically (where it was called from)

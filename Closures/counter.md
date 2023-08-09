## Counter

[problem](https://leetcode.com/problems/counter/?envType=study-plan-v2&envId=30-days-of-javascript)

My Solution:

```JavaScript
var createCounter = function(n) {
    // keep track of calls to inner function so that we do not increment n on the first call
    let calls = 0;
    // inner function body is returned into global memory when outer is called. Maintains referecne to count variable
    return function() {
        calls++;
        if (calls === 1) return n;
        n = n + 1; // increment count during each invocation
        return n;
    };
};

```

test:

```JS
    const counter = createCounter(10)
    counter() // 10
    counter() // 11
    counter() // 12

```

### Explanation:

The purpose of this function is know wether the inner function is being called for the first time, and to return n if so, and to continue incrementing the return value on each subsequent call.

- we have a closure because the returned inner function has access to the parameters and variables set in the outer function.
- we can keep track of the number of calls to the inner function, as well as the accumulating value of n
- notice that these values (calls and n) are not reset on each function call because they exist in the outer function which we only call once, however the inner function maintains a persistent reference to them, and can modify and remember the updated the values between each subsequent call.

nicer solution:

```JS

var createCounter = function(n) {
    return ()=> n++
};

```

Explanation:

- this solution takes advantage of the fact that 'n = n + 1' and 'n++' do not do the exact same thing
- n = n + 1 is an assignment operator to reassign the value of n
- n++ is the [post increment](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Increment) operator which increments n, but returns the original value of n before it was incremented
- therefore on the first inner function call n++ will still return n and not n + 1, but since n is still incremented in memory, its updated value is returned by the next call

```

```

```

```

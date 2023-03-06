---
theme: default
transition: slide-left
---

# Functional programming in JavaScript

<!--
Welcome to this presentation about Functional Programming in Javascript.

We'll see what's function programming, how you can use it in your code, and why it can be useful.
-->


---

## Introduction
### What is Functional programming

<v-clicks>

- programming Paradigm
- Pure Functions
- Avoid Side Effects
- Avoid Shared States
- Avoid Mutations

</v-clicks>

<!--
Functional programming is a programming paradigm where programs are constructed by applying and composing functions.
(Source Wikipedia)

JavaScript is not a purely functional programming language. But we'll see where and how we can apply those principles
using standard JavaScript.

I'll also give you some examples of libraries that can facilitate the usages of functional programming in JavaScript.

Functional programming requires 4 conditions. 

We'll go through the definition of each point right away.
-->


---
---
## Pure Functions

A pure function returns the same output when the same input is provided.

```js
const sum = (a, b) => {
    return a + b
}
```

<!--
A pure function returns the same output when the same input is provided.

In this example, the sum function will always return the same results for the same a and b values.
-->


---
---
## Side Effects

A side effect is a change of a value globally.

```js
let myVariable = 'hello';

const sideEffectsFunction = () => {
  myVariable = 'world';
  return myVariable
};
```

<v-click>

```js
let myVariable = 'hello';

sideEffectsFunction();

// We can't determine whether the value has changed or not.
```

</v-click>

<!--
Here's an example of side effect. This is a change outside the function.
We see that when the function is executed, the value of `myVariable` changes.
The code is harder to debug and test.

It also affects the readability of the function when it is
executed because we don't know the impact it has on the variable.

Side effects aren't reduced to the change of a global value. But also to
1. Changing the original value of a function arguments
2. Throw exceptions
3. Printing or logging
4. Start an external process
5. Invoke functions that contain side effects.
-->


---
---
## Shared States

Shared states are stored data shared between multiple functions.

```js
const sharedStates = {
    teamAScore: 10,
    teamBScore: 20,
}

const updateTeamAScore = (scoreIncrement) => {
    sharedStates.teamAScore += scoreIncrement
}

const updateTeamBScore = (scoreIncrement) => {
    sharedStates.teamBScore += scoreIncrement
}
```

<!--
Both updateTeamAScore and updateTeamBScore are mutating the value of the sharedState variables.
It is an example of a shared states. Both functions rely on the sharedStates variable.

It breaks the principle of functional programming because the execution of the function is not only depending
on its parameters.
-->


---
---
```js
const sharedStates = {
    teamAScore: 10,
    teamBScore: 20,
}

const updateTeamScore = (teamName, scoreIncrement) => {
    sharedStates[teamName] += scoreIncrement
}
```

<!--
Even if we simplify the previous two functions into one, we'd still have side effects, because
the result of the function is mutating a shared state and is not determine only by the given inputs
-->


---
---
## Immutability

Immutability is about not changing the value assigned to a variable.

```js
let a = 1;

a = 2; // mutation
```

<v-click>

```js
const a = 1;

a = 2; // Error
```

</v-click>

<!--
If we use the `let` keyword, JavaScript lets us mutate the value assigned to a variable.

As you probably know `const` will prevent us to assign a new value to a variable.

But we'll see right now that it's not that simple in JavaScript.
-->


---
---
### Objects

```js {1-4|6|8|all}
const myObj = {
    a: 1,
    b: 2
};

myObj.a = 5;

console.log(myObj)
```

<v-click>

```sh
{ a: 5, b: 2 }
```

</v-click>

<!--
In this example, we declare the variable myObj with the const keyword. As we saw in our previous example,
this should prevent us from mutating the value of the variable.

If we assign the value 5 to the attribute `a`, what will happen?

The value got mutated even though we used the const keyword.
-->

---
---
#### How to prevent it?
```js
const myObj = {
    a: 1,
    b: 2
};

const myObj2 = {...myObj }

myObj2.a = 5

console.log(myObj)
console.log(myObj2)
```

<v-click>

```shell
{ a: 1, b: 2 }
{ a: 5, b: 2 }
```

</v-click>

<!--
One way to prevent mutation in objects is to create a copy of the object.

In this example, we use the destructuring operator to copy the values of myObj into myObj2.
Note that we can also use Object.assign to perform the same task.

We see here that when we change the `a` attribute of myObj2, it doesn't affect the original object.
-->


---
---
#### Problem with shallow cloning of object

<v-click>

```js
const myObj = {
    a: 1,
    b: 2,
    c: {
        e: 10,
        f: 50
    }
};

const myObj2 = {...myObj }

myObj2.c.e = 60 

console.log(myObj)
console.log(myObj2)
```

</v-click>

<v-click>

```shell
{ a: 1, b: 2, c: { e: 60, f: 50 } }
{ a: 1, b: 2, c: { e: 60, f: 50 } }
```

</v-click>

<!--
We have a problem with the shallow copy of object though. Do you know which one?

When we have nested object, the shallow copy doesn't work as we'd like.

We see here that the value of `e` got mutated in myObj and myObj2. The spreading operator (as well as the Object.assign method)
perform a shallow cloning of the object. The reference of the object assigned to the c parameter 
remains the same in myObj and myObj2
-->


---
---
#### Problem with shallow cloning of object - Solution
```js
const myObj = {
    a: 1,
    b: 2,
    c: {
        e: 10,
        f: 50
    }
};

const myObj2 = JSON.parse(JSON.stringify(myObj))
// const myObj2 = structuredClone(myObj)

myObj2.c.e = 60 

console.log(myObj)
console.log(myObj2)
```

<v-click>

```shell
{ a: 1, b: 2, c: { e: 10, f: 50 } }
{ a: 1, b: 2, c: { e: 60, f: 50 } }
```

</v-click>

<!--
We performed here a deep cloning of the object with JSON parse and stringfy. The value of e
only changes in myObj2 and remains the same in myObj.

structuredClone() can also be used to replace the JSON workaround. Although, it works from node v17.
-->


---
---
### Arrays

```js
const myArr = [1, 2, 3]

myArr[0] = 5;

console.log(myArr)
```

<v-click>

```sh
[5, 2, 3]
```

</v-click>


<!--
Since arrays are object in JavaScript, the same problem can be encountered with them.

In that example, the index 0 took the value of 5, and therefore mutated the original array.
-->

---
---
#### Cloning to the rescue, again!

```js
const myArr = [1, 2, 3]

const shallowCloneArr = [...myArr];
const deepCloneArr = JSON.parse(JSON.stringify(myArr))
// const deepCloneArr = structuredClone(myArr)

```


<!--
The same solution can be used for arrays as it was for the object. Shallow clone has the same downside
if the array contains nested objects.
-->

---
---
#### Beware of some arrays' methods

```js
const myArr = [1, 2, 3]

const reversedArr = myArr.reverse();

console.log(myArr)
console.log(reversedArr)
```

<v-click>

```sh
[ 3, 2, 1 ]
[ 3, 2, 1 ]
```

</v-click>


<!--
We see here that the .reverse method, returns an array as result, but also mutates the original array.
Therefore, we'll have to clone the array in order to avoid mutation before using the .reverse method.
-->


---
---
#### Beware of some array's methods

```js
const cloneObject = (object) => {
    return JSON.parse(JSON.stringify(object));
}

const myArr = [1, 2, 3]

const reversedArr = cloneObject(myArr).reverse();

console.log(myArr)
console.log(reversedArr)
```

<v-click>

```sh
[ 1, 2, 3 ]
[ 3, 2, 1 ]
```

</v-click>


<!--
We see here the solution with a cloned array. The original array remains unmutated.
-->

---
---
### Methods mutating Arrays

- Array.prototype.pop()
- Array.prototype.push()
- Array.prototype.shift()
- Array.prototype.unshift()
- Array.prototype.reverse()
- Array.prototype.sort()
- Array.prototype.splice()


### Methods that don't mutate the original array

- Array.prototype.slice()
- Array.prototype.concat()
- Array.prototype.map()
- Array.prototype.filter()
- Array.prototype.reduce()


<!--
Cloning the array is not required all the time. In JavaScript, some methods are mutating the original
array while some others are not. Here's a list of the most common ones.
-->

---
---
## Deep dive into function composition

- Functions must have a single input
- Functions must return a single value
- Functions must execute a single task

<!--
Now that we have a better understanding of the 4 requirements of funcitonal programming, let's see how it 
can be useful for us, and how we can use it in concrete examples.

Those are the rules of the function composition. We'll now see some example of how it should work
and how we can simplify our code, so it's easier to read and better organized.
-->

---
---

```js
// Clean up a phone number
const cleanUpPhoneNumber = (phoneNumber) => {
    const trimmedPhoneNumber = phoneNumber.trim();
    const phoneNumberWithCountryCode = trimmedPhoneNumber.replace('0', '+41');
    const phoneNumberWithoutSpace = trimmedPhoneNumber.replaceAll(' ', '')
    
    return phoneNumberWithoutSpace
    
}
```

<!--
We see here that the function follows the previous rules we've set (no mutation, no shared state, pure function, etc.)

This is of course an exaggerated example, but it showcases how we can use function composition to solve such issue.

The problem with that code is that we have a lot of intermediary variables, and it makes it harder to test
since the function doesn't execute a single tasks but multiple of them.

Note that we could chain the string method and reach a simplified version of the function, but 
that wouldn't help neither with the readability nor the testing.
-->

---
---

```js {all|1, 5, 9|all}
const trimString = (str) => {
    return str.trim()
}

const replaceCountryCode = (phoneNumber) => {
    return phoneNumber.replace('0', '+41')
}

const removeSpaces = (str) => {
    return str.replaceAll(' ', '')
}
```


<v-click>

```js
const phoneNumber = ' 099 555 55 55 '
const cleanedUpNumber = removeSpaces(replaceCountryCode(trimString(phoneNumber)))
// +41995555555
```

</v-click>

<!--
We split up the previous function into multiple ones. Each function performs now a single task.

So far, we'd have to apply those three functions in reverse order one after the other on a phoneNumber.

It doesn't improve our ability to read the code (it probably makes it worse).

But at least, since we now have three separate function executing a single task, it already makes it 
easier to test. Each can be tested separately.
-->

---
---
### Pipe & Compose
```js
// Clean up a phone number
const trimString = (str) => {
    return str.trim()
}

const replaceCountryCode = (phoneNumber) => {
    return phoneNumber.replace('0', '+41')
}

const removeSpaces = (str) => {
    return str.replaceAll(' ', '')
}
```

```js
const phoneNumber = ' 099 555 55 55 '

const cleanUpPhoneNumber = pipe(
    trimString,
    replaceCountryCode,
    removeSpaces
)

const cleanedUpNumber = cleanUpPhoneNumber(phoneNumber)
// +41995555555
```

<!--
Pipe and compose are one way to apply function composition.

We'll check how to build our own pipe/compose function in the next slide, but here's already an example of 
how they can be used to solve our problem of applying multiple functions.

The idea of pipe is that it returns a function that will take the final value we need as parameter.
It passes that parameter to the first function given as an input, and pass the returned value to the next one,
and so on.

It is equivalent to the example we had before where we were "manually" chaining the functions after the others.
Just written in a more elegant way.
-->

---
---
### Pipe
```js
const pipe = (...funcs) => {
    return (x) => {
        return funcs.reduce((value, func) => {
            return func(value);
        }, x);
    }
}
```

<v-click>

```js
const cleanedUpNumber = cleanUpPhoneNumber(phoneNumber) // phoneNumber => x
```

</v-click>

<!--
Pipe and compose are one way to apply function composition.
In this example, pipe applies all the functions given as arguments to the value using the reduce method.

In our previous example, the value x corresponds to the phoneNumber.
trimString is applied first, then passes the value to replaceCountryCode, and so on.

pipe returns a function that simply applies all the arguments function to a value.
-->


---
---
### Compose
```js {3|all}
const pipe = (...funcs) => {
    return (x) => {
        return funcs.reduceRight((value, func) => {
            return func(value);
        }, x);
    }
}
```

<v-click>

```js {4-6|all}
const phoneNumber = ' 099 555 55 55 '

const cleanUpPhoneNumber = compose(
    removeSpaces,
    replaceCountryCode,
    trimString,
)

const cleanedUpNumber = cleanUpPhoneNumber(phoneNumber)
```

</v-click>

<!--
Compose has the same goal as the pipe function. The only difference is that it applies the given functions
in a reverse order.
You can see that it uses reduceRight, instead of reduce.

Therefore, when we create our cleanUpPhoneNumber function, we need to reverse the order which we pass
the arguments to compose.
-->

---
---
### JavaScript Pipe Operator

The pipe operator could become native to JavaScript!

Proposal: https://github.com/tc39/proposal-pipeline-operator (currently stage 2)

https://github.com/tc39/proposals

```js
Object.keys(envars)
    .map(envar => `${envar}=${envars[envar]}`)
    .join(' ')
    | > `$ ${%}`
    | > chalk.dim(%, 'node', args.join(' '))
    | > console.log(%);

// Instead of
const envarString = Object.keys(envars)
    .map(envar => `${envar}=${envars[envar]}`)
    .join(' ');
const consoleText = `$ ${envarString}`;
const coloredConsoleText = chalk.dim(consoleText, 'node', args.join(' '));
console.log(coloredConsoleText);
```

<!--
Same principle as the pipe function, but expressed with a special operator. `| >`

The returned value could be specified by the % (percentage)

Example taken from the proposal page.
-->

---
---
### Advantages of Pipe & Compose

- Improve readability
- Allow to test each parameter functions individually
- Improve re-usability. Each functions passed as arguments can easily be re-used elsewhere in the code.

---
---

## Currying

Function currying can solve the problem of the functions accepting more than one parameter

```js
const mutliplyNumberBy = (multiplier, num) => {
    return multiplier * num
}
```

We can't use that function with pipe or compose, due to the number of arguments required.

Arity of 2


<!--
Arity defines the number of arguments taken by a function. Currying function will help us get the number
of arguments required by a function to one, so we can easily use it in function composition.
-->


---
---

## Currying

Curried function - Basic example:

```js
const mutliplyNumberBy = (multiplier, num) => {
    return multiplier * num
}

const curriedMultiplyBy = (multiplier) => (num) => mutliplyNumberBy(multiplier, num);
```

Now the function `curriedMultiplyBy` only accepts one argument and satisfy our rules for the function composition.

<!--
The curriedMultiplyBy function returns itself a function that takes a single parameters. 
The returned function can be used in function composition.
-->

---
---
```js
const mutliplyNumberBy = (multiplier, num) => {
    return multiplier * num
}

const curriedMultiplyNumberBy = (multiplier) => (num) => mutliplyNumberBy(multiplier, num);

pipe(
    curriedMultiplyNumberBy(2) // multiply by 2, returns a function that accepts only 1 argument
    // ...
)
```

<!--
Here we see an example usage
-->

---
---

Currying in our previous example:

```js {all|5-12|14|22|all}
const trimString = (str) => {
    return str.trim()
}

/*
const replaceCountryCode = (phoneNumber) => {
    return phoneNumber.replace('0', '+41')
}
*/
const replaceCountryCode = (countryCode, phoneNumber) => {
    return phoneNumber.replace('0', countryCode)
}

const curriedReplaceCountryCode = (countryCode) => (phoneNumber) => replaceCountryCode(countryCode, phoneNumber);

const removeSpaces = (str) => {
    return str.replaceAll(' ', '')
}

const cleanUpPhoneNumber = pipe(
    trimString,
    curriedReplaceCountryCode('+41'),
    removeSpaces
)
```

<!--
This is the functions we had before to clean up a phone number. replaceCountryCode got modified, so 
it'd accept a country code as parameter as well.

Now that it has two parameters and is therefore not compatible with the function composition.

We curry replaceCountryCode, so it allows us to pass the countryCode as a single argument, and it will then
return a function that takes the phoneNumber only.

We can then see its usage in the pipe function
-->

---
---

### "Automating" currying

```js
const replaceCountryCode = (countryCode, phoneNumber) => {
    return phoneNumber.replace('0', countryCode)
}

const curriedReplaceCountryCode = curry(replaceCountryCode)

const replaceWithSwissCountryCode = curriedReplaceCountryCode('+41')
const replaceWithGermanCountryCode = curriedReplaceCountryCode('+49')
```

<!--
Manually currying functions can be tedious.

Especially when the functions take a large number of parameters.

Therefore, it exists a way to automatically curry a function (with the help of... a function).
-->

---
---

### Curry Function

```js {all|1|2,12|3|4|5-7|4|8-10|all}
function curry(fn, arity = fn.length) {
    return (function nextCurried(prevArgs){
        return function curried(nextArg){
            var args = [ ...prevArgs, nextArg ];
            if (args.length >= arity) {
                return fn( ...args );
            }
            else {
                return nextCurried( args );
            }
        };
    })([]);
}
```

<!--
This is an example of the curry function we can use to automatically curry our functions.

Arity => number of arguments accepted by the function we curry.
It executes the nextCurried right away with an empty array, it returns itself the curriedFunction
It combines the arguments that we already passed to the curry function (the ones in prevArgs)
with the arguments we're currently passing to it (nextArgs)

If the number of arguments is equal to the arity. We simply execute the original function
and pass it all the arguments we have.

If the number of arguments is lower than the arity, we "save" them in the prevArgs from the
nextCurried function.
Next time we'll invoke the function with new arguments, we'll remember those and combine them
with the new ones.

(usage of closure)
-->


---
---

```js {all|1|3-6|8-11|13-16|18-21|all}
const exampleFunc = (x, y, z) => x + y + z;

const curriedExampleFunc = curry(exampleFunc)
// arity = 3
// args.length = 0 (no args passed)
// Returns `curried` with prevArgs = []

const partialExampleFunc1 = curriedExampleFunc(10)
// arity = 3
// args = [10] => args.length = 1 (nextArgs = 10)
// Returns `curried` with prevArgs = [10]

const partialExampleFunc2 = partialExampleFunc1(15)
// arity = 3
// args = [10, 15] => args.length = 2 (prevArgs = [10], nextArgs = 10)
// Returns `curried` with prevArgs = [10, 15]

const result = partialExampleFunc2(40)
// arity = 3
// args = [10, 15, 40] => args.length = 3 (prevArgs = [10, 15], nextArgs = 40)
// exampleFunc invoked with args => 65

console.log(result)
```

```shell
65
```

<!--
To better understand the curry function we saw before, let's take a concret example.

curriedExampleFunc

partialExampleFunc1

partialExampleFunc2

result => the number of arguments passed to the function is equal to the number of parameters that the first
function accepts. Therefore, we execute the original function with all the arguments we passed to it so far.

One important requirement of a curried function is that the last arguments has to be the ones we want to 
execute the function on. For instance, if we want to modify an array, depending on other inputs, we'll have
to set the array as the last parameter of the function. Otherwise, currying is not possible in that case.
-->

---
---

## Functional programming in your application

1. Do it yourself
2. Lodash (fp module)
3. Ramda

<v-click>

```js
const greet = R.replace('{name}', R.__, 'Hello, {name}!');
greet('Alice'); //=> 'Hello, Alice!'
```

</v-click>

<!--
There can be multiple ways of implementing functional programming in your application.
The first one is to implement it yourself. You'd have to code the curry, pipe, compose
clone, functions on your own and use it.

The other way, maybe a bit more convenient, is to use an existing functional programming library
available. Two of the most popular example are lodash (with its fp module), and ramda.

For lodash fp module, you can use the standard lodash documentation, with the exception that
the first argument it shows in the standard documentation is instead the last one of the fp
module. Be aware that if you don't use the FP module, you won't get the advantages that come
with it and you'll get "simply" a utility library instead.

The advantage of using a library is that it does a lot of the work for you already.
The functions are for instance, auto curried in ramda and lodash. It also ensures immutability.

Ramda also offers other functionalities such as placeholders that will allow you to curry functions
where the last parameters can be fulfilled before the rest. 
-->

---
---

## Conclusion

- Pure Functions => Use only the function inputs to compute a value
- Avoid Side Effects => Avoid using global values
- Avoid Shared States => Avoid using global values
- Avoid Mutations => Be careful when using objects/arrays. 
- Function composition => Pipe, Compose, Curry

<!--

-->

---
---

## Conclusion

### When to use Functional programming

- Complicated data flow
  - Breaks down complicated operations into simpler ones. Forces you to simplify the way you think about a problem
  - Data Transformation/Mapping (particularly useful in BFFs)
- Improve tests coverage


---
---

## Sources

- [Functional Programming in JavaScript: A Practical Approach | Udemy](https://www.udemy.com/course/functional-programming-in-javascript-a-practical-guide/)
- [Immutable Array Methods: How to Write Remarkably Clean JavaScript Code](https://www.sitepoint.com/immutable-array-methods-javascript/)

# Section 3: Functions & Callbacks

## Generalized Functions

Create a function 10 squared
- Takes no input
- Returns 10*10

```
function tenSquared() {
 return 10*10;
}
tenSquared() // 100
```

With this approach, every time we want a new number to square, we need to create a new function that does essentially the same thing, less the number/data difference. Logically, it is the same functionality. This breaks the fundamental DRY principal in programming. We don't want to rewrite code when we don't have to. It gets much, much harder to track and maintain what we're doing if we do so.

Instead, we can abstract out the data into a Parameter (placeholder) and generalize the function, making it reusable.

```
function squareNum(num){
 return num*num;
}
squareNum(10); // 100
squareNum(9); // 81
squareNum(8); // 64
```

With this, we don’t need to decide what data to run our functionality on until we run the function. Then we provide an actual value (‘argument’) when we run the function.

What if it weren't just the data that we could leave TBD in our function? What if we could also leave a little bit of our code TBD? This is what Higher Order Functions will allow us to do. Higher order functions follow this same principle. We may not want to decide exactly what some of our functionality is until we run our function.

## Repeating Functionality

Now suppose we have a function `copyArrayAndMultiplyBy2`, written as follows:

```
function copyArrayAndMultiplyBy2(array) {
 const output = [];
 for (let i = 0; i < array.length; i++) {
 output.push(array[i] * 2);
 }
 return output;
 }
const myArray = [1,2,3];
const result = copyArrayAndMultiplyBy2(myArray);
```

Line-by-line, it will execute in the following way:

1. Define the function with the label `copyArrayAndMultiplyBy2` and save it to memory.
1. Define the const `myArray` and assign it the value of `[1,2,3]`.
1. Define the const `result` and assign it the return value of `copyArrayAndMultiplyBy2(myArray)`.
1. Create a new execution context for `copyArrayAndMultiplyBy2` with the argument of `myArray` and add the function to the call stack.
1. Enter the new execution context and assign our `array` parameter with the argument value of `[1,2,3]`.
1. Create a new constant `output` and assign it an empty array.
1. Enter the `for` loop inside of `copyArrayAndMultiplyBy2`.
	1. Define the incrementer variable (`let`) named `i` and set it to `0`.
	1. Set the condition to continue the loop as `i < array.length`.
	1. Increment the `i` variable as long as condition is met.
	1. Inside the body of the loop, push the value of the current position in the array, multiplied by 2, to the `output` array.
	1. After condition no longer evaluates to `true` (after 3x, in this case), exit the loop and `return` the value `output` (`[2,4,6]`) out into the global context/memory, stored as `result`.


## Higher Order Functions

To demonstrate the problem with non-DRY code, we then look at the following function:

```
function copyArrayAndDivideBy2(array) {
 const output = [];
 for (let i = 0; i < array.length; i++) {
 output.push(array[i] / 2);
 }
 return output;
 }
const myArray = [1,2,3];
const result = copyArrayAndDivideBy2(myArray);
```

As you can see, this is exactly the same as the previous function - the only difference being that we're dividing instead of multiplying.

Suppose we wanted to add yet another function that adds 3 instead of multiplying or dividing by two? Yup, we need to write another separate function.

```
function copyArrayAndAdd3(array) {
 const output = [];
 for (let i = 0; i < array.length; i++) {
 output.push(array[i] + 3);
 }
 return output;
 }
const myArray = [1,2,3];
const result = copyArrayAndAdd3(myArray);
```

To clean this up a bit, we could generalize our function so we pass in our specific
instruction only when we run copyArrayAndManipulate. We can leave a TBD where the bit that changes is and then fill it in when the function is exectuted. We do this by passin in that functionality as an argument to a parameter. The way we wrap up this funcitonality to be passed in is via another function - this is known as a `callback`.

```
function copyArrayAndManipulate(array, instructions) {
 const output = [];
 for (let i = 0; i < array.length; i++) {
 output.push(instructions(array[i]));
 }
 return output;
}
function multiplyBy2(input) { return input * 2; }
const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2);
```

Above, the `instructions` parameter is where we pass in our function to be run when the outer function (`copyArrayAndManipulate`) is executed. THe `instructions` parameter will be filled in with `multiplyBy2` when it is called during th efor loop. This is then run on each iteration with the input/argument value of the current array index. Inside the function that is passed in, that index value is multiplied by two and then pushed to the `output` array.

The math/logic is exactly the same as the original function, it is just happeining in a slightly different spot - the array and looping is abstracted out into the Higher Order Function and the mathematical operationns are passed in as the argument/callback function.

## Higher Order Functions Example

Lets look at a line-by-line execution of the above HOC example:

1. Define the function with the label `copyArrayAndManipulate` and save it to Global memory.
1. Define the function with the label `multiplyBy2` and save it to Global memory.
1. Define the const `result` and assign it the return value of `copyArrayAndManipulate([1,2,3], multiplyBy2)`.
1. Create a new execution context for `copyArrayAndManipulate` with the arguments of `[1,2,3]` and `multiplyBy2`. Add the function `copyArrayAndManipulate` to the call stack.
1. Enter the new execution context and assign our `array` parameter with the argument value of `[1,2,3]` and our `inctructions` parameter with the argument value of the `multiplyBy2` function (the entire function definition is being grabbed and inserted in). Note that within this execution context, we need to use the `instructions` label to run the code that is Globally labeled as the `multiplyBy2` function.
1. Create a new constant `output` and assign it an empty array (`[]`).
1. Enter the `for` loop inside of `copyArrayAndManipulate`.
	1. Define the incrementer variable (`let`) named `i` and set it to `0`.
	1. Set the condition to continue the loop as `i < array.length`.
	1. Increment the `i` variable as long as condition is met.
	1. Inside the body of the loop, push to the `output` array, the return value of `instructions` (AKA `multiplyBy2`) with the value of the current position in the array passed in as an argument.
		1. Create a new Execution Context for `instructions` (AKA `multiplyBy2`) with the argument of the value of the current position in the array currently being looped over (`array[i]`) as the `input` parameter.
		1. Add `instructions` (AKA `multiplyBy2`) to the Call Stack.
		1. Enter the execution context of `instructions` (AKA `multiplyBy2`)
		1. Multiply the value of the current position of the array by `2` and return that evaluated number.
		1. Remove the current function from the Call Stack.
		1. Repeat steps 1-5 for three iterations (in this case) - until contition in the loop no longer evaluates to `true` - and move on out of the loop and to the next step.
	1. After condition no longer evaluates to `true` (after 3x, in this case), exit the loop and `return` the value `output` (`[2,4,6]`) out into the global context/memory, stored as `result`.
	1. Remove `copyArrayAndManipulate` from the Call Stack.

## Higher Order Functions Q&A

### Data References and Side Effects

When any function, object, or array is defined and saved to memory, if it is used anywhere else (passed in as an argument, for example) that place where it is used is actually referencing the original saved data. It is not copied in as a separate piece of data. 

That is why we don't want to make changes to and manipulate the data we pass in because that would be changing the global data, making it hard to predict what's then gonna happen with that function.


This is what is referred to as a Side Effect.

Instead, it is better to create a brand new Array (or Object or Function) and make our adjustments to that new Array. This way, our input Array stays unmutated / unchanged.

### Loops and The Execution Context

For loops do not get their own Execution Context. When you use `let` they do get a protected namespace, however. But the loop is not added to the Call Stack and a new Execution Context is not created when we enter into a loop.

## Callbacks and Higher Order Functions

**How was this possible?**

Functions in JavaScript = first class Objects. This means that they have all of the features that Objects do and can co-exist with and can be treated like any other JavaScript Object:

1. Assigned to variables and properties of other Objects
2. Passed as arguments into functions
3. Returned as values from functions

Looking, again, at the code from above - which is our Higher Order Function and which is our Callback Function?

```
function copyArrayAndManipulate(array, instructions) {
	const output = [];
	for (let i = 0; i < array.length; i++) {
		output.push(instructions(array[i]));
	}
	return output;
}
function multiplyBy2(input) {return input * 2;}
const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2);
```

The outer function that takes in a function is our Higher-Order Function and the function we insert is our Callback Function.

Higher-Order Functions take in a function or pass out a function. Just a term to describe these functions - any function that does it we call that - but there's nothing different about them inherently.

Callbacks and Higher Order Functions simplify our code and keep it DRY. The special thing about them is the idea that we can edit a function after we've saved it, because we left a little bit of it blank. And not just edit it with what data we're gonna have in there, but literally edit its code. That makes our code saved profoundly more reusable.

Declarative readable code: Map, filter, reduce - the most readable way to write code to work with data. Note that the above example is an example of a Mapping function - which means take some data through some mapping functionality, some way of changing that data. Create new collection of data with each of those elements change according some function, in this case, multiply each of them by 2.

Asynchronous JavaScript: Callbacks are a core aspect of async JavaScript, and are under-the-hood of promises, async/await.

## Arrow Functions

Arrow Functions are a shorthand way to save functions.

```
function multiplyBy2(input) { return input * 2; } // Traditional function definition
const multiplyBy2 = (input) => { return input*2 } // Arrow fn with explicit return
const multiplyBy2 = (input) => input*2 // Arrow fn with implicit return (no brackets needed) - under the hood, the `return` key word is inserted for you
const multiplyBy2 = input => input*2 // No parentheses needed with only one param

const output = multiplyBy2(3) //6
```

For the sake of example, we can now refactor our HOC from the previous lesson to use an arrow function:

```
function copyArrayAndManipulate(array, instructions) {
	const output = [];
	for (let i = 0; i < array.length; i++) {
		output.push(instructions(array[i]));
	}
	return output;
}
const multiplyBy2 = input => input*2
const result = copyArrayAndManipulate([1, 2, 3], multiplyBy2);
```

Nothing has changed here, for all intents and purposes. We can even take it a step further and skip the named `multiplyBy2` assignment altogether and use an anonymous arrow function:

```
function copyArrayAndManipulate(array, instructions) {
	const output = [];
	for (let i = 0; i < array.length; i++) {
		output.push(instructions(array[i]));
	}
	return output;
}
const result = copyArrayAndManipulate([1, 2, 3], input => input*2);
```

Here we are just inserting the full function definiton that we previously named `multiplyBy2` - it is not being executed, it is just being inserted in the same way that it was when we were using the `multiplyBy2` label previously. Nothing has changed.

Understanding how anonymous and arrow functions work under-the-hood is vital to avoid confusion.


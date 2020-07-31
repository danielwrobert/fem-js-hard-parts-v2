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
	1. Inside the body of the loop, push the value of the current position in the array, multiplied by 2.
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

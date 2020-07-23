# JavaScript Principles

## Example

**code**

```
const num = 3;
function multiplyBy2 (inputNumber){
	const result = inputNumber*2;
	return result;
}
const output = multiplyBy2(num);
const newOutput = multiplyBy2(10);
```

**execution/memory**

1. define constant `num` and assign it the value of `3`.
2. define function `multiplyBy2()`. When we define a function, there is two parts:
	a. define the label/identifier
	b. store all of the code in the function into memory (we do not execute anything at this point)
3. define constant `output` and assign it the value returned by `multiplyBy2(num)`.
4. execute `multiplyBy2(num)`, creating a new Execution Context for that function block. Inside that new Execution Context, we:
	a. assign `inputNumber` the value of `3`. (`inputNumber` is the placeholder and is referred to as a "Parameter". `3` is the data that gets passed in to that placeholder and is called an "Argument". These two things are fundamentally different things. One is a label and one is the thing that is stored in that label.)
	b. define constant `result` and assign it the value of the operaton on the right hand side - which is executed and evaluates to `6`.
	c. in our local memory, look up (or locate) the data stored with the label `result` and return it (ship it out of the functions Local Execution Context into the Global Execution Context). This function execution ultimately evaluates to the value returned in `result` - in this case, `6`.
5. assign the returned value of `6` to `output`.
6. define constant `newOutput` and assign it the value returned by `multiplyBy2(10)`.
7. repeat step 6 for the new function call. execute `multiplyBy2(10)`, creating a new Execution Context for that function block. Inside that new Execution Context, we:
	a. assign `inputNumber` the value of `10`.
	b. define constant `result` and assign it the value of the operaton on the right hand side - which is executed and evaluates to `20`.
	c. in our local memory, look up (or locate) the data stored with the label `result` and return it (ship it out of the functions Local Execution Context into the Global Execution Context). This function execution ultimately evaluates to the value returned in `result` - in this case, `20`.
8. assign the returned value of `20` to `newOutput`.

## Thread of Execution

When JavaScript code runs, it:

- Goes through the code line-by-line and runs/ ’executes’ each line - known as the thread of execution.
- Saves ‘data’ like strings and arrays so we can use that data later - in its memory. We can even save code (‘functions’).

## Functions

Functions are code we save (‘define’) to be used (call/invoke/execute/run) later with the function’s name & `()`.

A function being run is like a mini program. Therefore, we need the following two things:

- Thread of execution (the ability to go through the code line-by-line and run it)
- Memory (a little store of data to store anything that shows up while we're inside that function only)

These two things are referred to as the Execution Context. The main program has it's own (Global) Execution Context while each function also has it's own (mini/local) Execution Context. The memory inside of a function's execution context can be thought of as it's "Local Memory". It's only available while running the function code and not anywhere outside that block.

In JavaScript, we only have one thread of execution. Therefore, we can only execute one thing at a time. As soon as we want to execute a function, we have to weave into that function's execution context and then weave out into the global execution context, when completed. We can't continue on down the page while we're running that function, simultaneously.

You can see the reptitiveness in the above example of the Thread of Execution - but this is the nature of a function - you save them once and can use them again and again.

## Call Stack

- JavaScript keeps track of what function is currently running (where the thread of execution currently is)
- Run a function - add to call stack
- Finish running the function - JS removes it from call stack
- Whatever is top of the call stack
- that’s the function we’re currently running

The call stack is a traditional way of storing information on our computer. We have a number of ways of doing this - we have arrays, we have objects, and we also have what's called "stacks".

The one key single rule with a stack is we're only engaged with a very, very top thing on it, that's the only thing we're interacting with.

So, when you run/execute a function in JS, you add it to the stack and then become busy inside that function until you return out of that function. Once you return, that function pops off of the call stacn and then the next item in the stack moves up to be executed.

Whatever is at the top of the Call Stack, that is where we are right now in our thread of execution.

Always, at the bottom of our call stack, is our Global Execution Context. When we add to the call stack, we run the thing on the top and then always return to `global()` (the global execution context).

If you were to run another function nested inside of a currently running function (for example if another function was called inside of `multiplyByTwo`), that function would then be placed at the top of the Call Stack when it was invoked.

If you had another one nested inside another level deeper, it would also be added on top of the stack. Then, when completed, the thread of execution returns back out to the parent execution context (which would be the next item on the call stack). Essentailly, it will go back to wherever it was when it started running that function - in other words, it gets taken off the stack and the thread is back where it was before (eventually making it's way back out to Global). That's the key premise of a stack.

Note that one a function is run, everything is deleted / removed from memory afterwards, except for what is returned out.

## Three Core Components of JS

1. Memory to store data (including other functions/programs) as we go through the code line-by-line.
2. The Thread of Execution to go through the code line-by-line (weaving in and out of the execution contexts).
3. The Call Stack to keep track of the functions we are running and where our thread of execution is.
# Section 4: Closure

## Closure Introduction

Closure is the most esoteric of JavaScript concepts. If you understand it well, you can do some really powerful things.

It enables powerful pro-level functions like ‘once’ and ‘memoize’. Many JavaScript design patterns including the module pattern use closure. Building iterators, handling partial application and maintaining state in an asynchronous world all depend on Closure.

### Functions with memories

When our functions get called, we create a live store of data (local memory/variable environment/state) for that function’s execution context. When the function finishes executing, its local memory is deleted (except the returned value).

Side note - whenever you hear about the term `state` (application state), it's one of those really bizarre terms, that just means, the live data at that particular moment, given moment in my application, that's being stored.

But what if our functions could hold on to live data between executions?

This would let our function definitions have an associated cache/persistent memory. But it all starts with us **returning a function from another function**.

## Returning Functions

In JavaScript, we can return functions from within another function. This is a really big part of understanding Closure (and JS). Once we really get this notion down, everything else follows easily. 

Let's walk through (line-by-line) an example:

```
function createFunction() {
	function multiplyBy2 (num) {
		return num*2;
	}
	return multiplyBy2;
}
const generatedFunc = createFunction();
const result = generatedFunc(3); // 6
```

1. Define the function with the label `createFunction` and save it to Global memory.
1. Define the constant with the label `generatedFunc` and save it to Global memory.
1. Define the constant with the label `generatedFunc`, save it to Global memory, and assign it the return value of `createFunction()`.
1. Create a new execution context for `createFunction` and add the function to the call stack.
1. Enter the new execution context for `createFunction`, define a new function with the label `multiplyBy2`, and save that new function to Local Memory.
1 Return the entire function with the label `multiplyBy2` out to the Global Execution Context and assign back to the `generatedFunc` label. **Important Note**: Here we are returning out the _value_ of what is stored in Local Memory with the label/identifyier `multiplyBy2`. A function definition is actually a value - a thing that can be stored. We're not returning the label itself - that is just how it is being located (see diagram below). The value (the entire function definition itself) is what is being returned out (everything _after_ the `multiplyBy2` label). So `generatedFunc` is going to be a new label in the Global Execution Context for the function originally born as `multiplyBy2`.
1. Remove `createFunction` from the Call Stack (it still lives in Global Memory and can be called again but we're not using it anymore in this program).
	1. Back out in the Global Execution Context, we have a function assigned to `generatedFunc` which, for all intents and purposes, was formerly known as `multiplyBy2` in the Local Memory / EC of `createFunction`. Globally, this same funciton is only known as `generatedFunc` (this also includes the parameter `num`). From this point in our code, `generatedFunc` has no connection to `createFunction` - zero relationship at all. The code we get from `multiplyBy2` was simply what was returned out when it was run that one time. But JS is a synchronous language - it executes once and moves on.
1. Define the constant with the label `result` and assign it the return value of `generatedFunc(3)`.
1. Create a new execution context for `generatedFunc`, add the function to the call stack, and enter the new Execution Context.
1. Assign the argument value of `3` to the parameter `num`.
1. Multiply `3 * 2` (`6`) and return the result back out to the Global Execution Context and assign it to the `result` constant.

Here's a visual example of the above execution:

![Closure Example](./images/Closure-Example.jpg)

So why did we save a nicely semantically (that means kind of meaningfully named) function inside another function, only to then return it out, giving it a really bad name out and use it globally? Why didn't we just define it globally in the first place?

It turns out, when that function gets returned out, it gets the most powerful property, bonus feature, of JavaScript that we can ask for.

## Nested Function Scope

Here we look at an example of calling a function in the same function call as it was defined:

```
function outer (){
	let counter = 0;
	function incrementCounter (){
		counter ++;
	}
	incrementCounter();
}
outer();
```

Let's have a walk-through the above function line-by-line:

1. Define the function with the label `outer` and save it to Global memory.
1. Call `outer()`, create a new execution context for it, and add the function to the call stack.
1. Enter the new execution context for `outer`
1. Define a new variable with the label `counter`, set the value to `0`, and save it to Local Memory.
1. Define a new function with the label `incrementCounter`, and save that new function to Local Memory.
1. Call `incrementCounter()`, create a new execution context for it, and add the function to the call stack.
1. Increment `counter` via `counter++`.
	1. JavaScript looks for `counter` in the Local Memory / Execution Context (in `incrementCounter`) first.
	1. When it does not find `counter` there, it then looks to the next parent Execution Context (in `outer`) and finds it there in that parent function's Local Memory.

Here's a visual example of the above execution:

![Nested Function Scope Example](./images/Nested-Function-Scope.jpg)

The key takeaway is that where you define your functions determines what data it has access to when you call it.

## Retaining Function Memory

Now we take a look at calling a function outside of the function call in which it was defined. This will illustrate how function memory is treated/retained.

```
function outer (){
	let counter = 0;
	function incrementCounter () { counter ++; }
	return incrementCounter;
}
const myNewFunction = outer();
myNewFunction();
myNewFunction();
```

Let's have our usual walk-through the above function line-by-line:

1. Define the function with the label `outer` and save it to Global memory.
1. Define a new constant with the label `myNewFunction`, assign it to the return value of the invocation of `outer()` (we don't have that value just yet), and save it to Local Memory.
1. Call `outer()`, create a new execution context for it, and add the function to the call stack.
1. Enter the new execution context for `myNewFunction`.
1. Define a new variable with the label `counter`, set the value to `0`, and save it to Local Memory.
1. Define a function with the label `incrementCouter` and save it to Local Memory.
1 Return the entire function (_value_) with the label `incrementCounter` out to the Global Execution Context and assign back to the `myNewFunction` label.
1. `outer()` is then popped off of the Call Stack (pop is a fancy word for removed and push is a fancy word for added).
1. Call `myNewFunction()`, create a new execution context for it, and add the function to the call stack. At this point, `myNewFunction` label essentially contains the following code: `function() { counter++; }`.
1. Inside of the (now named) `myNewFunction` function (formly known as `incrementCounter` from inside `outer`), we run `counter++` to increment the counter value.
	1. JavaScript looks for `counter` in the Local Memory / Execution Context (in `incrementCounter`) first.
	1. When it does not find `counter` there, before it moves up to the parent/containing function's Execution Context, it looks in it's "backpack" of memory (see below) that it took with it when it was defined inside of `incrementCounter` and returned out. When a function is defined, it gets a bond to the surrounding Local Memory (“Variable Environment”) in which it has been defined. It still has access to that data and it brings it out with it into Global Memory to persist and live there. Forever and ever and ever.
	1. Now, in our Global (scoped) memory that lives in the "backpack" of the label that is globally stored as `myNewFunction`, the `counter` label is now set to the value of `1`.
1. `myNewFunction()` is then popped off of the Call Stack 
1. Call `myNewFunction()` once more, create a new execution context for it, and add the function to the call stack.
1. Inside of the subsequent call to `myNewFunction` function (formly known as `incrementCounter` from inside `outer`), we run `counter++` to increment the counter value once again.
	1. JavaScript again looks for `counter` in the Local Memory / Execution Context (in `incrementCounter`) first.
	1. When it does not find `counter` there, before it moves up to the parent/containing function's Execution Context, it looks in it's "backpack" of memory (see below) that it took with it when it was defined inside of `incrementCounter` and returned out. 
	1. Now, in our Global (scoped) memory that lives in the "backpack" of the label that is globally stored as `myNewFunction`, the `counter` label is now set to the value of `2`.
1. The secont call to `myNewFunction()` is then popped off of the Call Stack.

**The bond**

When a function is defined, it gets a bond to the surrounding Local Memory (“Variable Environment”) in which it has been defined. This is live data at this point.

**The ‘backpack’**

In our example above, we return incrementCounter’s code (function definition) out of outer into global and give it a new name - `myNewFunction`. We maintain the bond to outer’s live local memory - it gets ‘returned out’, attached on the back of `incrementCounter`’s function definition.

So outer’s local memory is now stored attached to `myNewFunction` - even though outer’s execution context is long gone. When we run `myNewFunction` in global, it will first look in its own local memory first (as we’d expect), but then in `myNewFunction`’s ‘backpack’.

**What can we call this ‘backpack’?**

There are a few names that are used to refer to this feature:

- Closed over ‘Variable Environment’ (C.O.V.E.)
- Persistent Lexical Scope Referenced Data (P.L.S.R.D.)
- ‘Backpack’
- ‘Closure’

The ‘backpack’ (or ‘closure’) of live data is attached incrementCounter (then to `myNewFunction`) through a hidden property known as `[[scope]]` (hidden properties are always surrounded by the double brackets). This hidden property links to where all of the surrounding "backpack" data is stored and it is still attached when the inner function is returned out and given it's new Global label.

When we search for some bit of data in our Local Scope that is note there, this property interceeds and we then look there before moving out to the next level up in Execution Context / Memory. This data persists - it isn't removed like an Execution Context's temporary memory - it permanently sticks around, as long as the function definition it is attached to is not overwritten. Permanent and Private memory.

The only way we can access it, however, is by running the function it is attached to (via it's backpack) and hoping that function was written in a way when it was defined that if it looks for something that does not exist in Local Memory, it will look to the `[[scope]]` property (on the backpack). We can not access this `[[scope]]` property directly.

Here's a visual example of the above execution:

![Retaining Function Memory](./images/Retaining-Function-Memory.jpg)
# Section 7: Classes & Prototypes

Starting at page 66 of [the slides](https://static.frontendmasters.com/resources/2019-09-18-javascript-hard-parts-v2/javascript-hard-parts-v2.pdf).

## Class & OOP Introduction

### Classes, Prototypes - Object Oriented JavaScript

- An enormously popular paradigm for structuring our complex code
- Prototype chain - the feature behind-the-scenes that enables emulation of OOP but is a compelling tool in itself
- Understanding the difference between __proto__ and prototype
- The new and class keywords as tools to automate our object & method creation

### Core of development (and running code)

1. Save data (e.g. in a quiz game the scores of user1 and user2)
2. Run code (functions) using that data (e.g. increase user 2’s score)

Easy! So why is development hard?

In a quiz game I need to save lots of users, but also admins, quiz questions, quiz outcomes, league tables - all have data and associated functionality.

In 100,000 lines of code
- Where is the functionality when I need it?
- How do I make sure the functionality is only used on the right data!

That's what makes coding so complex. So much data, so much functionality and it only applies to certain bits. And finding the right functionality and making sure it only applies to the right bit, we need some sort of organizing structure.

That is, I want my code to be:

1. Easy to reason about

But also:
2. Easy to add features to (new functionality)
3. Nevertheless efficient and performant

The Object-oriented paradigm aims is to let us achieve these three goals. And really all paradigms of code are sets of guidelines - best practices approaches.

## Object Dot Notation

So if I’m storing each user in my app with their respective data (let’s simplify):

user1:
- name: ‘Tim’
- score: 3

user2:
- name: ‘Stephanie’
- score: 5

And the functionality I need to have for each user (again simplifying!)
- increment functionality (there’d be a ton of functions in practice)

In fact, there'd be a ton of functionality here. Now, two things, I do not want to have to run all over my file of code or my files of code to try and hunt out that increment function. In my ideal world, wherever user1 is in my application right now being passed around into different bits of my application, I have that increment functionality right there adjacent to it.

How could I store my data and functionality together in one place?

### Objects - store functions with their associated data!

This is the principle of encapsulation - and it’s going to transform how we can ‘reason about’ our code:

```
const user1 = {
	name: "Will",
	score: 3,
	increment: function() { user1.score++; }
};

user1.increment(); //user1.score -> 4
```

Let's keep creating our objects. What alternative techniques do we have for creating objects?

### Creating user2 user dot notation

Declare an empty object and add properties with dot notation:

```
const user2 = {}; //create an empty object

//assign properties to that object
user2.name = "Tim";
user2.score = 6;
user2.increment = function() {
	user2.score++;
};
```

1. Define the constant `user1` and set it to an empty object.
1. Define the property `name` on the `user1` object and set it to the string "Tim".
1. Define the property `score` on the `user1` object and set it to the number 6.
1. Define the method `increment` on the `user1` object and set it to the function - passing in the entire function definition to that label (everything from the keyword `function` and on).

## Factory Functions

### Creating user3 using Object.create

`Object.create` is going to give us fine-grained control over our object later on. The only things that it does in terms of our object itself is return another object.

```
const user3 = Object.create(null);

user3.name = "Eva";
user3.score = 9;
user3.increment = function() {
	user3.score++;
};
```

1. Define the constant `user3` and set it to the return value of `Object.create(null)` - which is an empty object.
	- Note that, no matter what we pass in as an argument, it will always return out an empty object at the end. It may have some hidden properties depending on what we pass in - but always an empty object with no direct properties.
1. Define the property `name` on the `user3` object and set it to the string "Eva".
1. Define the property `score` on the `user3` object and set it to the number 9.
1. Define the method `increment` on the `user3` object and set it to the function - passing in the entire function definition to that label (everything from the keyword `function` and on).

Our code is getting repetitive, we're breaking our DRY principle. And suppose we have millions of users! What could we do? What do we tend to do whenever we're doing lines of code, again and again and again?

Put in a function. Save it once use again and again. And the only bits you want to change have those be passed in as inputs to specify when you run the function, what it's actually going to do.

The paradigm of Object Oriented Programming is not as popular increasingly as the Functional Programming style, but it is an amazing, really intuitive way of thinking about structuring an application. Application is data - user, scores, whatever functionality, the ability to change that user score, put it together. And look at it, is that right there.

But we're doing it multiple times, so now let's do the work of creating the object, save it once, and use it as many times as we want.

Note that this solution is going to turn out to be untenable. You can never use it in practice, but it gets us a long way there. Everything else we do it's just about making this much, much more efficient.

## Factory Functions Example
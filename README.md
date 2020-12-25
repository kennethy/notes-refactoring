# Refactoring - Fowler (WIP)

My study notes from reading [Refactoring: Improving the Design of Existing Code, Fowler](https://www.amazon.ca/Refactoring-Improving-Design-Existing-Code/dp/0134757599/ref=sr_1_1?dchild=1&keywords=martin+fowler&qid=1608616449&sr=8-1)

# Chapter 3 - Bad Smells in Code

**1. Mysterious Name**

Confusing names for functions, modules, variables, and classes. Ensure they clearly communicate what they do and how to use them.

**2. Duplicated Code**

Same code structure in more than one place. It requires updates in multiple places, and may require careful reading if there's small difference. Avoid by extracting similar structure to designated functions.

**3. Long Function**

Long function that takes in many parameters, have many temporary variables, have many loops/conditions, or does too much. Keep function short and it should have only one responsibility.

**4. Long Parameter List**

Passing too many parameters. Avoid parameter derivable from another. Avoid flags. May introduce parameter object or class that groups the parameters.

**5. Global Data**

Global data that could be modified anywhere in the codebase. Use getter/setter to control access. Try to keep them immutable.

**6. Mutable Data**

Data is updated, not realizing another part of the software expects it to be something different. Risk increases when its scope grows.

**7. Divergent Change**

When a module is often changed in different ways for different reasons. It may be a sign that the module is responsible for too many things, and may want to split the processing that it involves in.

**8. Shotgun Surgery**

Multiple small edits in different classes needed when making a change. Changes are all over the place.

**9. Feature Envy**

When a function in one module spends more time communicating with functions or data inside another module than it does within its own module.

**10. Data Clumps**

When three or four data items are used together in multiple places, either as class fields or function parameters. If deleting one of data value would make the rest meaningless, it could be a sign to make a class to group the data.

**11. Primitive Obsession**

Using primitive data types when it's more appropriate to create fundamental types appropraite to the domain space.

**12. Repeated Switches**

The same conditional switching logic pops up in different places. Use polymorphism to help alleviate this.

**13. Loops**

With the support of first-class functions, use pipeline operations (such as filter, reduce, and map) to replace loops

**14. Lazy Element**

Using elements that are expected to grow but never do so. Should just get rid of them.

**15. Speculative Generality**

Adding all sorts of hooks in advance when they will not be used.

**16. Temporary Field**

A class field that is set only in certain circumstances.

**17. Message Chains**

When a client asks one object for another object, and so on.

**18. Middle Man**

When client talks to a class that delegates most of its methods to another class.

**19. Inside Trading**

When exceptions are made to allow classes to talk to each other when they ideally should not.

**20. Large Class**

When a class does too much and have too many fields.

**21. Alternative Classes with Different Interfaces**

When interface is different for similar classes. Should keep the interface the same to support substituion.

**22. Data Class**

When data holders are being manipulated in far too much detail by other classes. 

**23. Refused Request**

When subclass inherits all the data and methods that it may not need. It's a much stronger smell when subclass is reusing behaviour but does not want to support the interface of the superclass.

**24. Comments**

Comments used as deodorant.

# Chapter 6 - A First Set of Refactorings

## Extract Function

**Motivation:** Separation between intention and implementation. Extract the "how" into a function and name the function after the "what".

```js
// from
function printOwing(invoice) {
    printBanner();
    let oustanding = calculateOutstanding();

    // print details
    console.log(`name: ${invoice.customer}`);
    console.log(`amount: ${oustanding}`);
}

// to
function printOwing(invoice) {
    printBanner();
    let oustanding = calculateOutstanding();
    printDetails(outstanding);

    function printDetails(outstanding) {
        console.log(`name: ${invoice.customer}`);
        console.log(`amount: ${oustanding}`);
    }
}
```
## Inline Function

**Motivation:** When the body is as clear as the intention, just use the implementation body directly.

```js
// from
function getRating(driver) {
    return moreThanFiveLateDeliveries(driver) ? 2 : 1;
}

function moreThanFiveLateDeliveries(driver) {
    return driver.numberOfLateDeliveries > 5;
}

// to
function getRating() {
    return (driver.numberOfLateDeliveries > 5) ? 2 : 1;
}
```

## Extract Variable

**Motivation:** Use local variables to break down long and complex expressions.

```js
// from
function calculation () {
    return order.quantity * order.itemPrice -
        Math.max(0, order.quantity - 500) * order.itemPrice * 0.05 +
        Math.min(order.quantity * order.itemPrice * 0.1, 100);
}

// to
function calculation() {
    const basePrice = order.quantity * order.itemPrice;
    const quantityDiscount = Math.max(0, order.quantity - 500) * order.itemPrice * 0.05;
    const shipping = Math.min(basePrice * 0.1, 100);
    return basePrice - quantityDiscount + shipping;
}
```

## Inline Variable

**Motivation:** When the expression is as meaningful as its variable name, just use the expression directly.

```js
// from
let basePrice = anOrder.basePrice;
return basePrice > 500;

// to
return anOrder.basePrice > 1000;
```

## Change Function Declaration

**Motivation:** Rename function name to better convey the intention.

```js
// from
function circum(radius) { ... }

// to
function circumference(radius) { ... }
```

## Encapsulate Variable

**Motivation:** Encapsulate widely accessed data to control access.

```js
// from
let defaultOwner = { firstName: 'Martin', lastName: 'Fowler' };

// to
let defaultOwnerData = { firstName: 'Martin', lastName: 'Fowler' };
export function defaultOwner() { return defaultOwnerData }
export function setDefaultOwner(arg) {  defaultOwnerData = arg; }
```

## Rename Variable

**Motivation:** Naming things well is the heart of clean programming.

```js
// from
let a = height * width;

// to
let aera = height * width;
```



# Tips

1. When you have to add a feature to a program but the code is not structured in a convenient way, first refactor the program to make it easier to add the feature, then add the feature. (page 4)
2. Before you start refactoring, make sure you have a solid suite of tests. These tests must be self-checking. (page 5)
3. Refactoring changes the programs in small steps, so if you make a mistake, it is easy to find where the bug is. (page 8)
4. Any fool can write code that a computer can understand. Good programmers write code that human can understand. (page 10)
5. When programming, follow the camping rule: Always leave the code base healthier than when you found it. (page 34)
6. The true test of how good code is how easy it is to change it. (page 43)
7. You have to refactor when you run into ugly code -- but excellent code needs plenty of refactoring too (page 52)
8. The whole purpose of refactoring is to make us program faster, producing more value with less effort (page 56)
9. When you feel the need to write a comment, first try to refactor the code so that any comment becomes superfluous. (page 84)
10. Make sure all tests are fully automatic and that they check their own results. (page 86)
11. A suite of tests is a powerful bug detector that decapitates the time it takes to find bugs. (page 86)
12. When you feel the need to write a comment, first try to refactor the code so that any comment becomes superfluous. (page 84)
13. Always make sure a test will fail when it should. (page 91)
14. Run tests frequently. Run those exercising the code you'reworking on at least few minutes; run all tests at least daily. (page 92)
15. It is better to write and run incomplete tests than not to run complete tests. (page 93)
16. Think of the boundary conditions under which things might go wrong and concentrate your tests there. (page 97)
17. Don't let the fear that testing can't catch all bugs stop you from writing tests that catch most bugs. (page 98)
18. When you get a bug report, start by writing a unit test that exposes the bug. (page 99)


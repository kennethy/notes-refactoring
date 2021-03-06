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
let area = height * width;
```

## Introduce Parameter Object

**Motivation:** Replace a data clump with a single data structure.

```js
// from
function amountInvoiced(startDate, endDate) { ... }
function amountReceived(startDate, endDate) { ... }
function amountOverdue(startDate, endDate) { ... }

// to
function amountInvoiced(aDateRange) { ... }
function amountReceived(aDateRange) { ... }
function amountOverdue(aDateRange) { ... }
```

## Combine Functions into Class

**Motivation:** Group functions that operate closely together into a class. It helps to simplify function calls by removing shared arguments.

```js
// from
function base(aReading) { ... }
function taxableCharge(aReading) { ... }
function calculateBaseCharge(aReading) { ... }

// to
class Reading {
    base() { ... }
    taxableCharge() { ... }
    calculateBaseCharge() { ... }
}
```

## Combine Functions into Transform

**Motivation:** Reduce duplication and keep derivation logic in a consistent place by using a data transformation function. It takes the source data as input and calculates all the derivations, putting each derived value as a field in the output data. Consider [Combining Functions into Class](#combine-functions-into-class) if fields from data source may be be updated. Derived fields may gone stale when data source is updated.

```js
// from
function base(aReading) { ... }
function taxableCharge(aReading) { ... }

// to
function enrichReading(argReading) {
    const aReading = _.cloneDeep(argReading);
    aReading.baseCharge = base(aReading);
    aReading.taxableCharge = taxableCharge(aReading);

}
```

## Split Phase

**Motivation:** Split code that's dealing with different things. Each topic can be updated separately without knowing the details of each other.

```js
// from
const orderData = orderString.split(/\s+/);
const productPrice = priceList[orderDta[0].split('-')[1]];
const orderPrice = parseInt(orderData[1]) * productPrice;

// to
const orderRecord = parseOrder(order);
const orderPrice = price(orderRecord, priceList);

function parseOrder(aString) {
    const values = aString.split(/\s+/);
    return ({
        productID: values[0].split('-')[1],
        quantity: parseInt(values[1])
    });
}

function price(order, priceList) {
    return order.quantity * priceList[order.productID];
}
```

# Chapter 7 - Encapsulation

```
Perhaps the most important criteria to be used in decomposing modules is to identify secrets that modules should hide from the rest of the system [Parnas]
```

## Encapsulate Record

**Motivation:** Group closely related fields and functions together. Derived values and stored values are separated when using a simple data record, which could be exposing too much details to the client.

```js
// from
organization = {name: 'Acme Gooseberries', country: 'GB'};

// to
class Organization {
    constructor(data) {
        this._name = data.name;
        this._country = data.country;
    }

    get name() { return this._name; }
    set name(arg) { this._name = arg; }
    get country() { return this._country; }
    set country(arg) { this._country = arg; }
}
```

## Encapsulate Collection

**Motivation:** Use modifier methods (setter/getter) to encapsulate underlying collection.

```js
// **from**
class Person {****
    get courses() { return this._courses; }
    set sources(aList) { this._courses = aList; }
}

// to
class Person {
    get courses() { return this._courses.slice(); }
    addCourse(aCourse) { ... }
    removeCourse(aCourse) { ... }
}
```

## Replace Primitive with Object

**Motivation:** Use class for data items that need special behaviours.

```js
// from
orders.filter(o => "high" === o.priority || "rush" === o.priority);

// to
orders.filter(o => o.priority.higherThan(new Priority("normal")));
```

## Replace Temp with Query

**Motivation:** Extract the calculation of a value into a function to avoid duplication.

```js
// from
const basePrice = this._quantity * this._itemPrice;
if (basePrice > 1000)
    return basePrice * 0.95;
else
    return basePrice * 0.98

// to
get basePrice() { this._quantity * this._itemPrice; }

if (this.basePrice > 1000)
    return this.basePrice * 0.95
else
    return this.basePrice * 0.98;
```

## Extract Class

**Motivation:** Split class when it has too many responsibilities and difficult to understand.

```js
// from
class Person {
    get officeAreaCode() { return this._officeAreCode; }
    get officeNumber() { retirm this._officeNumber; }
}

// to
class Person {
    get officeAreaCode() { return this._telephoneNumber.areCode; }
    get officeNumber() { return this._telephoneNumber.number; }
}

class TelephoneNumber {
    get areaCode() { return this._areaCode; }
    get number() { return this._number; }
}
```

## Inline Class

**Motivation:** Remove class when it longer has any meaningful responsibilities.

```js
// from
class Person {
    get officeAreaCode() { return this._telephoneNumber.areCode; }
    get officeNumber() { return this._telephoneNumber.number; }
}

class TelephoneNumber {
    get areaCode() { return this._areaCode; }
    get number() { return this._number; }
}

// to
class Person {
    get officeAreaCode() { return this._officeAreCode; }
    get officeNumber() { return this._officeNumber; }
}
```

## Hide Delegate

**Motivation:** Encapsulate delegate so minimum changes are needed when its interface change.

```js
// from
manager = aPerson.department.manager;

// to
manager = aPerson.manager;

class Person {
    get manager() { return this.department.manager; }
}
```

## Remove Middle Man

**Motivation:** When there's too much forwarding (via delegating methods) to the delegate, just use the delegate directly.

```js
// from
manager = aPerson.manager;

class Person {
    get manager() { return this.department.manager; }
}

// to
manager = aPerson.department.manager;
```

## Substitute Algorithm

**Motivation:** Do things in a simpler way if it can be done so.

```js
// from
function foundPerson(people) {
    for (let i = 0; i < people.length; i++>) {
        if (people[i] === 'Don') return 'Don';
        if (people[i] === 'John') return 'John';
        if (people[i] === 'Kent') return 'Kent';
    }
}

// to
function foundPerson(people) {
    const candidates = ['Don', 'John', 'Kent'];
    return people.find(p => candidates.include(p)) || '';
}
```

# Chapter 8 - Moving Features

## Move Function

**Motivation:** Ensure only related software elements are grouped together, and the links between them are easy to find and understand. Consider moving the element when it references elements in other contexts more than the one it currently resides in.

```js
// from
class Account {
    get overdraftCharge() { ... }
}

// to
class AccountType {
    get overdraftCharge() { ... }
}
```

## Move Field

**Motivation:** Move data if it makes more sense for it to be moved.

```js
// from
class Customer {
    get plan() { return this._plan; }
    get discountRate() { return this._discountRate; }
}

// to
class Customer {
    get plan() { return this._plan; }
    get discountRate() { return this.plan.discountRate; }
}
```

## Move Statements into Function

**Motivation:** Move statements into a function when they can be better understood when grouped as part of the called function.

```js
// from
result.push(`<p>title: ${person.photo.title}</p>`);
result.concat(photoData(person.photo));

function photoData(aPhoto) {
    return [
        `<p>location: ${aPhoto.location}</p>`,
        `<p>date: ${aPhoto.date.toDateString()}</p>`
    ];
}

// to
result.concat(photoData(person.photo));

function photoData(aPhoto) {
    return [
        `<p>title: ${aPhoto.title}</p>`,
        `<p>location: ${aPhoto.location}</p>`,
        `<p>date: ${aPhoto.date.toDateString()}</p>`
    ];
}
```

## Move Statements to Callers

**Motivations:** Extract statements when abstraction boundaries shift and unit of behaviour becomes a mix of two or more different things.

```js
// from
emitPhotoData(outStream, person.photo);

function emitPhotoData(outStream, photo) {
    outStream.write(`<p>title: ${photo.title}</p>\n`);
    outStream.write(`<p>location: ${photo.location}</p>\n`);
}

// to
emitPhotoData(outStream, person.photo);

outstream.write(`<p>location: ${person.photo.location}</p>\n`);

function emitPhotoData(outStream, photo) {
    outStream.write(`<p>title: ${photo.title}</p>\n`);
}
```

## Replace Inline Code with Function Call

**Motivation:** Eliminate duplications.

```js
// from
let appliesToMass = false;
for (const s of states) {
    if (s === 'MA') appliesToMass = true;
}

// to
appliesToMass = states.includes('MA');
```

## Slide Statements

**Motivation:** Code is easier to understand when things that are related to each other appear together.

```js
// from
const pricingPlan = retrievePricingPlan();
const order = retrieveOrder();
let charge;
const chargePerUnit = pricingPlan.unit;

// to
const pricingPlan = retrievePricingPlan();
const chargePerUnit = pricingPlan.unit;
const order = retrieveOrder();
let charge;
```

## Split Loop

**Motivation:** By splitting the loop, you ensure you only need to understand the behaviour you need to modify.

```js
// from
let averageAge = 0;
let totalSalary = 0;
for (const p of people) {
    averageAge += p.age;
    totalSalary += p.salary;
}

// to
let totalSalary = 0;
for (const p of people) {
    totalSalary += p.salary;
}

let averageAge = 0;
for (const p of people) {
    averageAge += p.age;
}
averageAge = averageAge / people.length;
```

## Replace Loop with Pipeline

**Motivation:** Collection pipelines can better describe the process as a series of operations. 

```js
// from
const names = [];
for (const i of input) {
    if (i.job === 'programmer') {
        names.push(i.name);
    }
}

// to
const names = input
    .filter(i => i.job === 'programmer')
    .map(i => i.name);
```

## Remove Dead Code

**Motivation**: Delete unused code. Save time from understanding code that does not matter.

```js
// from
if (false) {
    doSomethingThatUsedToMatter();
}

// to
```

# Chapter 9 - Organizing Data

## Split Variable

**Motivation:** Any variable with more than one responsibility should be replaced with multiple variables, one for each responsibility.

```js
// from
let temp = 2 * (height + width);
console.log(temp);
temp = height * width;
console.log(temp);

// to
const perimeter = 2 * (width + height);
console.log(perimeter);
const area = height * width;
console.log(area);
```

## Rename Field

**Motivation:** Fields/Setters/Getters should be named clearly to convey what they will do.

```js
// from
class Organization {
    get name() {...}
}

// to
class Organization() {
    get title() {...}
}
```

## Replace Derived Variable with Query

**Motivation:** A calculation often makes it clearer what the meaning of the data is, and it is protected from being corrupted when you fail to update the variable s the source data changes.

```js
// from
get discountedTotal() { return this._discountedTotal; }
set discount(aNumber) {
    const old = this._discount;
    this._discount = aNumber;
    this._discountedTotal += old - aNumber;
}

// to
get discountedTotal() { return this._baseTotal - this._discount; }
set discount(aNumber) { this._discount = aNumber; }
```

## Change Reference to Value

**Motivations** Value objects are generally easier to reason about because they are immutable.

```js
// from
class Product {
    applyDiscount(arg) { this._price.amount -= arg; }
}

// to
class Product {
    applyDiscount(arg) {
        this._price = new Money(this._price.amount - arg, this._price.currency);
    }
}
```

## Change Value To Reference

**Motivation:** Useful to keep a reference of the object if it is is shared and will be updated frequently.

```js
// from
let customer = new Customer(customerData);

// to
let customer = customerRepository.get(customerData.id);

```

# Chapter 10 - Simplifying Conditional Logic

### Decompose Conditional

**Motivation:** Clarify intention by decomposing complicated condition checks with function calls named after the intention.

```js
// from
if (!aDate.isBefore(plan.summerStart) && !aDate.isAfter(plan.summerEnd)) {
    charge = quantity * plan.summerRate;
} else {
    charge = quantity * plan.regularRate + plan.regularServiceCharge;
}

// to
charge = summer() ? summerCharge() : regularCharge();
```

## Consolidate Conditional Expression

**Motivation:** Combine conditional checks where each check is different yet the resulting action is the same.

```js
// from
if (anEmployee.seniority < 2) return 0;
if (anEmployee.monthsDisabled > 12) return 0;
if (anEmployee.isPartTime) return 0;

// to
if (isNotEligibleForDisability) return 0;

function isNotEligibleForDisability() {
    return (
        (anEmployee.seniority < 2) ||
        (anEmployee.monthsDisabled > 12) ||
        (anEmployee.isPartTime)
    );
}
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


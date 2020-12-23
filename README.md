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

When three or four data items are used together in multiple places, either as class fields or function parameters. If deleting one of data value would make the rest meaningful, it could be a sign to make a class to group the data.

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
12. Always make sure a test will fail when it should. (page 91)



---
title: OOPS
subtitle: Object-Oriented Programming Principles
date: 2026-09-08T12:00:00+05:30
description: An introduction to OOPS principles and how they keep systems reliable in the presence of failures.
categories:
  - LowLevel Design
tags:
  - oops
---

### Classes and Objects

#### Class
- template which defines what an object will contain (data) and what it can do(behavior)
- template used to create many ojects with similar structure and behavior but with independent state.

#### Object
- instance of a class that we can store data, interact and invoke methods on.
- each object gets its own copy of the data but shared same structure an behaviour and operate independently of every other object created from that same class.

#### Enum
- special data type that defines a fixed set of named constatnts.
- Unlike strings or integer, enums are type-safe, meaning complier enforces that you can only assign values explicitly declared in the defined set.

```java
String status = "PENDING";

// Somewhere else in the codebase...
if (status.equals("PNDING")) {  // Typo! This condition is never true
    processOrder();
}
// Enums eliminate this entire class of bugs by moving validation to compile time.
```
---
### Encapsulation
- resticting direct access to the internal details of that class, and providing controlled ways to interact with them through methods.
- Benefits : data hiding, ensures that data can only be modified in controlled, predictable ways (not any part of code change data), maintainability since others know only return type and parameters to pass.
- Achieved by using access modifiers (private, protected, public) and providing getter/setter methods.

---
### Abstraction
- achieved through interface and abstract class in Java.
- foundation for flexible software design
- programming to interface rather than implementation which decouples that caller and callee and increase runtime flexibility.

#### Interface vs Abstract Class

- While abstract classes abstract a family of related classes that share behavior , interfaces abstract a capability that unrelated classes can share.

##### Multiple Inheritence (Only on Interface)
- When a class implements multiple interfaces, an instance of that class can masquerade as any of those interface types. That is the core power of polymorphism paired with interface-based multiple
```
Person implements Driver, Dancer, Doctor

// now person can be passed in place Driver, Dancer, Doctor

Employee

// now employee cannot be passed in place Driver, Dancer, Doctor
```
#### Abstraction vs Encapsulation 
- Abstraction is the external view of an object, while Encapsulation is the internal view.

---
### Inheritance
- Inheritance enables code reuse by letting you define common logic once in a base class and then extend or specialize it in multiple derived classes.
- Why Inheritance Matters ? 
  - Code Reuse: Avoid duplicating common logic across related classes.
  - Extensibility: Add new features to existing classes without modifying them.
  - Polymorphism: Enable runtime flexibility by treating derived objects as instances of the base class.
- When to Avoid Inheritance
  - When there's no clear "is-a" relationship between classes.
  - When composition provides more flexibility and control.
  - when tight coupling risk may arrive

---
### Polymorphism
- Polymorphism allows the same method name or interface to exhibit different behaviors depending on the object that is invoking it.
- achieved through method overriding (runtime polymorphism/dynamic dispatch) and method overloading (compile-time polymorphism).
- for overriding, inheritence is prequeiste so we can override parent behavior.
---

Reference  :
- https://dsundar01.github.io/knowledgebase/?file=lowleveldesign/oops/oops.md
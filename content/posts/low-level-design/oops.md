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

### Class Relationship

### Association
- Association represents a relationship between two classes where one object users, communicates with, or references another object. (has a Relationship)
```
Student -> Teacher
- Student can exist without Teacher
- Teacher can exist without Student
```
- realtionship exists but neither class owns the other and both have independent lifecycle.

#### Types of Association
- Associations between classes can vary depending on 
    1. how objects are connected
    2. which direction information flows
#### Based on Direction
- determines which class holds a reference to the other and whether communication is one-way or bidirectional.
1. Unidirectional: Only one class has a reference to the other.other class doesn't aware who is holding reference on it.
2. Bidirectional: Both classes have references to each other and both reference must stay in sunch otherwise state becomes inconsistent.
#### Based on Multiplicity
- defines how many instances of one class can be associated with instances of another class.
- 1:1, 1:N, N:M

#### One-to-One (1:1)

```java
@Entity
public class User {
    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "profile_id", referencedColumnName = "id")
    private Profile profile;
}

@Entity
public class Profile {
    @Id
    @GeneratedValue
    private Long id;
    private String bio;
}
```

##### PostgreSQL Tables

```sql
CREATE TABLE profiles (
    id BIGSERIAL PRIMARY KEY,
    bio TEXT
);

CREATE TABLE users (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    profile_id BIGINT UNIQUE REFERENCES profiles(id)
);
```

#### One-to-Many (1:N)

```java
@Entity
public class Department {
    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL)
    private List<Employee> employees = new ArrayList<>();
}

@Entity
public class Employee {
    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
}
```

##### PostgreSQL Tables

```sql
CREATE TABLE departments (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE employees (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    department_id BIGINT REFERENCES departments(id)
);
```

#### Many-to-Many (N:M)

```java
@Entity
public class Student {
    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @ManyToMany
    @JoinTable(
        name = "student_course",
        joinColumns = @JoinColumn(name = "student_id"),
        inverseJoinColumns = @JoinColumn(name = "course_id")
    )
    private Set<Course> courses = new HashSet<>();
}

@Entity
public class Course {
    @Id
    @GeneratedValue
    private Long id;
    private String title;
}
```

##### PostgreSQL Tables

```sql
CREATE TABLE students (
    id BIGSERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL
);

CREATE TABLE courses (
    id BIGSERIAL PRIMARY KEY,
    title VARCHAR(255) NOT NULL
);

CREATE TABLE student_course (
    student_id BIGINT REFERENCES students(id),
    course_id BIGINT REFERENCES courses(id),
    PRIMARY KEY (student_id, course_id)
);
```
---

### Aggregation

| Aspect | Association | Aggregation |
|---|---|---|
| Relationship | Mutual reference / "uses-a" | Whole-part / "has-a" with loose ownership |
| Hierarchy | No clear container or contained | Clear container and contained |
| Lifecycle | Objects exist independently | Contained part can exist without the whole |

#### whole-part relationship with loose ownership.
- The whole does not create or destroy the part. part is passed in constructor/injected.
- the part can be shared among multiple wholes.
- Both the whole and the part can be created and destroyed independently.

> If a class contains other classes for logical grouping only without lifecycle ownership, it is an aggregation.

#### Example: Playlist and Song

```text
Playlist  ◇───has───*  Song
```

```java
class Playlist {
    String name;
    List<Song> songs;     // Songs are passed in from outside, not created here

    Playlist(String name, List<Song> songs) {
        this.name = name;
        this.songs = songs;
    }

    void addSong(Song s) { songs.add(s); }
    void removeSong(Song s) { songs.remove(s); }
    int getTotalDuration() { return songs.stream().mapToInt(s -> s.duration).sum(); }
}

class Song {
    String title;
    String artist;
    int duration;
}

class Main {
    public static void main(String[] args) {
        // Songs are created outside the Playlist
        Song s1 = new Song("Hey Jude", "The Beatles", 420);
        Song s2 = new Song("Imagine", "John Lennon", 183);

        List<Song> songs = new ArrayList<>();
        songs.add(s1);
        songs.add(s2);

        Playlist p = new Playlist("My Favorites", songs);  // passed in from outside
        Playlist p2 = new Playlist("My Favorites", songs);  // parts can be shared

    }
}
```
> Deleting a Playlist does not delete its Songs, and a Song can exist without a Playlist.
---

### Composition
- special type of association that signifies strong ownership between objects.
- The whole owns the part and controls its lifecycle.
- When the whole is destroyed, the parts are also destroyed.
- The parts are not shared with any other object.
- The part has no independent meaning or identity outside the whole.

#### Example: Order and OrderItem

```java
class OrderItem {
    String productName;
    int quantity;
}

class Order {
    int orderId;
    List<OrderItem> items;      // Order creates and owns its items

    void addItem(String productName, int quantity) {
        items.add(new OrderItem(productName, quantity));  // created inside Order
    }

    void cancel() {
        items.clear();  // items are destroyed with the order
    }
}

class Main {
    public static void main(String[] args) {
        Order order = new Order(101);
        order.addItem("Laptop", 1);    // items are created by the Order
        order.addItem("Mouse", 2);

        // OrderItems cannot exist without the Order
        order.cancel();
    }
}
```

> Order creates its own OrderItems. If the Order is destroyed, its OrderItems are also destroyed; they have no meaning outside the Order.

#### Composition vs Aggregation vs Association

| Aspect | Association | Aggregation | Composition |
|---|---|---|---|
| Relationship | "uses-a" / mutual awareness | "has-a" / whole-part (loose) | "contains-a" / whole-part (strong) |
| Ownership | None | Weak / shared | Strong / exclusive |
| Lifecycle | Independent | Part survives whole | Part destroyed with whole |
| Sharing | Can reference many | Part can be shared | Part belongs to one whole |
| Example | Student ↔ Teacher | Playlist ◇───* Song | Order ■───* OrderItem |

#### Decision Logic

```text
Do you have a whole-part relationship?
├── No  → Association
└── Yes → Can the part exist independently?
    ├── No  → Composition
    └── Yes → Can the part be shared?
        ├── Yes → Aggregation
        └── No  → Who creates it?
            ├── Whole creates it     → Composition
            └── External creates it  → Aggregation
```

Reference  :
- https://dsundar01.github.io/knowledgebase/?file=lowleveldesign/oops/oops.md
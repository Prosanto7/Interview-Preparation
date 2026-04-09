## 🧠 What is OOP?

**Object-Oriented Programming** is a programming paradigm based on the concept of "**objects**", which can contain **data** (fields, attributes) and **methods** (functions or procedures).

---

## 🔑 4 Pillars of OOP

| Concept       | Description                                                                 | Example (Real Life)                                  |
|---------------|-----------------------------------------------------------------------------|------------------------------------------------------|
| **Encapsulation** | Hiding internal data using access modifiers. Use getters/setters to control access. | A car hides its internal engine parts; you use a steering wheel. |
| **Abstraction**    | Hiding unnecessary details and showing only relevant features.          | Driving a car – you don't care *how* the engine works. |
| **Inheritance**    | A class (child) inherits fields/methods from another (parent).          | A `Dog` class inherits from `Animal`.                |
| **Polymorphism**   | One interface, many implementations.                                   | A `print()` method may work differently for `PDF` vs `Word`. |

---

## 📦 Core OOP Concepts in Detail

### 1. **Class and Object**
- **Class**: A blueprint for creating objects.
- **Object**: An instance of a class.

```java
class Car {
    String color;
    void drive() {
        System.out.println("Car is moving");
    }
}

Car myCar = new Car();  // Object creation
```

---

### 2. **Encapsulation**
- Protects data by making variables **private** and accessing them using **getters/setters**.

```java
class BankAccount {
    private int balance = 0;

    public void deposit(int amount) {
        if (amount > 0) balance += amount;
    }

    public int getBalance() {
        return balance;
    }
}
```

---

### 3. **Abstraction**
- Hides complex implementation using **abstract classes** or **interfaces**.

```java
abstract class Animal {
    abstract void sound();
}

class Dog extends Animal {
    void sound() {
        System.out.println("Barks");
    }
}
```

---

### 4. **Inheritance**
- Reusability: one class inherits from another using `extends`.

```java
class Animal {
    void eat() {
        System.out.println("This animal eats food.");
    }
}

class Dog extends Animal {
    void bark() {
        System.out.println("Dog barks");
    }
}
```

---

### 5. **Polymorphism**
- **Compile-time**: Method overloading (same method name, different params)
- **Run-time**: Method overriding (subclass provides specific implementation)

```java
// Overloading
class Print {
    void show(int a) { System.out.println(a); }
    void show(String b) { System.out.println(b); }
}

// Overriding
class Animal {
    void sound() { System.out.println("Some sound"); }
}

class Cat extends Animal {
    void sound() { System.out.println("Meow"); }
}
```

---

## 👨‍👩‍👦 Other OOP Concepts

| Concept                 | Description |
|-------------------------|-------------|
| **Constructor**         | Special method to initialize objects |
| **Destructor / Finalizer** | Called when object is destroyed (e.g., `__del__` in Python) |
| **Interface**           | A contract for what a class must implement |
| **Abstract Class**      | Cannot be instantiated, may contain method implementations |
| **Composition**         | Has-a relationship. (e.g., Car has an Engine) |
| **Association**         | Relationship between two classes |
| **Aggregation**         | A special form of association where child can exist independently |
| **Access Modifiers**    | `private`, `protected`, `public` — control access to members |

---

## 🏛 Real-Life Example: University System

### Entities:
- `Person` (Base class)
  - Fields: name, age
- `Student` and `Teacher` (Inherited classes)
  - `Student` has: roll number, subjects
  - `Teacher` has: salary, department

```java
class Person {
    String name;
    int age;
    void displayInfo() { ... }
}

class Student extends Person {
    int roll;
    void study() { ... }
}

class Teacher extends Person {
    double salary;
    void teach() { ... }
}
```

---

## 💡 When and Why to Use OOP?

| Reason                        | Benefit |
|-------------------------------|---------|
| **Scalability**               | Easy to manage large systems |
| **Reusability**               | Code reuse through inheritance |
| **Maintainability**           | Better modularization |
| **Security**                  | Data hiding using encapsulation |

---

## 🆚 OOP vs Procedural Programming

| Feature            | OOP                          | Procedural                   |
|--------------------|------------------------------|------------------------------|
| Focus              | Objects                      | Functions                    |
| Data               | Encapsulated                 | Global or local              |
| Reusability        | High                         | Low                          |
| Example Language   | Java, Python (OOP style)     | C, Basic                     |

---


## 🔐 What Are Access Modifiers?

Access modifiers **control the visibility** of class members (fields, methods, constructors) to other parts of your program.

They are essential for:
- Hiding internal implementation
- Securing data
- Maintaining encapsulation

---

## 👨‍🏫 Types of Access Modifiers (Common in Java, C++, PHP, C#)

| Modifier       | Accessible Within Class | Same Package | Subclass | Outside Package |
|----------------|--------------------------|--------------|----------|------------------|
| `private`      | ✅ Yes                  | ❌ No        | ❌ No    | ❌ No            |
| `default` (no modifier) | ✅ Yes          | ✅ Yes        | ❌ No    | ❌ No            |
| `protected`    | ✅ Yes                  | ✅ Yes        | ✅ Yes    | ❌ No (unless subclass) |
| `public`       | ✅ Yes                  | ✅ Yes        | ✅ Yes    | ✅ Yes            |

---

## 🔍 Detailed Explanation with Examples

### 1. **`private`** – Most Restrictive
- Access **only within the same class**
- Used for sensitive data or logic

```java
class Account {
    private double balance = 1000;

    public double getBalance() {
        return balance;
    }
}
```

🔒 Other classes **cannot access `balance` directly.**

---

### 2. **`default` (Package-Private)** – No Modifier
- Accessible **within the same package**
- Not accessible from outside the package, even if it's a subclass

```java
class User { // default class
    int id; // default field
}
```

✅ Usable by other classes in the same package.

---

### 3. **`protected`**
- Accessible **within the same package**
- Accessible by **subclasses even in different packages**

```java
class Animal {
    protected void eat() {
        System.out.println("Animal eats");
    }
}

class Dog extends Animal {
    void bark() {
        eat(); // allowed
    }
}
```

✅ Useful for **inheritance** when you want subclasses to access parent methods.

---

### 4. **`public`** – Least Restrictive
- Accessible from **anywhere**
- Ideal for APIs or shared utilities

```java
public class MathUtils {
    public static int add(int a, int b) {
        return a + b;
    }
}
```

🌍 Can be accessed from **any class or package**.

---

## 📘 In PHP

| Modifier     | Description |
|--------------|-------------|
| `public`     | Accessible from anywhere |
| `protected`  | Accessible in same class and subclass |
| `private`    | Accessible only within same class |

```php
class Person {
    public $name;
    protected $age;
    private $ssn;
}
```

---

## ⚠️ When to Use What?

| Use Case                            | Recommended Modifier |
|-------------------------------------|----------------------|
| Sensitive data (e.g., passwords)    | `private`            |
| Base method for subclasses          | `protected`          |
| API methods, utility functions      | `public`             |
| Internal helper methods (Java only) | default              |

---

## 🛡 Access Modifiers in Other Languages

| Language    | Supports | Notes |
|-------------|----------|-------|
| **Java**    | ✅ Full support (`public`, `private`, `protected`, default) |
| **C++**     | ✅ Uses same keywords, also supports `friend` |
| **Python**  | 🚫 Not enforced strictly; uses `_name` (protected) and `__name` (private by convention) |
| **C#**      | ✅ Adds `internal`, `protected internal`, `private protected` |
| **PHP**     | ✅ Supports `public`, `protected`, `private` |

---


## 🔹 `static` Keyword — *Belongs to the Class*

### ❓ What is `static`?
When a member (variable, method, block, or class) is declared as `static`, it belongs **to the class** rather than any individual object.

---

### 1. 🔸 `static` Variables

- Shared by **all instances** of the class.
- Initialized **only once**, at class loading time.
- Good for constants or counters.

```java
class Student {
    static String college = "ABC College";
    int id;

    Student(int id) {
        this.id = id;
    }
}
```

🔁 If you change `college` for one object, it reflects for **all**.

#### ✅ Use case:
- Shared properties like tax rate, school name, or global counters.

---

### 2. 🔸 `static` Methods

- Can be called **without creating an object**.
- **Cannot access non-static members** (because they belong to an object, not the class).

```java
class MathUtil {
    static int square(int x) {
        return x * x;
    }
}
```

Call with: `MathUtil.square(5);`

#### ❌ You **can't use** `this` or instance variables inside static methods.

---

### 3. 🔸 `static` Block

- Used for **static initialization**.
- Executes **only once** when the class is loaded.

```java
class Config {
    static {
        System.out.println("Static block runs first");
    }
}
```

---

### 4. 🔸 `static` Class (Nested)

- Inner classes can be `static`.
- They don't need an instance of the outer class.

```java
class Outer {
    static class Inner {
        void display() {
            System.out.println("Static nested class");
        }
    }
}
```

## 🔸 `final` Keyword — *Cannot Be Changed*

### ❓ What is `final`?
When something is declared as `final`, it means **"cannot be modified"** after assignment or declaration.

---

### 1. 🔸 `final` Variables

- Once assigned, **cannot be reassigned**.
- Must be initialized during declaration or in the constructor.

```java
class Constants {
    final double PI = 3.14159;
}
```

Trying to reassign `PI = 3.14;` will give an error.

#### ✅ Use case:
- Defining constants like PI, speed of light, config values.

---

### 2. 🔸 `final` Methods

- **Cannot be overridden** in subclasses.

```java
class Animal {
    final void sound() {
        System.out.println("Animal makes sound");
    }
}

class Dog extends Animal {
    // ❌ Cannot override sound()
}
```

#### ✅ Use case:
- You want to ensure base class functionality is **not changed** by any subclass.

---

### 3. 🔸 `final` Classes

- **Cannot be subclassed**.
- Used to make a class **immutable or secure**.

```java
final class MathUtils {
    // Can't be extended
}
```

#### ✅ Use case:
- Prevent inheritance (like `String`, `Integer` in Java are final).
- Design secure or utility classes.

---

## 🔁 Comparison Table: `static` vs `final`

| Feature              | `static`                                 | `final`                                  |
|----------------------|-------------------------------------------|-------------------------------------------|
| Ownership            | Belongs to class                          | Can't be modified once assigned           |
| Variables            | Shared by all instances                   | Acts like a constant                      |
| Methods              | Can be called without object              | Can't be overridden in subclass           |
| Classes              | Only nested classes can be static         | Class can't be inherited                  |
| Access to `this`     | ❌ No access in static context             | ✅ Yes (in normal methods)                |
| Initialization       | During class load                         | During declaration or constructor         |

---

## 🎓 Real-World Analogies

| Concept         | Real-life Analogy                       |
|-----------------|------------------------------------------|
| `static` var    | Shared printer for all employees         |
| `static` method | Utility function like a calculator       |
| `final` var     | Your National ID – once set, can't change|
| `final` method  | Safety rules – no one can modify         |
| `final` class   | A sealed box – no one can extend it      |

---

## 📌 Summary

| Keyword | On Variable         | On Method                          | On Class                        |
|---------|----------------------|-------------------------------------|----------------------------------|
| `static` | Shared across all objects | Doesn't need object to be called | Used only in inner classes       |
| `final`  | Acts as constant         | Prevents overriding              | Prevents inheritance             |

---


## 🔷 ABSTRACT CLASS

An **abstract class** is a class that cannot be instantiated on its own and is meant to be **extended**. It can have both abstract methods (without implementation) and concrete methods (with implementation).

---

### ✅ Properties and Rules of Abstract Class:

1. **Cannot be instantiated**  
   ```java
   AbstractClass obj = new AbstractClass(); // ❌ Not allowed
   ```

2. **Can contain abstract and non-abstract methods**
   ```java
   abstract class Animal {
       abstract void sound(); // Abstract method
       void breathe() {
           System.out.println("Breathing");
       }
   }
   ```

3. **Can have constructors**
   - Used for initialization when subclass is instantiated.
   ```java
   abstract class Animal {
       Animal() {
           System.out.println("Animal created");
       }
   }
   ```

4. **Can have instance variables**
   - They are inherited by subclasses.
   ```java
   abstract class Animal {
       String name;
   }
   ```

5. **Can have static methods and final methods**
   - Static: Utility within class.
   - Final: Can’t be overridden.
   ```java
   static void info() { ... }
   final void greet() { ... }
   ```

6. **Supports all access modifiers** (`private`, `protected`, `public`)
   - Unlike interfaces (before Java 9) which were limited.

7. **Can extend another class and implement interfaces**
   ```java
   abstract class Dog extends Animal implements Pet {
   }
   ```

---

## 🔶 INTERFACE

An **interface** is a contract. It contains method signatures but **no implementation** (until Java 8+). A class that implements an interface **must define all of its methods**.

---

### ✅ Properties and Rules of Interface:

1. **Cannot contain method implementations (until Java 8)**
   - All methods are implicitly `public abstract`.

   ```java
   interface Drawable {
       void draw(); // implicitly public abstract
   }
   ```

2. **Supports multiple inheritance**
   - A class can implement **multiple interfaces**, overcoming Java’s single inheritance limit.

3. **All variables are public, static, and final by default**
   ```java
   interface Constants {
       int MAX = 10; // treated as public static final
   }
   ```

4. **Methods are public by default**

5. **No constructors**
   - Interfaces are not part of the object creation chain.

6. **Java 8+: Can have `default` and `static` methods**
   ```java
   interface Printer {
       default void print() {
           System.out.println("Default Print");
       }
   }
   ```

7. **Java 9+: Can have `private` methods**
   - For code reuse inside default/static methods only.

8. **Used to achieve complete abstraction**

---

## 🧠 Real-Life Analogy

| Concept       | Analogy                                           |
|---------------|----------------------------------------------------|
| Abstract Class | A **blueprint with partial implementation**. Like a general `Vehicle` class that includes how to start the engine but leaves `drive()` abstract. |
| Interface     | A **contract**. If you sign a `Flyable` interface, you promise to define `fly()` method yourself. |

---

## 🔄 COMPARISON: Abstract Class vs Interface

| Feature                        | **Abstract Class**                          | **Interface**                                 |
|-------------------------------|---------------------------------------------|-----------------------------------------------|
| Instantiation                 | Cannot be instantiated                     | Cannot be instantiated                         |
| Method Types                  | Abstract & Non-abstract                    | Only abstract (until Java 8)                   |
| Access Modifiers              | Can have any (`private`, `protected`, etc.) | Only `public` (methods are public abstract)    |
| Constructors                  | Yes                                         | No                                             |
| Variables                     | Instance and static                        | Only `public static final`                     |
| Multiple Inheritance          | Not supported                              | Supported (`implements Interface1, Interface2`)|
| Use case                      | Partial implementation with common logic    | Full abstraction or capability specification   |
| Default Implementation        | Yes                                         | From Java 8 (using `default` methods)          |
| Performance                   | Slightly better due to fewer indirections  | Interface calls are typically slower (pre-JDK 8)|
| Inheritance                   | `extends`                                  | `implements`                                   |

---

## 📌 When to Use What?

| Situation | Prefer |
|----------|--------|
| You want to provide **default behavior** or partial logic | **Abstract Class** |
| You want to define a **pure contract** with no behavior | **Interface** |
| You need to **implement multiple capabilities** | **Interface** |
| You need a **base class** with common fields/methods | **Abstract Class** |

---

## 🧪 Example:

```java
// Interface
interface Movable {
    void move();
}

// Abstract Class
abstract class Vehicle {
    void start() {
        System.out.println("Vehicle started");
    }
    abstract void fuelType();
}

// Concrete Class
class Car extends Vehicle implements Movable {
    public void move() {
        System.out.println("Car is moving");
    }

    void fuelType() {
        System.out.println("Petrol or Diesel");
    }
}
```

## 🧱 SOLID Principles Overview

| Letter | Principle Name                 | Purpose                                  |
|--------|--------------------------------|------------------------------------------|
| S      | Single Responsibility Principle | One class = one job                      |
| O      | Open/Closed Principle           | Open for extension, closed for modification |
| L      | Liskov Substitution Principle   | Subtypes must be substitutable for their base types |
| I      | Interface Segregation Principle | No client should depend on methods it doesn’t use |
| D      | Dependency Inversion Principle  | Depend on abstractions, not concretions |

---

## 1️⃣ **Single Responsibility Principle (SRP)**

> “A class should have only one reason to change.”

### ✅ Explanation:
A class should do one thing and do it well. If it has more than one responsibility, those responsibilities become coupled.

### 👨‍🏫 Real-life example:
Think of a **school teacher**. Their job is to teach. If they also have to do **admin work**, any changes to admin policies might force changes in teaching responsibilities.

### 🧑‍💻 Code Example:

```java
// BAD: Doing multiple jobs
class ReportManager {
    void generateReport() { ... }
    void printReport() { ... }
    void saveToDatabase() { ... } // 💥 Database logic doesn't belong here
}

// GOOD: Split responsibilities
class ReportGenerator {
    void generateReport() { ... }
}

class ReportPrinter {
    void printReport() { ... }
}

class ReportSaver {
    void saveToDatabase() { ... }
}
```

---

## 2️⃣ **Open/Closed Principle (OCP)**

> “Software entities should be open for extension, but closed for modification.”

### ✅ Explanation:
You should be able to add new functionality **without changing existing code**.

### 👨‍🏫 Real-life example:
Think of a **phone charger**. You can design it to accept multiple phone types (via adapters) without redesigning the charger every time a new phone model comes.

### 🧑‍💻 Code Example:

```java
// BAD: You change existing code to add new shape
class AreaCalculator {
    double calculate(Object shape) {
        if (shape instanceof Circle) { ... }
        else if (shape instanceof Rectangle) { ... }
    }
}

// GOOD: Use polymorphism
interface Shape {
    double area();
}

class Circle implements Shape {
    double radius;
    public double area() { return Math.PI * radius * radius; }
}

class Rectangle implements Shape {
    double length, width;
    public double area() { return length * width; }
}

class AreaCalculator {
    double calculate(Shape shape) {
        return shape.area();
    }
}
```

---

## 3️⃣ **Liskov Substitution Principle (LSP)**

> “Objects of a superclass should be replaceable with objects of its subclasses without breaking the application.”

### ✅ Explanation:
Subclasses should behave like their parent class, without changing the expected behavior.

### 👨‍🏫 Real-life example:
A **Bird** can fly. But if we create a `Penguin` class that **inherits** from `Bird`, and Penguin **can’t fly**, it violates the contract.

### 🧑‍💻 Code Example:

```java
// BAD: Penguin breaks expectation
class Bird {
    void fly() { ... }
}

class Penguin extends Bird {
    void fly() {
        throw new UnsupportedOperationException(); // 💥 Violates LSP
    }
}

// GOOD: Refactor hierarchy
interface Bird { void eat(); }

interface FlyingBird extends Bird {
    void fly();
}

class Sparrow implements FlyingBird {
    public void fly() { ... }
}

class Penguin implements Bird {
    public void eat() { ... }
}
```

---

## 4️⃣ **Interface Segregation Principle (ISP)**

> “Clients should not be forced to depend on methods they do not use.”

### ✅ Explanation:
Split large interfaces into smaller, specific ones. Don’t force classes to implement unused methods.

### 👨‍🏫 Real-life example:
A **printer** and a **scanner** may both use a device, but not all devices need to implement both functionalities.

### 🧑‍💻 Code Example:

```java
// BAD: Fat interface
interface Machine {
    void print();
    void scan();
    void fax();
}

class SimplePrinter implements Machine {
    public void print() { ... }
    public void scan() { throw new NotSupported(); } // ❌
    public void fax() { throw new NotSupported(); }  // ❌
}

// GOOD: Split interfaces
interface Printer { void print(); }
interface Scanner { void scan(); }

class SimplePrinter implements Printer {
    public void print() { ... }
}
```

---

## 5️⃣ **Dependency Inversion Principle (DIP)**

> “High-level modules should not depend on low-level modules. Both should depend on abstractions.”

### ✅ Explanation:
Use interfaces/abstractions so that high-level logic doesn't break when low-level code changes.

### 👨‍🏫 Real-life example:
A **remote control** should not care about the specific brand of TV. It just sends a signal.

### 🧑‍💻 Code Example:

```java
// BAD: Direct dependency
class MySQLDatabase {
    void save(String data) { ... }
}

class DataManager {
    MySQLDatabase db = new MySQLDatabase();
    void saveData(String data) {
        db.save(data); // tightly coupled
    }
}

// GOOD: Use abstraction
interface Database {
    void save(String data);
}

class MySQLDatabase implements Database {
    public void save(String data) { ... }
}

class DataManager {
    Database db;
    DataManager(Database db) { this.db = db; }
    void saveData(String data) {
        db.save(data); // loosely coupled
    }
}
```

---

## 🔚 Summary Table

| Principle | Description | Real-Life Analogy |
|----------|-------------|-------------------|
| **SRP** | One class = one job | A teacher should only teach, not manage accounts |
| **OCP** | Extend without modifying | A phone charger that adapts to new phones |
| **LSP** | Subclass should behave like parent | Penguins shouldn't inherit flying behavior |
| **ISP** | Split large interfaces | Don’t force a printer to scan |
| **DIP** | Depend on abstractions | A remote should work with any TV brand |

---


## 🔗 **Coupling**

### 🔍 What is Coupling?
**Coupling** refers to the **degree of dependency** between modules/classes. It measures how much one module knows about the inner workings of another module.

### 🎯 Goal:  
You want **low (loose) coupling** — modules should be independent and interact through well-defined interfaces.

---

### 🔧 Types of Coupling:

| Type               | Description                                                                 | Example |
|--------------------|-----------------------------------------------------------------------------|---------|
| **Content Coupling** | One module modifies or relies on the internal workings of another.          | Module A accesses private data of Module B. ❌ |
| **Common Coupling**  | Modules share the same global data.                                         | Multiple classes use the same global variable. ❌ |
| **External Coupling**| Two modules depend on external formats (e.g., file formats or protocols).   | Both modules depend on same API response format. ⚠️ |
| **Control Coupling** | One module controls the flow of another by passing control flags.           | Passing a flag like `isPrint = true`. ⚠️ |
| **Data Coupling**    | Modules share data through parameters.                                      | `printReport(String reportName)` ✅ |
| **Message Coupling** | Modules communicate through public interfaces (ideal form).                 | Using interfaces/observers/events ✅✅ |

---

### ✅ Best Practices to Reduce Coupling:
- Use **interfaces** and **abstraction**.
- Apply **Dependency Injection**.
- Avoid **global variables**.
- Follow the **Single Responsibility Principle (SRP)**.
- Break tightly coupled classes into reusable services.

---

## 🧱 **Cohesion**

### 🔍 What is Cohesion?
**Cohesion** is the **degree to which the elements of a module belong together**. It measures how well a module performs a single, well-defined task.

### 🎯 Goal:
You want **high cohesion** — a class should focus on one specific responsibility.

---

### 🔧 Types of Cohesion:

| Type                   | Description                                                                 | Example |
|------------------------|-----------------------------------------------------------------------------|---------|
| **Coincidental Cohesion** | Randomly grouped tasks.                                                      | `Utility` class with unrelated functions. ❌ |
| **Logical Cohesion**      | Tasks that are grouped logically, not functionally.                         | Class with methods: `printPDF()`, `printExcel()`. ⚠️ |
| **Temporal Cohesion**     | Tasks that are related by time or order of execution.                       | Initialization methods lumped together. ⚠️ |
| **Procedural Cohesion**   | Tasks are grouped because they must be done in a specific sequence.         | Order of operations in transaction. ⚠️ |
| **Communicational Cohesion** | Tasks that operate on the same data.                                          | Methods that work on a `Customer` object. ✅ |
| **Functional Cohesion**     | Every part of the module works together to perform one function (ideal). | `InvoiceService` that only processes invoices. ✅✅ |

---

### ✅ Best Practices to Increase Cohesion:
- Stick to the **Single Responsibility Principle**.
- Group related logic and data.
- Avoid "God classes" that do everything.
- Refactor classes when responsibilities grow.

---

## 🔁 Coupling vs. Cohesion — Comparison

| Feature          | Coupling                            | Cohesion                              |
|------------------|-------------------------------------|----------------------------------------|
| **Definition**     | Degree of interdependence between modules | Degree to which elements of a module belong together |
| **Goal**           | **Low** Coupling                   | **High** Cohesion                      |
| **Indicates**      | How strongly a module is connected to others | How focused a module is on a single task |
| **Good Design**    | Modules can work independently     | Each module does one thing well        |
| **Example**        | Class A tightly uses Class B’s logic | Class A handles only customer data     |
| **Result of**      | Poor separation of concerns        | Good SRP & encapsulation               |

---

## 💡 Real-Life Analogy

### Coupling:
Imagine departments in a company. If the **HR team needs to constantly talk to the IT team** for every little task, they’re **tightly coupled**.

### Cohesion:
If each department **does its own job** (HR handles hiring, IT handles systems), then they are **highly cohesive**.

---


## 🧩 What is Dependency Injection?

**Dependency Injection** is a design pattern used to **decouple the creation of objects from their usage**, making code more modular, testable, and flexible.

In simple terms:

> **Rather than a class creating its own dependencies, it is given (injected) what it needs from the outside.**

---

## 🚸 Real-Life Story Analogy

### 🛠️ Without Dependency Injection (Tightly Coupled)

Imagine you're building a **Car**. Inside the `Car` class, you write:

```java
class Car {
    Engine engine = new Engine(); // creates its own dependency
}
```

This is like saying:

> “The car builds its own engine. No external garage can provide or replace it.”

If the engine needs to change (say, from petrol to electric), you must **go into the `Car` class and modify it.** Not flexible!

---

### ✅ With Dependency Injection (Loosely Coupled)

Now let’s apply Dependency Injection:

```java
class Car {
    Engine engine;
    
    Car(Engine engine) {
        this.engine = engine; // dependency is injected
    }
}
```

This is like:

> “You give the car an engine from outside. You can choose petrol, diesel, electric — whatever you like.”

Now the car is **not responsible for building or deciding the type of engine**. It just **uses** whatever is given.

---

## 💡 Why is Dependency Injection Important?

| Benefit               | Description |
|-----------------------|-------------|
| ✅ **Loose Coupling** | Objects depend on abstractions, not concrete classes. |
| ✅ **Testability**    | You can easily inject mock dependencies in unit tests. |
| ✅ **Flexibility**    | Change the behavior without modifying the class itself. |
| ✅ **Reusability**    | Components become reusable across multiple contexts. |

---

## 💻 Technical Example (in Java)

Let’s say we have a `TextEditor` that uses `SpellChecker`.

### 🧱 Without DI:

```java
class SpellChecker {
    public void checkSpelling() {
        System.out.println("Checking spelling...");
    }
}

class TextEditor {
    private SpellChecker spellChecker;

    public TextEditor() {
        spellChecker = new SpellChecker(); // tight coupling
    }

    public void makeSpellCheck() {
        spellChecker.checkSpelling();
    }
}
```

This makes `TextEditor` tightly bound to `SpellChecker`. You can't easily replace `SpellChecker` with another version or mock it for testing.

---

### 🔌 With DI (Constructor Injection):

```java
class TextEditor {
    private SpellChecker spellChecker;

    public TextEditor(SpellChecker spellChecker) {
        this.spellChecker = spellChecker; // dependency is injected
    }

    public void makeSpellCheck() {
        spellChecker.checkSpelling();
    }
}
```

Now, the `SpellChecker` can be passed in from outside. You can inject:

- A mock spell checker for testing.
- An advanced spell checker for premium users.

---

## 🔄 Types of Dependency Injection

| Type                  | Description | Example |
|-----------------------|-------------|---------|
| **Constructor Injection** | Dependencies are passed via the constructor. | `new Car(engine)` |
| **Setter Injection**      | Dependencies are set using setter methods.   | `car.setEngine(engine)` |
| **Interface Injection**   | The dependency provides an injector method.  | Rare, often seen in complex systems |

---

## 🏠 Real-Life Story: Restaurant Example

Let’s say we’re designing a **Restaurant App**.

### 🍲 Without DI:
The `Restaurant` class creates its own `Chef`:

```java
class Restaurant {
    Chef chef = new Chef(); // tightly coupled
}
```

Now, you can’t replace the chef with a different cuisine expert without changing the restaurant class itself.

---

### 🍱 With DI:
```java
class Restaurant {
    Chef chef;

    public Restaurant(Chef chef) {
        this.chef = chef; // dependency injected
    }
}
```

Now you can inject:
- `ItalianChef`
- `JapaneseChef`
- `MockChef` for testing

Your restaurant is more flexible and scalable!

---

## 🧪 DI in Testing

You can pass a mock dependency easily:

```java
SpellChecker mockChecker = Mockito.mock(SpellChecker.class);
TextEditor editor = new TextEditor(mockChecker);
```

This allows you to test `TextEditor` in isolation without worrying about how `SpellChecker` works.

---

## 🤖 Frameworks that Support DI

| Language | Frameworks |
|----------|------------|
| Java     | Spring, Guice |
| PHP      | Laravel (Service Container) |
| Python   | FastAPI, Flask with plugins |
| JavaScript | Angular (built-in DI) |

---

## ✅ Summary

| Concept           | Description |
|-------------------|-------------|
| **Dependency Injection** | Giving objects what they need rather than letting them create it. |
| **Why use it?**   | To reduce coupling, improve flexibility, make testing easier. |
| **Types**         | Constructor, Setter, Interface |
| **Analogy**       | A car is given an engine from the outside garage. |
| **Real Benefit**  | Better design, modularity, and testability in large systems. |

---

## 🎯 OOP Interview Questions — Critical Scenarios

---

### 🏗️ Core Concepts

**Q1. When should you use composition over inheritance?**

**Answer:**
The classic rule is **"favor composition over inheritance"** (Gang of Four). Inheritance creates tight coupling — changes to the parent cascade to all children.

```php
// ❌ Inheritance: FlyingFish breaks the hierarchy
class Animal {
    public function breathe(): void { echo "Breathing\n"; }
}
class Fish extends Animal {
    public function swim(): void { echo "Swimming\n"; }
}
class FlyingFish extends Fish {
    public function fly(): void { echo "Flying\n"; }
}
// What if we need SwimmingBird? Multiple inheritance not supported.

// ✅ Composition: Mix behaviors freely
interface Swimmable {
    public function swim(): void;
}
interface Flyable {
    public function fly(): void;
}

class SwimBehavior implements Swimmable {
    public function swim(): void { echo "Swimming\n"; }
}
class FlyBehavior implements Flyable {
    public function fly(): void { echo "Flying\n"; }
}

class FlyingFish {
    public function __construct(
        private Swimmable $swimBehavior,
        private Flyable $flyBehavior
    ) {}

    public function swim(): void { $this->swimBehavior->swim(); }
    public function fly(): void { $this->flyBehavior->fly(); }
}
```

**Use inheritance when:** "IS-A" relationship is genuine and stable (Cat IS-A Animal).  
**Use composition when:** "HAS-A" or "CAN-DO" relationship (Car HAS-A Engine, Duck CAN-DO Swimming).

**Rule of thumb:** If you find yourself overriding a method to throw `UnsupportedOperationException` or `NotImplementedException`, inheritance is wrong.

---

**Q2. What is the difference between polymorphism at compile-time and runtime?**

**Answer:**

**Compile-time (Static Polymorphism):** Method overloading — resolved at compile time based on method signature.

```php
class Calculator {
    // PHP doesn't support true overloading, but same concept:
    public function add(int $a, int $b): int { return $a + $b; }
    // Different parameter types would be handled differently in Java/C++
}

// Java example:
// int add(int a, int b)     → compile picks this for int args
// float add(float a, float b) → compile picks this for float args
```

**Runtime (Dynamic Polymorphism):** Method overriding — resolved at runtime based on actual object type.

```php
abstract class Shape {
    abstract public function area(): float;
    
    public function describe(): string {
        return "I am a shape with area: " . $this->area();  // Dynamic dispatch
    }
}

class Circle extends Shape {
    public function __construct(private float $radius) {}
    public function area(): float { return M_PI * $this->radius ** 2; }
}

class Square extends Shape {
    public function __construct(private float $side) {}
    public function area(): float { return $this->side ** 2; }
}

$shapes = [new Circle(5), new Square(4)];
foreach ($shapes as $shape) {
    echo $shape->describe();  // Calls correct area() at runtime
}
```

**Why it matters:** Runtime polymorphism is the foundation of the Open/Closed Principle — you add new shapes without modifying `describe()`.

---

**Q3. Explain a real scenario where each SOLID principle would be violated and how to fix it.**

**Answer:**

**SRP Violation:**
```php
// ❌ UserController handles auth, business logic, AND email
class UserController {
    public function register(Request $request) {
        $user = User::create($request->all());   // DB logic
        Mail::send('welcome', $user);            // Email logic
        JWT::generate($user);                    // Auth logic
        // 3 reasons to change this class
    }
}

// ✅ Each responsibility in its own class
class UserController {
    public function register(Request $request) {
        $user = $this->userService->register($request->validated());
        return $this->authService->tokenResponse($user);
    }
}
```

**OCP Violation:**
```php
// ❌ Must modify class to add new payment method
class PaymentProcessor {
    public function process(string $type, float $amount) {
        if ($type === 'credit') { /* credit logic */ }
        elseif ($type === 'paypal') { /* paypal logic */ }
        // Must add elseif for every new method
    }
}

// ✅ Open for extension via interface
interface PaymentGateway {
    public function charge(float $amount): bool;
}
class StripeGateway implements PaymentGateway { ... }
class PayPalGateway implements PaymentGateway { ... }
```

**LSP Violation (classic):**
```php
// ❌ ReadOnlyFile throws exception on write — breaks parent contract
class File {
    public function write(string $data): void { file_put_contents($this->path, $data); }
}
class ReadOnlyFile extends File {
    public function write(string $data): void {
        throw new ReadOnlyException("Cannot write");  // LSP violated
    }
}

// ✅ Use interface segregation instead
interface Readable { public function read(): string; }
interface Writable { public function write(string $data): void; }
class ReadOnlyFile implements Readable { ... }
class WriteableFile implements Readable, Writable { ... }
```

---

### 🔄 Design Questions

**Q4. What is the difference between Association, Aggregation, and Composition?**

**Answer:**
All three describe relationships between classes, but differ in lifecycle dependency:

```
Association:   A uses B, but A and B have independent lifecycles
Aggregation:   A HAS-A B, but B can exist without A (weak ownership)
Composition:   A HAS-A B, B CANNOT exist without A (strong ownership)
```

```php
// Association — Teacher uses Classroom (neither owns the other)
class Teacher {
    public function teach(Classroom $room): void {
        $room->prepare();
    }
}

// Aggregation — Department has Professors (professors can exist without dept)
class Department {
    private array $professors = [];
    public function addProfessor(Professor $p): void {
        $this->professors[] = $p;
    }
}

// Composition — Order contains OrderItems (items can't exist without order)
class Order {
    private array $items = [];
    
    public function addItem(string $product, int $qty): void {
        $this->items[] = new OrderItem($product, $qty);  // Order creates items
    }
    // When Order is deleted, OrderItems are deleted (cascade)
}
```

**In database terms:**
- Aggregation → nullable FK (professor.department_id can be NULL)
- Composition → NOT NULL FK with CASCADE DELETE

---

**Q5. How does PHP handle object cloning and what are the pitfalls?**

**Answer:**
`clone` creates a **shallow copy** — primitive properties are copied, but objects are still referenced (not copied).

```php
class Cart {
    public array $items = [];
    public CustomerInfo $customer;  // Object reference

    public function __clone() {
        // Deep clone objects inside
        $this->customer = clone $this->customer;
        // Arrays of objects also need deep cloning:
        $this->items = array_map(fn($item) => clone $item, $this->items);
    }
}

$cart1 = new Cart();
$cart1->customer = new CustomerInfo("Alice");
$cart2 = clone $cart1;

// Without __clone(): $cart2->customer IS $cart1->customer (same reference!)
// Modifying cart2's customer would modify cart1's customer too

// With __clone(): separate CustomerInfo objects
$cart2->customer->name = "Bob";
echo $cart1->customer->name; // "Alice" (not affected)
```

**Real-world use:** Prototype pattern, creating order templates, copying configurations with modifications.

---

**Q6. What are magic methods in PHP and when should/shouldn't you use them?**

**Answer:**

| Magic Method | Triggered When | Use Case |
|-------------|---------------|---------|
| `__construct` | Object created | Initialization |
| `__destruct` | Object destroyed | Resource cleanup |
| `__get($name)` | Reading inaccessible property | Lazy loading, proxies |
| `__set($name, $value)` | Writing inaccessible property | Validation, active record |
| `__isset($name)` | `isset()` on inaccessible property | Companion to `__get` |
| `__call($name, $args)` | Calling inaccessible method | Fluent builders, proxies |
| `__toString` | Object used as string | Debug output, model display |
| `__invoke` | Object called as function | Callable objects, middleware |
| `__serialize`/`__unserialize` | Serialization | Custom serialization format |

```php
class FluentQuery {
    private array $conditions = [];

    // __call allows dynamic where methods
    public function __call(string $method, array $args): static {
        if (str_starts_with($method, 'where')) {
            $field = lcfirst(substr($method, 5)); // whereUserId → userId
            $this->conditions[$field] = $args[0];
            return $this;
        }
        throw new BadMethodCallException("Method $method not found");
    }
}

$query = (new FluentQuery())
    ->whereUserId(5)
    ->whereStatus('active');
```

**When NOT to use magic methods:**
- `__get/__set` for normal properties — use explicit getters/setters (IDE can't autocomplete magic properties)
- `__call` for large method sets — it hides the API, hard to document
- `__destruct` for critical cleanup — PHP doesn't guarantee order of destruction

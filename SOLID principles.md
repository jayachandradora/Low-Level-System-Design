# SOLID principles

The SOLID principles are a set of design principles intended to make software designs more understandable, flexible, and maintainable. They are particularly useful in object-oriented programming. Here's a detailed breakdown of each principle, along with Java examples and best use cases.

### 1. Single Responsibility Principle (SRP)

**Definition**: A class should have only one reason to change, meaning it should have only one job or responsibility.

**Example**:
```java
// Violation of SRP
class User {
    private String name;

    public void saveUser() {
        // Save user to database
    }

    public void sendEmail() {
        // Send email to user
    }
}

// Compliant with SRP
class User {
    private String name;
    // User properties and methods
}

class UserRepository {
    public void saveUser(User user) {
        // Save user to database
    }
}

class EmailService {
    public void sendEmail(User user) {
        // Send email to user
    }
}
```

**Best Use Cases**: When you notice a class doing too much, it’s a good indication that you should refactor it to comply with SRP.

### 2. Open/Closed Principle (OCP)

**Definition**: Software entities (classes, modules, functions, etc.) should be open for extension but closed for modification.

**Example**:
```java
// Violation of OCP
class Shape {
    public double area() {
        // Calculate area
        return 0;
    }
}

class Rectangle extends Shape {
    private double width, height;
    public double area() {
        return width * height;
    }
}

// Compliant with OCP
interface Shape {
    double area();
}

class Rectangle implements Shape {
    private double width, height;
    public double area() {
        return width * height;
    }
}

class Circle implements Shape {
    private double radius;
    public double area() {
        return Math.PI * radius * radius;
    }
}
```

**Best Use Cases**: Use OCP when you want to add new functionality without altering existing code, making it less prone to bugs.

### 3. Liskov Substitution Principle (LSP)

**Definition**: Subtypes must be substitutable for their base types without altering the correctness of the program.

**Example**:
```java
// Violation of LSP
class Bird {
    public void fly() {}
}

class Ostrich extends Bird {
    @Override
    public void fly() {
        throw new UnsupportedOperationException("Ostrich can't fly");
    }
}

// Compliant with LSP
interface Bird {
    void eat();
}

interface Flyable {
    void fly();
}

class Sparrow implements Bird, Flyable {
    public void eat() {}
    public void fly() {}
}

class Ostrich implements Bird {
    public void eat() {}
}
```

**Best Use Cases**: Ensure subclasses behave correctly when used in place of their parent classes, especially in polymorphic contexts.

### 4. Interface Segregation Principle (ISP)

**Definition**: No client should be forced to depend on methods it does not use. Split large interfaces into smaller, specific ones.

**Example**:
```java
// Violation of ISP
interface Worker {
    void work();
    void eat();
}

class HumanWorker implements Worker {
    public void work() {}
    public void eat() {}
}

class RobotWorker implements Worker {
    public void work() {}
    public void eat() {
        throw new UnsupportedOperationException();
    }
}

// Compliant with ISP
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

class HumanWorker implements Workable, Eatable {
    public void work() {}
    public void eat() {}
}

class RobotWorker implements Workable {
    public void work() {}
}
```

**Best Use Cases**: Use ISP when you have clients with different requirements, allowing you to create specialized interfaces.

### 5. Dependency Inversion Principle (DIP)

**Definition**: High-level modules should not depend on low-level modules. Both should depend on abstractions. Abstractions should not depend on details; details should depend on abstractions.

**Example**:
```java
// Violation of DIP
class LightBulb {
    public void turnOn() {}
}

class Switch {
    private LightBulb bulb;

    public Switch(LightBulb bulb) {
        this.bulb = bulb;
    }

    public void operate() {
        bulb.turnOn();
    }
}

// Compliant with DIP
interface Switchable {
    void turnOn();
}

class LightBulb implements Switchable {
    public void turnOn() {}
}

class Switch {
    private Switchable device;

    public Switch(Switchable device) {
        this.device = device;
    }

    public void operate() {
        device.turnOn();
    }
}
```

**Best Use Cases**: Use DIP when you want to reduce dependencies between modules, allowing for greater flexibility and easier testing.

### Summary

Applying the SOLID principles leads to more robust and maintainable code. They are particularly useful in large systems where requirements may change over time. By adhering to these principles, you create systems that are easier to understand, extend, and modify.

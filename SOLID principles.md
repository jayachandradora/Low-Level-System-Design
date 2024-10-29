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


## Dependency Inversion Principle (DIP)

**Definition**: The Dependency Inversion Principle states that high-level modules should not depend on low-level modules; both should depend on abstractions. Additionally, abstractions should not depend on details; details should depend on abstractions.

### Key Concepts

1. **High-Level Modules**: These are the modules that provide complex logic or functionality in your application. They typically represent the application's core behavior.

2. **Low-Level Modules**: These are the modules that deal with specific implementations or lower-level details, such as data access, file operations, or hardware interactions.

3. **Abstractions**: Interfaces or abstract classes that define contracts for behavior, allowing different implementations to be plugged in.

### Explanation

The principle encourages you to depend on interfaces (abstractions) rather than concrete implementations. This separation allows for greater flexibility and makes it easier to replace or change components without affecting the entire system.

### Example

Here’s an expanded example to illustrate the Dependency Inversion Principle:

**Scenario**: Let's say you have a simple notification system that sends messages. Initially, it directly depends on a specific implementation (like Email).

#### Without DIP (Violation)

```java
class EmailService {
    public void sendEmail(String message) {
        // Logic to send an email
        System.out.println("Email sent: " + message);
    }
}

class Notification {
    private EmailService emailService;

    public Notification() {
        this.emailService = new EmailService(); // Tight coupling
    }

    public void notifyUser(String message) {
        emailService.sendEmail(message);
    }
}

public class Main {
    public static void main(String[] args) {
        Notification notification = new Notification();
        notification.notifyUser("Hello, User!");
    }
}
```

**Issues**:
- **Tight Coupling**: `Notification` is tightly coupled to `EmailService`. If you want to change how notifications are sent (e.g., via SMS), you would need to modify the `Notification` class.
- **Difficult to Test**: Testing the `Notification` class in isolation becomes complicated since it directly depends on `EmailService`.

#### With DIP (Compliant)

```java
interface NotificationService {
    void notify(String message);
}

class EmailService implements NotificationService {
    public void notify(String message) {
        // Logic to send an email
        System.out.println("Email sent: " + message);
    }
}

class SMSService implements NotificationService {
    public void notify(String message) {
        // Logic to send an SMS
        System.out.println("SMS sent: " + message);
    }
}

class Notification {
    private NotificationService notificationService;

    // Dependency Injection via Constructor
    public Notification(NotificationService notificationService) {
        this.notificationService = notificationService; // Depend on abstraction
    }

    public void notifyUser(String message) {
        notificationService.notify(message);
    }
}

public class Main {
    public static void main(String[] args) {
        NotificationService emailService = new EmailService();
        Notification notification = new Notification(emailService);
        notification.notifyUser("Hello, User!");

        // If you want to use SMS instead:
        NotificationService smsService = new SMSService();
        Notification smsNotification = new Notification(smsService);
        smsNotification.notifyUser("Hello, User!");
    }
}
```

**Benefits**:
- **Loose Coupling**: The `Notification` class is now loosely coupled to `NotificationService`. You can easily change the notification method by passing a different implementation without modifying the `Notification` class.
- **Easier Testing**: You can now easily mock `NotificationService` for unit tests, allowing for isolated testing of the `Notification` class.

### Best Use Cases for DIP

1. **Evolving Systems**: When building applications that are expected to evolve over time, use DIP to accommodate future changes without major rewrites.

2. **Testing**: In scenarios requiring extensive unit testing, DIP allows you to mock dependencies, making tests more reliable and easier to implement.

3. **Frameworks and Libraries**: When creating frameworks or libraries that other developers will use, implementing DIP allows them to extend your framework without modifying its core logic.

4. **Microservices Architecture**: In microservices, where services interact with various external systems, DIP allows for flexibility in integrating different services or replacing them without major changes.

### Summary

The Dependency Inversion Principle is crucial for building robust, maintainable systems. By adhering to this principle, you promote loose coupling, making your codebase easier to manage and extend while improving testability.

## Open/Closed Principle (OCP)

The Open/Closed Principle (OCP) is one of the five SOLID principles of object-oriented design. It states that software entities (classes, modules, functions, etc.) should be open for extension but closed for modification. This means you should be able to add new functionality to an existing class without changing its existing code.

### Detailed Explanation

1. **Open for Extension**: You should be able to extend the behavior of a class. This often involves creating new classes or interfaces that inherit or implement the existing functionality.

2. **Closed for Modification**: The existing code should not be changed when adding new functionality. This reduces the risk of introducing bugs into existing code and helps maintain stability.

### Use Cases

1. **Plugin Systems**: In systems where you need to add new features (like an IDE), using interfaces and abstract classes allows you to implement new plugins without changing the core code.

2. **Payment Processing**: If you have a payment system that supports multiple payment methods, you can create an interface like `PaymentMethod` and implement it for `CreditCard`, `PayPal`, etc. You can add new payment methods without modifying existing code.

3. **Shape Drawing**: Consider a drawing application. Instead of modifying a single `Shape` class to add new shapes, you can create a base `Shape` interface and extend it with new shape classes like `Circle`, `Square`, etc.

### Example in Java

Let's illustrate the Open/Closed Principle with a payment processing example.

#### Step 1: Define an Interface

```java
public interface PaymentMethod {
    void pay(double amount);
}
```

#### Step 2: Implement Concrete Classes

```java
public class CreditCardPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " using Credit Card.");
    }
}

public class PayPalPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " using PayPal.");
    }
}
```

#### Step 3: Use in Client Code

```java
public class PaymentProcessor {
    private List<PaymentMethod> paymentMethods;

    public PaymentProcessor() {
        paymentMethods = new ArrayList<>();
    }

    public void addPaymentMethod(PaymentMethod method) {
        paymentMethods.add(method);
    }

    public void processPayments(double amount) {
        for (PaymentMethod method : paymentMethods) {
            method.pay(amount);
        }
    }
}
```

#### Step 4: Extending Functionality

To add a new payment method (e.g., `BitcoinPayment`), you simply create a new class without modifying the existing code:

```java
public class BitcoinPayment implements PaymentMethod {
    @Override
    public void pay(double amount) {
        System.out.println("Paying " + amount + " using Bitcoin.");
    }
}
```

### Conclusion

The Open/Closed Principle encourages developers to write more maintainable and flexible code. By designing your system to be extensible without modifying existing code, you reduce the risk of bugs and make your codebase easier to work with as requirements change. This principle is particularly useful in large projects where maintaining stability while adding features is critical.



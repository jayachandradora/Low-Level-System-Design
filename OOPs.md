# OOPs 

Object-Oriented Programming (OOP) is a programming paradigm that uses "objects" to represent data and methods to manipulate that data. The four main concepts of OOP are:

1. **Encapsulation**
2. **Abstraction**
3. **Inheritance**
4. **Polymorphism**

Let's explore each concept with detailed examples and Java implementations.

### 1. Encapsulation

**Definition**: Encapsulation is the bundling of data (attributes) and methods (functions) that operate on that data into a single unit, usually a class. It restricts direct access to some components and can prevent the accidental modification of data.

**Example**: A `BankAccount` class.

```java
class BankAccount {
    private double balance; // Private variable

    public BankAccount(double initialBalance) {
        this.balance = initialBalance;
    }

    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        }
    }

    public double getBalance() {
        return balance; // Accessor method
    }
}

// Use case
public class Main {
    public static void main(String[] args) {
        BankAccount account = new BankAccount(1000);
        account.deposit(500);
        account.withdraw(200);
        System.out.println("Balance: " + account.getBalance()); // Outputs: Balance: 1300.0
    }
}
```

### 2. Abstraction

**Definition**: Abstraction is the concept of hiding the complex reality while exposing only the necessary parts. It helps in reducing programming complexity and effort.

**Example**: An abstract class `Shape`.

```java
abstract class Shape {
    abstract void draw(); // Abstract method
}

class Circle extends Shape {
    void draw() {
        System.out.println("Drawing a Circle");
    }
}

class Rectangle extends Shape {
    void draw() {
        System.out.println("Drawing a Rectangle");
    }
}

// Use case
public class Main {
    public static void main(String[] args) {
        Shape circle = new Circle();
        Shape rectangle = new Rectangle();
        
        circle.draw(); // Outputs: Drawing a Circle
        rectangle.draw(); // Outputs: Drawing a Rectangle
    }
}
```

### 3. Inheritance

**Definition**: Inheritance is a mechanism where a new class (subclass) inherits the properties and behavior (methods) of another class (superclass). This promotes code reusability.

**Example**: A `Vehicle` superclass and `Car` subclass.

```java
class Vehicle {
    String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public void honk() {
        System.out.println("Honk! Honk!");
    }
}

class Car extends Vehicle {
    int doors;

    public Car(String brand, int doors) {
        super(brand);
        this.doors = doors;
    }

    public void displayInfo() {
        System.out.println("Brand: " + brand + ", Doors: " + doors);
    }
}

// Use case
public class Main {
    public static void main(String[] args) {
        Car car = new Car("Toyota", 4);
        car.honk(); // Outputs: Honk! Honk!
        car.displayInfo(); // Outputs: Brand: Toyota, Doors: 4
    }
}
```

### 4. Polymorphism

**Definition**: Polymorphism allows methods to do different things based on the object it is acting upon. The two types of polymorphism are compile-time (method overloading) and runtime (method overriding).

**Example**: Method overriding with `Shape`.

```java
class Triangle extends Shape {
    void draw() {
        System.out.println("Drawing a Triangle");
    }
}

// Use case
public class Main {
    public static void main(String[] args) {
        Shape[] shapes = { new Circle(), new Rectangle(), new Triangle() };
        
        for (Shape shape : shapes) {
            shape.draw(); // Outputs: Drawing a Circle, Drawing a Rectangle, Drawing a Triangle
        }
    }
}
```

### Summary

- **Encapsulation**: Protects data within a class.
- **Abstraction**: Hides complexity and exposes only essentials.
- **Inheritance**: Enables new classes to reuse code from existing classes.
- **Polymorphism**: Allows methods to be called in different contexts.

These OOP concepts facilitate better organization, code reuse, and maintainability in Java applications.

## Encapsulation: Protecting Data Within a Class

### Encapsulation: Protecting Data Within a Class

**Definition**: Encapsulation is a fundamental principle of Object-Oriented Programming that restricts direct access to some of an object's components. This is done by making the fields (attributes) of a class private and providing public methods to access and modify these fields. This protects the integrity of the data and ensures that the object maintains a valid state.

### Why Use Encapsulation?

1. **Data Hiding**: Sensitive data can be hidden from outside interference and misuse.
2. **Control**: You can control how data is accessed and modified.
3. **Flexibility and Maintainability**: Changes to encapsulated code can be made with minimal impact on other parts of the program.
4. **Improved Security**: Protects the data from being corrupted by invalid values.

### Example: `Person` Class

Let's implement a `Person` class that demonstrates encapsulation.

```java
class Person {
    private String name;  // Private variable
    private int age;      // Private variable

    // Constructor
    public Person(String name, int age) {
        this.name = name;
        setAge(age); // Use the setter to ensure valid age
    }

    // Getter for name
    public String getName() {
        return name;
    }

    // Setter for name
    public void setName(String name) {
        this.name = name;
    }

    // Getter for age
    public int getAge() {
        return age;
    }

    // Setter for age with validation
    public void setAge(int age) {
        if (age >= 0) { // Ensure age is valid
            this.age = age;
        } else {
            System.out.println("Age cannot be negative.");
        }
    }
}

// Use case
public class Main {
    public static void main(String[] args) {
        Person person = new Person("Alice", 30);

        // Accessing properties using getters
        System.out.println("Name: " + person.getName()); // Outputs: Name: Alice
        System.out.println("Age: " + person.getAge());   // Outputs: Age: 30

        // Modifying properties using setters
        person.setAge(25);
        System.out.println("Updated Age: " + person.getAge()); // Outputs: Updated Age: 25

        // Attempting to set an invalid age
        person.setAge(-5); // Outputs: Age cannot be negative.
        System.out.println("After invalid update, Age: " + person.getAge()); // Still 25
    }
}
```

### Explanation of the Example

1. **Private Variables**: The `name` and `age` attributes are declared as private, meaning they cannot be accessed directly from outside the `Person` class.

2. **Constructor**: The constructor initializes the `name` and calls the `setAge` method to ensure that the `age` is valid right from the start.

3. **Getters and Setters**: 
   - The `getName` and `getAge` methods allow controlled access to the private fields.
   - The `setName` method allows changing the name, while the `setAge` method includes validation to ensure that the age cannot be set to a negative value.

4. **Data Integrity**: The validation in the `setAge` method protects the `age` field from invalid inputs, maintaining the integrity of the `Person` object.

### Conclusion

Encapsulation in this example demonstrates how protecting class data can lead to better control, security, and maintainability. By using private fields and public methods, you can enforce rules and ensure that your objects remain in a valid state throughout their lifecycle.

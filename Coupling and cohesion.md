Coupling and cohesion are fundamental concepts in software design that help create maintainable and scalable systems. They play a crucial role in how components within a system interact with each other.

### Cohesion

**Definition**: Cohesion refers to how closely related and focused the responsibilities of a single module or class are. High cohesion means that a module is designed to perform a specific task, making it easier to maintain and understand.

#### Types of Cohesion (from lowest to highest):

1. **Coincidental Cohesion**: Parts are grouped arbitrarily. There's no meaningful relationship between them.
   - **Example**: A utility class with methods for file handling, string manipulation, and date formatting.

2. **Logical Cohesion**: Parts are grouped because they perform similar functions, even though they are different in nature.
   - **Example**: A class that processes different types of input (e.g., keyboard, mouse) but has no shared functionality.

3. **Temporal Cohesion**: Parts are grouped by when they are executed.
   - **Example**: A class that initializes several unrelated components at startup.

4. **Procedural Cohesion**: Parts are grouped because they always follow a specific sequence of execution.
   - **Example**: A method that first reads data, then processes it, and finally saves it.

5. **Communicational Cohesion**: Parts are grouped because they operate on the same data set.
   - **Example**: A class that retrieves user information and displays it.

6. **Sequential Cohesion**: Parts are grouped such that output from one part is the input to another.
   - **Example**: A pipeline class that processes data step-by-step.

7. **Functional Cohesion**: All parts contribute to a single well-defined task.
   - **Example**: A class that calculates and returns the area of different shapes.

#### Example of High Cohesion in Java

```java
public class Calculator {
    public double add(double a, double b) {
        return a + b;
    }

    public double subtract(double a, double b) {
        return a - b;
    }

    public double multiply(double a, double b) {
        return a * b;
    }

    public double divide(double a, double b) {
        if (b == 0) throw new IllegalArgumentException("Cannot divide by zero");
        return a / b;
    }
}
```

In this example, the `Calculator` class has high cohesion because all its methods relate directly to arithmetic operations.

### Coupling

**Definition**: Coupling refers to the degree of interdependence between software modules. Low coupling means that a module does not heavily depend on other modules, which makes it easier to change and maintain.

#### Types of Coupling (from highest to lowest):

1. **Content Coupling**: One module directly accesses the content of another module.
   - **Example**: A method modifying the internal data of another class.

2. **Common Coupling**: Two modules share the same global data.
   - **Example**: Multiple classes accessing and modifying the same global variable.

3. **External Coupling**: Modules depend on externally imposed data formats or protocols.
   - **Example**: Two modules communicating through a specific file format.

4. **Control Coupling**: One module controls the behavior of another by passing it information on what to do (e.g., flags).
   - **Example**: A function that takes a control flag to determine its behavior.

5. **Stamp Coupling**: Modules share a composite data structure and use only part of it.
   - **Example**: A method that takes an object containing multiple fields, but only uses one.

6. **Data Coupling**: Modules share data through parameters, and each piece of data is essential for the other module's operation.
   - **Example**: A method that takes simple data types as arguments.

7. **Message Coupling**: Modules communicate only through message passing, making them entirely independent.
   - **Example**: An event-driven architecture where components communicate through events.

#### Example of Low Coupling in Java

```java
public class UserService {
    private Database database;

    public UserService(Database database) {
        this.database = database;
    }

    public User getUser(int userId) {
        return database.findUserById(userId);
    }
}

public class Database {
    public User findUserById(int userId) {
        // Database query logic
        return new User(userId, "John Doe");
    }
}
```

In this example, `UserService` and `Database` have low coupling. The `UserService` class depends on the `Database` class, but the dependency is injected through the constructor. This allows for easy testing and replacement of the `Database` implementation without modifying `UserService`.

### Use Cases

1. **High Cohesion Use Case**:
   - **Scenario**: A billing system where you want to keep calculations related to invoices separate.
   - **Benefit**: If calculations are encapsulated in a class focused solely on billing, it becomes easier to update or extend billing-related functionalities without affecting other parts of the application.

2. **Low Coupling Use Case**:
   - **Scenario**: In a large enterprise application with multiple modules, having a user authentication module that can be used by various other modules (like billing, order processing, etc.) without tightly coupling them to the authentication logic.
   - **Benefit**: If the authentication method changes, you only need to update the authentication module without modifying every module that relies on it.

### Conclusion

Understanding and applying the principles of coupling and cohesion can significantly improve software design. High cohesion within modules leads to clearer responsibilities and easier maintenance, while low coupling between modules enhances flexibility and adaptability, making the codebase more robust and easier to work with as requirements evolve.

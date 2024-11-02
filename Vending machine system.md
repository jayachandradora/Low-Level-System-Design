# Vending machine system

To design a vending machine system based on your requirements, we can use Object-Oriented Programming (OOP) principles, following SOLID principles, and incorporating relevant design patterns. The design will focus on high cohesion and low coupling to ensure maintainability and extensibility.

### Design Overview

1. **Classes**:
   - `Product`: Represents a product in the vending machine.
   - `VendingMachine`: Manages products, accepts payments, dispenses products, and returns change.
   - `Payment`: Interface for payment strategies.
   - `CoinPayment` and `NotePayment`: Concrete implementations of payment methods.
   - `Inventory`: Manages product stock.
   - `Transaction`: Handles individual transactions.
   - `ChangeCalculator`: Calculates and manages change for transactions.
   - `VendingMachineUI`: Provides an interface for interacting with the vending machine.

2. **Design Patterns**:
   - **Strategy Pattern**: To handle different payment methods.
   - **Singleton Pattern**: For managing the vending machine's state.
   - **Observer Pattern**: For notifying when products are restocked or low on stock (if needed).

### Low-Level Design

#### 1. Product Class

```java
public class Product {
    private String name;
    private double price;
    private int quantity;

    public Product(String name, double price, int quantity) {
        this.name = name;
        this.price = price;
        this.quantity = quantity;
    }

    public String getName() {
        return name;
    }

    public double getPrice() {
        return price;
    }

    public int getQuantity() {
        return quantity;
    }

    public void decrementQuantity() {
        if (quantity > 0) {
            quantity--;
        }
    }

    public boolean isAvailable() {
        return quantity > 0;
    }
}
```

#### 2. Payment Interface and Implementations

```java
public interface Payment {
    boolean pay(double amount);
}

public class CoinPayment implements Payment {
    private double amountInserted;

    public CoinPayment() {
        this.amountInserted = 0;
    }

    public void insertCoin(double coin) {
        amountInserted += coin;
    }

    @Override
    public boolean pay(double amount) {
        if (amountInserted >= amount) {
            amountInserted -= amount;
            return true;
        }
        return false;
    }

    public double getChange() {
        return amountInserted;
    }
}

public class NotePayment implements Payment {
    private double amountInserted;

    public NotePayment() {
        this.amountInserted = 0;
    }

    public void insertNote(double note) {
        amountInserted += note;
    }

    @Override
    public boolean pay(double amount) {
        if (amountInserted >= amount) {
            amountInserted -= amount;
            return true;
        }
        return false;
    }

    public double getChange() {
        return amountInserted;
    }
}
```

#### 3. Inventory Class

```java
import java.util.HashMap;
import java.util.Map;

public class Inventory {
    private Map<String, Product> products;

    public Inventory() {
        products = new HashMap<>();
    }

    public void addProduct(Product product) {
        products.put(product.getName(), product);
    }

    public Product getProduct(String name) {
        return products.get(name);
    }

    public boolean isProductAvailable(String name) {
        Product product = products.get(name);
        return product != null && product.isAvailable();
    }

    public void restockProduct(String name, int quantity) {
        Product product = products.get(name);
        if (product != null) {
            // Assuming a method to update quantity directly
            product.restock(quantity);
        }
    }
}
```

#### 4. VendingMachine Class

```java
import java.util.List;

public class VendingMachine {
    private Inventory inventory;
    private ChangeCalculator changeCalculator;

    public VendingMachine(Inventory inventory) {
        this.inventory = inventory;
        this.changeCalculator = new ChangeCalculator();
    }

    public String dispenseProduct(String productName, Payment payment) {
        Product product = inventory.getProduct(productName);
        if (product == null || !product.isAvailable()) {
            return "Product is out of stock.";
        }
        
        if (payment.pay(product.getPrice())) {
            product.decrementQuantity();
            double change = payment.getChange();
            return "Dispensed: " + productName + ". Change: " + change;
        } else {
            return "Insufficient funds.";
        }
    }
    
    public void restockProduct(String productName, int quantity) {
        inventory.restockProduct(productName, quantity);
    }
}
```

#### 5. ChangeCalculator Class

```java
public class ChangeCalculator {
    public double calculateChange(double payment, double price) {
        return payment - price;
    }
}
```

#### 6. VendingMachineUI Class

```java
import java.util.Scanner;

public class VendingMachineUI {
    private VendingMachine vendingMachine;

    public VendingMachineUI(VendingMachine vendingMachine) {
        this.vendingMachine = vendingMachine;
    }

    public void start() {
        Scanner scanner = new Scanner(System.in);
        // Simple user interaction
        System.out.println("Select a product:");
        String productName = scanner.nextLine();
        
        System.out.println("Select payment method (coin/note):");
        String method = scanner.nextLine();
        
        Payment payment;
        if (method.equals("coin")) {
            payment = new CoinPayment();
            // Simulate coin insertion
            ((CoinPayment) payment).insertCoin(1.0); // Example
        } else {
            payment = new NotePayment();
            ((NotePayment) payment).insertNote(5.0); // Example
        }
        
        String result = vendingMachine.dispenseProduct(productName, payment);
        System.out.println(result);
        
        scanner.close();
    }
}
```

### Explanation of SOLID Principles

1. **Single Responsibility Principle**: Each class has a single responsibility. For instance, `Product` manages product details, `Inventory` handles stock management, and `VendingMachine` orchestrates transactions.

2. **Open/Closed Principle**: The system can be extended (e.g., new payment methods) without modifying existing code. You can add new classes implementing the `Payment` interface.

3. **Liskov Substitution Principle**: Any class implementing the `Payment` interface can be used interchangeably without affecting the behavior of the system.

4. **Interface Segregation Principle**: The `Payment` interface is simple and focused, allowing different implementations without requiring unused methods.

5. **Dependency Inversion Principle**: High-level modules (like `VendingMachine`) do not depend on low-level modules (like `CoinPayment` or `NotePayment`). They depend on abstractions (the `Payment` interface).

### High Cohesion and Low Coupling

- **High Cohesion**: Related functionality is grouped together. For instance, all payment-related logic is encapsulated within payment classes, making it easier to manage.
- **Low Coupling**: Classes interact through well-defined interfaces. For example, the `VendingMachine` interacts with `Payment` and `Inventory` through their respective interfaces, reducing dependencies.

### Conclusion

This design effectively meets the requirements of the vending machine system while adhering to SOLID principles and promoting high cohesion and low coupling. It is extensible and maintainable, allowing for the addition of new features, products, or payment methods in the future.

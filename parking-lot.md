Designing a parking lot system is a classic problem in software design that can help illustrate principles of object-oriented design, data structures, and system behavior. Below is a low-level design (LLD) of a parking lot system in Java, along with explanations, use cases, and trade-offs.

### System Requirements

1. **Parking Lot**: Can have multiple levels, each with a certain number of spots.
2. **Parking Spots**: Can be of different types (compact, regular, oversized).
3. **Vehicles**: Can be cars, bikes, or trucks.
4. **Operations**:
   - Park a vehicle
   - Remove a vehicle
   - Check available spots
   - Track parked vehicles

### Classes and Relationships

#### 1. Vehicle Class

```java
public abstract class Vehicle {
    protected String licensePlate;
    protected VehicleSize size;

    public Vehicle(String licensePlate, VehicleSize size) {
        this.licensePlate = licensePlate;
        this.size = size;
    }

    public String getLicensePlate() {
        return licensePlate;
    }

    public VehicleSize getSize() {
        return size;
    }
}
```

#### 2. Specific Vehicle Types

```java
public class Car extends Vehicle {
    public Car(String licensePlate) {
        super(licensePlate, VehicleSize.COMPACT);
    }
}

public class Bike extends Vehicle {
    public Bike(String licensePlate) {
        super(licensePlate, VehicleSize.MOTORCYCLE);
    }
}

public class Truck extends Vehicle {
    public Truck(String licensePlate) {
        super(licensePlate, VehicleSize.LARGE);
    }
}
```

#### 3. Vehicle Size Enum

```java
public enum VehicleSize {
    MOTORCYCLE,
    COMPACT,
    LARGE
}
```

#### 4. ParkingSpot Class

```java
public class ParkingSpot {
    private VehicleSize size;
    private Vehicle currentVehicle;
    private boolean isAvailable;

    public ParkingSpot(VehicleSize size) {
        this.size = size;
        this.isAvailable = true;
    }

    public boolean park(Vehicle vehicle) {
        if (isAvailable && vehicle.getSize() == size) {
            this.currentVehicle = vehicle;
            isAvailable = false;
            return true;
        }
        return false;
    }

    public Vehicle remove() {
        Vehicle vehicle = currentVehicle;
        currentVehicle = null;
        isAvailable = true;
        return vehicle;
    }

    public boolean isAvailable() {
        return isAvailable;
    }

    public VehicleSize getSize() {
        return size;
    }
}
```

#### 5. ParkingLot Class

```java
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;

public class ParkingLot {
    private List<ParkingSpot> spots;
    private Map<String, Vehicle> parkedVehicles;

    public ParkingLot(int numCompact, int numLarge, int numMotorcycle) {
        spots = new ArrayList<>();
        parkedVehicles = new HashMap<>();
        
        for (int i = 0; i < numCompact; i++) {
            spots.add(new ParkingSpot(VehicleSize.COMPACT));
        }
        for (int i = 0; i < numLarge; i++) {
            spots.add(new ParkingSpot(VehicleSize.LARGE));
        }
        for (int i = 0; i < numMotorcycle; i++) {
            spots.add(new ParkingSpot(VehicleSize.MOTORCYCLE));
        }
    }

    public boolean parkVehicle(Vehicle vehicle) {
        for (ParkingSpot spot : spots) {
            if (spot.park(vehicle)) {
                parkedVehicles.put(vehicle.getLicensePlate(), vehicle);
                return true;
            }
        }
        return false; // No available spots
    }

    public Vehicle removeVehicle(String licensePlate) {
        Vehicle vehicle = parkedVehicles.remove(licensePlate);
        if (vehicle != null) {
            for (ParkingSpot spot : spots) {
                if (!spot.isAvailable() && spot.getSize() == vehicle.getSize()) {
                    return spot.remove();
                }
            }
        }
        return null; // Vehicle not found
    }

    public int availableSpots() {
        int count = 0;
        for (ParkingSpot spot : spots) {
            if (spot.isAvailable()) {
                count++;
            }
        }
        return count;
    }
}
```

### Use Cases

1. **Parking a Vehicle**: When a vehicle arrives, the system finds the first available spot that matches the vehicle's size.
2. **Removing a Vehicle**: When a vehicle leaves, the system frees up the spot, making it available for future vehicles.
3. **Checking Availability**: The system can provide the number of available parking spots at any time.

### Trade-offs

1. **Simplicity vs. Scalability**: 
   - This design is straightforward but may not scale well with an increasing number of vehicles or complex parking strategies (like reserving spots). Adding features such as multi-level parking or dynamic spot allocation can complicate the design.

2. **Performance**:
   - The current implementation uses a linear search to find available spots. In a larger system, using a more efficient data structure (like a priority queue) for managing available spots could improve performance.

3. **Flexibility**:
   - The design is somewhat rigid in terms of vehicle types. If you want to add new vehicle types, you need to modify the code in multiple places (Vehicle class, ParkingSpot handling). Using a more flexible design pattern (like Strategy or Factory) could alleviate this issue.

4. **Concurrency**:
   - This design does not handle concurrent access to parking spots. If multiple threads attempt to park or remove vehicles simultaneously, you may run into race conditions. Adding synchronization or using concurrent data structures would be necessary in a real-world application.

### Conclusion

This parking lot design in Java demonstrates key principles of object-oriented programming and encapsulation. The simplicity of the design makes it easy to understand and implement, while trade-offs related to performance, scalability, and concurrency should be carefully considered for production-level systems.

## To enhance the flexibility of the parking lot system using the Strategy and Factory design patterns

To enhance the flexibility of the parking lot system using the **Strategy** and **Factory** design patterns, we can separate the concerns of vehicle parking behavior and vehicle creation. This allows us to easily add new types of vehicles or change the parking strategy without modifying existing code.

### Applying the Factory Pattern

First, let's implement a **VehicleFactory** that creates different types of vehicles. This will allow us to easily add new vehicle types in the future without modifying the existing logic.

#### VehicleFactory Class

```java
public class VehicleFactory {
    public static Vehicle createVehicle(String type, String licensePlate) {
        switch (type.toLowerCase()) {
            case "car":
                return new Car(licensePlate);
            case "bike":
                return new Bike(licensePlate);
            case "truck":
                return new Truck(licensePlate);
            default:
                throw new IllegalArgumentException("Unknown vehicle type: " + type);
        }
    }
}
```

### Applying the Strategy Pattern

Next, let's define a **ParkingStrategy** interface to encapsulate different parking behaviors. We can then implement various strategies for parking vehicles.

#### ParkingStrategy Interface

```java
public interface ParkingStrategy {
    boolean parkVehicle(Vehicle vehicle, List<ParkingSpot> spots);
}
```

#### Concrete Parking Strategies

For example, let's implement two strategies: **FirstFitParkingStrategy** and **BestFitParkingStrategy**.

**FirstFitParkingStrategy**:

```java
public class FirstFitParkingStrategy implements ParkingStrategy {
    @Override
    public boolean parkVehicle(Vehicle vehicle, List<ParkingSpot> spots) {
        for (ParkingSpot spot : spots) {
            if (spot.park(vehicle)) {
                return true;
            }
        }
        return false;
    }
}
```

**BestFitParkingStrategy**:

```java
public class BestFitParkingStrategy implements ParkingStrategy {
    @Override
    public boolean parkVehicle(Vehicle vehicle, List<ParkingSpot> spots) {
        ParkingSpot bestSpot = null;
        for (ParkingSpot spot : spots) {
            if (spot.isAvailable() && spot.getSize() == vehicle.getSize()) {
                if (bestSpot == null || bestSpot.getSize().ordinal() > spot.getSize().ordinal()) {
                    bestSpot = spot;
                }
            }
        }
        return bestSpot != null && bestSpot.park(vehicle);
    }
}
```

### Modifying the ParkingLot Class

Now, we need to modify the `ParkingLot` class to use the `ParkingStrategy`:

```java
import java.util.List;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;

public class ParkingLot {
    private List<ParkingSpot> spots;
    private Map<String, Vehicle> parkedVehicles;
    private ParkingStrategy parkingStrategy;

    public ParkingLot(int numCompact, int numLarge, int numMotorcycle, ParkingStrategy strategy) {
        this.spots = new ArrayList<>();
        this.parkedVehicles = new HashMap<>();
        this.parkingStrategy = strategy;

        for (int i = 0; i < numCompact; i++) {
            spots.add(new ParkingSpot(VehicleSize.COMPACT));
        }
        for (int i = 0; i < numLarge; i++) {
            spots.add(new ParkingSpot(VehicleSize.LARGE));
        }
        for (int i = 0; i < numMotorcycle; i++) {
            spots.add(new ParkingSpot(VehicleSize.MOTORCYCLE));
        }
    }

    public boolean parkVehicle(Vehicle vehicle) {
        boolean parked = parkingStrategy.parkVehicle(vehicle, spots);
        if (parked) {
            parkedVehicles.put(vehicle.getLicensePlate(), vehicle);
        }
        return parked;
    }

    // ... (remaining methods)
}
```

### Example Usage

Now you can easily switch between different parking strategies and create vehicles using the factory:

```java
public class Main {
    public static void main(String[] args) {
        ParkingLot parkingLot = new ParkingLot(5, 5, 5, new FirstFitParkingStrategy());
        
        Vehicle car = VehicleFactory.createVehicle("car", "CAR123");
        Vehicle bike = VehicleFactory.createVehicle("bike", "BIKE123");
        
        parkingLot.parkVehicle(car);
        parkingLot.parkVehicle(bike);

        // Switch to a different strategy
        parkingLot.setParkingStrategy(new BestFitParkingStrategy());
        // Continue parking vehicles...
    }
}
```

### Trade-offs and Benefits

1. **Flexibility**: Using the Factory pattern allows you to easily add new vehicle types without modifying the existing code. The Strategy pattern allows you to change the parking behavior at runtime.

2. **Single Responsibility Principle**: Each class has a single responsibility. The factory handles vehicle creation, while the strategies handle parking logic.

3. **Extensibility**: New vehicle types and parking strategies can be added with minimal changes to existing code.

4. **Code Clarity**: The code is easier to read and maintain since responsibilities are clearly separated.

By applying these design patterns, the parking lot system becomes more modular and easier to extend, making it suitable for real-world scenarios where requirements may evolve over time.


## Implement a Priority Queue to manage available parking spots.

To improve the performance of the parking lot system by using a more efficient data structure, we can implement a **Priority Queue** to manage available parking spots. This will allow us to quickly find the most suitable parking spot based on the vehicle size or other criteria.

### Modifications to Use a Priority Queue

We'll utilize Java's `PriorityQueue` class, which provides a way to manage the parking spots based on their size. The priority queue will allow us to prioritize parking spots based on their size, making the parking operation more efficient.

### Step 1: Implement a Comparator for Parking Spots

First, we need to define a comparator to determine the order of the parking spots based on their size. This will help the priority queue to prioritize smaller (or more suitable) spots.

```java
import java.util.Comparator;

public class ParkingSpotComparator implements Comparator<ParkingSpot> {
    @Override
    public int compare(ParkingSpot spot1, ParkingSpot spot2) {
        return Integer.compare(spot1.getSize().ordinal(), spot2.getSize().ordinal());
    }
}
```

### Step 2: Modify the ParkingLot Class

We'll replace the `List<ParkingSpot>` with a `PriorityQueue<ParkingSpot>` in the `ParkingLot` class to manage the parking spots more efficiently.

#### Updated ParkingLot Class

```java
import java.util.HashMap;
import java.util.Map;
import java.util.PriorityQueue;

public class ParkingLot {
    private PriorityQueue<ParkingSpot> availableSpots;
    private Map<String, Vehicle> parkedVehicles;
    private ParkingStrategy parkingStrategy;

    public ParkingLot(int numCompact, int numLarge, int numMotorcycle, ParkingStrategy strategy) {
        this.parkedVehicles = new HashMap<>();
        this.parkingStrategy = strategy;

        availableSpots = new PriorityQueue<>(new ParkingSpotComparator());

        for (int i = 0; i < numCompact; i++) {
            availableSpots.add(new ParkingSpot(VehicleSize.COMPACT));
        }
        for (int i = 0; i < numLarge; i++) {
            availableSpots.add(new ParkingSpot(VehicleSize.LARGE));
        }
        for (int i = 0; i < numMotorcycle; i++) {
            availableSpots.add(new ParkingSpot(VehicleSize.MOTORCYCLE));
        }
    }

    public boolean parkVehicle(Vehicle vehicle) {
        // Try to park the vehicle using the strategy
        boolean parked = parkingStrategy.parkVehicle(vehicle, availableSpots);
        if (parked) {
            parkedVehicles.put(vehicle.getLicensePlate(), vehicle);
        }
        return parked;
    }

    public Vehicle removeVehicle(String licensePlate) {
        Vehicle vehicle = parkedVehicles.remove(licensePlate);
        if (vehicle != null) {
            // Find the corresponding parking spot and make it available again
            for (ParkingSpot spot : availableSpots) {
                if (!spot.isAvailable() && spot.getSize() == vehicle.getSize()) {
                    spot.remove();
                    availableSpots.offer(spot); // Re-add the spot to the queue
                    break;
                }
            }
        }
        return vehicle; // Return the removed vehicle
    }

    public int availableSpotsCount() {
        return availableSpots.size();
    }
}
```

### Step 3: Modify the Parking Strategy

Now, let's adjust the `FirstFitParkingStrategy` and `BestFitParkingStrategy` to work with the `PriorityQueue`.

#### Updated FirstFitParkingStrategy

```java
public class FirstFitParkingStrategy implements ParkingStrategy {
    @Override
    public boolean parkVehicle(Vehicle vehicle, PriorityQueue<ParkingSpot> spots) {
        for (ParkingSpot spot : spots) {
            if (spot.park(vehicle)) {
                spots.remove(spot); // Remove the spot from the queue if it is now occupied
                return true;
            }
        }
        return false; // No available spots
    }
}
```

#### Updated BestFitParkingStrategy

```java
public class BestFitParkingStrategy implements ParkingStrategy {
    @Override
    public boolean parkVehicle(Vehicle vehicle, PriorityQueue<ParkingSpot> spots) {
        ParkingSpot bestSpot = null;
        for (ParkingSpot spot : spots) {
            if (spot.isAvailable() && spot.getSize() == vehicle.getSize()) {
                if (bestSpot == null || bestSpot.getSize().ordinal() > spot.getSize().ordinal()) {
                    bestSpot = spot;
                }
            }
        }
        if (bestSpot != null && bestSpot.park(vehicle)) {
            spots.remove(bestSpot); // Remove the spot from the queue
            return true;
        }
        return false; // No suitable spot found
    }
}
```

### Step 4: Example Usage

You can now create the parking lot and use it as before:

```java
public class Main {
    public static void main(String[] args) {
        ParkingLot parkingLot = new ParkingLot(5, 5, 5, new BestFitParkingStrategy());

        Vehicle car = VehicleFactory.createVehicle("car", "CAR123");
        Vehicle bike = VehicleFactory.createVehicle("bike", "BIKE123");
        
        parkingLot.parkVehicle(car);
        parkingLot.parkVehicle(bike);

        // Display available spots
        System.out.println("Available spots: " + parkingLot.availableSpotsCount());
    }
}
```

### Trade-offs and Benefits

1. **Efficiency**: Using a `PriorityQueue` allows for efficient retrieval of the best spot based on size. The time complexity for parking a vehicle is improved, particularly in scenarios with many parking spots.

2. **Scalability**: The system can handle an increasing number of vehicles and spots without a significant drop in performance.

3. **Code Clarity**: The separation of concerns continues to enhance readability and maintainability. The parking strategy implementation remains clean and modular.

4. **Potential Drawbacks**: While using a priority queue improves efficiency for spot selection, it adds complexity. We need to ensure proper handling of the queue when vehicles are parked or removed. Additionally, if many vehicles of different sizes are parked and removed frequently, the queue management may incur overhead.

By incorporating a priority queue, the parking lot system becomes more responsive and capable of handling varying demands while maintaining clear design principles.


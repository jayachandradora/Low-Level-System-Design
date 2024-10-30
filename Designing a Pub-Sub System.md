# Designing a Pub-Sub System

Designing a Pub-Sub (Publisher-Subscriber) system in Java while adhering to SOLID principles, leveraging design patterns, and ensuring low coupling and high cohesion involves a structured approach. Here’s a detailed design that incorporates these aspects.

### 1. SOLID Principles

- **Single Responsibility Principle (SRP)**: Each class should have one reason to change.
- **Open/Closed Principle (OCP)**: Classes should be open for extension but closed for modification.
- **Liskov Substitution Principle (LSP)**: Objects of a superclass should be replaceable with objects of a subclass.
- **Interface Segregation Principle (ISP)**: Clients should not be forced to depend on interfaces they do not use.
- **Dependency Inversion Principle (DIP)**: High-level modules should not depend on low-level modules. Both should depend on abstractions.

### 2. Design Patterns

- **Observer Pattern**: To manage the relationship between publishers and subscribers.
- **Strategy Pattern**: For different message handling strategies.
- **Factory Pattern**: To create instances of subscribers or message processors dynamically.

### 3. Components of the System

#### 3.1 Interfaces

```java
public interface Subscriber {
    void update(String message);
    String getSubscriptionTopic();
}

public interface Publisher {
    void publish(String topic, String message);
}

public interface MessageHandler {
    void handle(String message);
}
```

#### 3.2 Message Class

```java
public class Message {
    private final String content;

    public Message(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }
}
```

#### 3.3 PubSub System

```java
import java.util.*;

public class PubSubSystem {
    private final Map<String, List<Subscriber>> subscribersMap = new HashMap<>();

    public void subscribe(Subscriber subscriber) {
        String topic = subscriber.getSubscriptionTopic();
        subscribersMap.computeIfAbsent(topic, k -> new ArrayList<>()).add(subscriber);
    }

    public void unsubscribe(Subscriber subscriber) {
        String topic = subscriber.getSubscriptionTopic();
        List<Subscriber> subscribers = subscribersMap.get(topic);
        if (subscribers != null) {
            subscribers.remove(subscriber);
        }
    }

    public void publish(String topic, String message) {
        List<Subscriber> subscribers = subscribersMap.get(topic);
        if (subscribers != null) {
            for (Subscriber subscriber : subscribers) {
                subscriber.update(message);
            }
        }
    }
}
```

### 4. Concrete Subscribers

```java
public class ConcreteSubscriberA implements Subscriber {
    @Override
    public void update(String message) {
        System.out.println("Subscriber A received: " + message);
    }

    @Override
    public String getSubscriptionTopic() {
        return "topic1";
    }
}

public class ConcreteSubscriberB implements Subscriber {
    @Override
    public void update(String message) {
        System.out.println("Subscriber B received: " + message);
    }

    @Override
    public String getSubscriptionTopic() {
        return "topic2";
    }
}
```

### 5. Message Handlers

```java
public class LoggingMessageHandler implements MessageHandler {
    @Override
    public void handle(String message) {
        System.out.println("Logging message: " + message);
    }
}

public class NotificationMessageHandler implements MessageHandler {
    @Override
    public void handle(String message) {
        System.out.println("Sending notification: " + message);
    }
}
```

### 6. Factory Pattern for Subscribers

```java
public class SubscriberFactory {
    public static Subscriber createSubscriber(String type) {
        switch (type) {
            case "A":
                return new ConcreteSubscriberA();
            case "B":
                return new ConcreteSubscriberB();
            default:
                throw new IllegalArgumentException("Unknown subscriber type: " + type);
        }
    }
}
```

### 7. Example Usage

```java
public class PubSubExample {
    public static void main(String[] args) {
        PubSubSystem pubSubSystem = new PubSubSystem();

        // Create subscribers using the factory
        Subscriber subscriberA = SubscriberFactory.createSubscriber("A");
        Subscriber subscriberB = SubscriberFactory.createSubscriber("B");

        // Subscribe to topics
        pubSubSystem.subscribe(subscriberA);
        pubSubSystem.subscribe(subscriberB);

        // Publish messages
        pubSubSystem.publish("topic1", "Hello to Topic 1");
        pubSubSystem.publish("topic2", "Hello to Topic 2");
        
        // Unsubscribe and demonstrate
        pubSubSystem.unsubscribe(subscriberB);
        pubSubSystem.publish("topic2", "Subscriber B should not receive this!");
    }
}
```

### 8. Enhancements and Considerations

#### 8.1 Thread Safety
- Use `ConcurrentHashMap` for the `subscribersMap` to ensure thread safety.

#### 8.2 Message Filtering
- Implement filtering in the subscriber to allow for more granular control over which messages to receive.

#### 8.3 Scalability
- For larger applications, consider using message brokers like Kafka or RabbitMQ for decoupled communication.

#### 8.4 Persistence
- Store messages for offline subscribers using a database or a persistent queue.

#### 8.5 Error Handling
- Implement error handling and logging for message delivery failures.

### Conclusion
This design leverages SOLID principles, design patterns, and good software engineering practices to create a flexible, maintainable, and extensible Pub-Sub system in Java. It ensures low coupling and high cohesion, making it easier to add new features or modify existing ones without affecting other parts of the system.

The designed Pub-Sub system effectively leverages SOLID principles as follows:

### 1. Single Responsibility Principle (SRP)

Each class in the design has a distinct responsibility:

- **`Subscriber` Interface**: Defines the contract for subscribers to receive updates, ensuring that any subscriber only handles its message processing logic.
- **`Publisher` Interface**: Manages publishing messages to subscribers, isolating the publishing logic from message handling.
- **`PubSubSystem` Class**: Centralizes the subscription and publishing logic without being burdened with message processing details.
- **`MessageHandler` Interface and Implementations**: Each handler is responsible for a specific type of message handling (e.g., logging, notifications).

### 2. Open/Closed Principle (OCP)

The design allows for extension without modification:

- **New Subscribers**: Additional subscriber classes can be created (e.g., `ConcreteSubscriberC`) without changing the existing subscriber implementations or the `PubSubSystem`.
- **New Message Handlers**: You can introduce new message handlers (e.g., `EmailNotificationHandler`) without modifying the existing handler implementations or the core logic.

### 3. Liskov Substitution Principle (LSP)

The system ensures that derived classes can be used interchangeably with their base classes:

- Any implementation of the `Subscriber` interface (like `ConcreteSubscriberA` or `ConcreteSubscriberB`) can be substituted in the system without affecting its functionality. The `PubSubSystem` treats all subscribers uniformly based on their common interface.

### 4. Interface Segregation Principle (ISP)

Clients are not forced to implement interfaces they do not use:

- Subscribers implement only the `update` and `getSubscriptionTopic` methods, which are relevant to them. They are not burdened with methods they don’t need. Similarly, different message handlers can implement their specific handling logic without unnecessary methods.

### 5. Dependency Inversion Principle (DIP)

High-level modules depend on abstractions rather than concrete implementations:

- The `PubSubSystem` is not tightly coupled with specific subscriber implementations. It interacts through the `Subscriber` interface, allowing it to function independently of the actual implementations.
- The `SubscriberFactory` abstracts the creation of subscribers, allowing the system to remain decoupled from the specific subscriber types being used.

### Summary

The design of the Pub-Sub system adheres to the SOLID principles by ensuring clear responsibilities, promoting extensibility, allowing substitutability, reducing unnecessary dependencies, and adhering to interface contracts. This results in a flexible, maintainable, and scalable architecture that can evolve over time without requiring significant changes to existing code.

The design of the Pub-Sub system effectively embodies the principles of low coupling and high cohesion, leading to a robust and maintainable architecture. Here’s how each principle is leveraged:

## Low Coupling

Low coupling means that components in a system have minimal dependencies on each other, making it easier to modify or replace components without affecting the entire system.

1. **Use of Interfaces**: 
   - The system employs interfaces (`Subscriber`, `Publisher`, `MessageHandler`) to define contracts for interactions. This decouples the concrete implementations from their usage. For example, `PubSubSystem` interacts with any `Subscriber` without knowing the specifics of each subscriber's implementation.

2. **Dynamic Subscription**:
   - Subscribers can be added or removed dynamically. The `PubSubSystem` does not need to be modified to accommodate new subscriber types; it simply works with the `Subscriber` interface.

3. **Factory Pattern**:
   - The `SubscriberFactory` decouples the creation of subscriber instances from their usage in the system. This means the `PubSubSystem` does not need to know about the specific classes of subscribers being instantiated, promoting separation of concerns.

4. **Independent Message Handlers**:
   - Message handling logic is encapsulated in separate classes that implement the `MessageHandler` interface. This means that changes in message processing (like adding new handlers) do not affect the publisher or the subscribers directly.

## High Cohesion

High cohesion refers to how closely related and focused the responsibilities of a single module or class are. Classes with high cohesion have well-defined roles, making them easier to understand and maintain.

1. **Dedicated Responsibilities**:
   - Each class has a specific role: 
     - `PubSubSystem` manages subscriptions and message distribution.
     - `ConcreteSubscriberA` and `ConcreteSubscriberB` implement specific behaviors for receiving messages.
     - `MessageHandler` implementations (e.g., `LoggingMessageHandler`, `NotificationMessageHandler`) handle distinct message-processing strategies.

2. **Clear Interfaces**:
   - Interfaces are focused on specific actions, such as handling messages or publishing them. This clarity ensures that each component only exposes relevant functionality, promoting higher cohesion.

3. **Encapsulation of Logic**:
   - Each subscriber encapsulates its logic for processing messages. For example, if `ConcreteSubscriberA` needs to format messages differently, it can do so within its own class without affecting other subscribers or the `PubSubSystem`.

4. **Behavioral Grouping**:
   - Related classes and interfaces are organized around common responsibilities, like message handling. This organization helps to keep the system modular and coherent.

### Summary

In summary, the design of the Pub-Sub system achieves low coupling through the use of interfaces, dynamic subscription mechanisms, and the factory pattern, allowing components to evolve independently. It achieves high cohesion by assigning well-defined roles to each class, ensuring that related responsibilities are grouped together and that each component remains focused on a specific task. This combination leads to a flexible, maintainable, and extensible system architecture.

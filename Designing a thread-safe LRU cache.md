# Designing a thread-safe LRU cache

Designing a thread-safe LRU (Least Recently Used) cache in Java that meets the specified requirements involves a careful selection of data structures, adherence to object-oriented design principles, and ensuring efficient operations. Below is a comprehensive design along with an explanation of the relevant low-level design principles.

### High-Level Design

1. **Data Structures**:
   - Use a `HashMap` to store key-value pairs for O(1) access.
   - Use a doubly linked list to maintain the order of usage, with the head representing the most recently used item and the tail representing the least recently used item.

2. **Thread Safety**:
   - Use `ReentrantLock` or `synchronized` blocks to ensure that the cache can handle concurrent access from multiple threads safely.

3. **Capacity Management**:
   - The cache should have a fixed capacity defined during initialization, and when this capacity is exceeded, the least recently used item should be removed.

### Class Design

#### Node Class

```java
class Node {
    int key;
    int value;
    Node prev;
    Node next;

    public Node(int key, int value) {
        this.key = key;
        this.value = value;
    }
}
```

#### LRUCache Class

```java
import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.locks.ReentrantLock;

class LRUCache {
    private final int capacity;
    private final Map<Integer, Node> cache;
    private final Node head;
    private final Node tail;
    private final ReentrantLock lock;

    public LRUCache(int capacity) {
        this.capacity = capacity;
        this.cache = new HashMap<>();
        this.head = new Node(0, 0); // Dummy head
        this.tail = new Node(0, 0); // Dummy tail
        head.next = tail;
        tail.prev = head;
        this.lock = new ReentrantLock();
    }

    private void remove(Node node) {
        Node prevNode = node.prev;
        Node nextNode = node.next;
        prevNode.next = nextNode;
        nextNode.prev = prevNode;
    }

    private void addToFront(Node node) {
        node.prev = head;
        node.next = head.next;
        head.next.prev = node;
        head.next = node;
    }

    public int get(int key) {
        lock.lock();
        try {
            if (cache.containsKey(key)) {
                Node node = cache.get(key);
                remove(node);
                addToFront(node);
                return node.value;
            }
            return -1; // Key not found
        } finally {
            lock.unlock();
        }
    }

    public void put(int key, int value) {
        lock.lock();
        try {
            if (cache.containsKey(key)) {
                remove(cache.get(key));
            } else if (cache.size() >= capacity) {
                // Remove the least recently used item (tail's previous node)
                Node lruNode = tail.prev;
                remove(lruNode);
                cache.remove(lruNode.key);
            }

            Node newNode = new Node(key, value);
            cache.put(key, newNode);
            addToFront(newNode);
        } finally {
            lock.unlock();
        }
    }
}
```

### Low-Level Design Principles

1. **Single Responsibility Principle (SRP)**:
   - Each class has a clear responsibility: `Node` handles the data structure for cache entries, while `LRUCache` manages the cache's logic and operations. This promotes easier maintenance and understanding of each component.

2. **Open/Closed Principle (OCP)**:
   - The `LRUCache` class can be extended with additional features (e.g., statistics tracking, resizing) without modifying existing functionality, adhering to the principle that classes should be open for extension but closed for modification.

3. **Liskov Substitution Principle (LSP)**:
   - In our current design, there are no derived classes. However, if we decided to create subclasses of `Node` in the future, they should be usable without affecting the correctness of the cache.

4. **Interface Segregation Principle (ISP)**:
   - The design avoids large interfaces. The `LRUCache` class contains only the necessary methods (`get` and `put`), keeping the interface simple and focused on the cache's primary functionality.

5. **Dependency Inversion Principle (DIP)**:
   - The cache relies on abstractions like `Map` for storing data. If the underlying storage needs to change, we can do so with minimal impact on the high-level logic.

### Design Patterns

- **Composite Pattern**: The `Node` class forms part of a composite structure (the doubly linked list) that maintains the order of usage.
- **Locking Mechanism**: Using `ReentrantLock` for synchronization can be viewed as a behavioral design pattern, ensuring safe access in concurrent scenarios.

### Thread Safety

- The use of `ReentrantLock` ensures that multiple threads can safely interact with the cache. The locking mechanism prevents race conditions and ensures that the cache state remains consistent during `put` and `get` operations.

### Conclusion

This Java implementation of an LRU cache adheres to solid design principles, promoting maintainability, scalability, and robustness. By combining these principles with efficient data structures, we achieve a thread-safe cache that performs both `put` and `get` operations in O(1) time complexity.

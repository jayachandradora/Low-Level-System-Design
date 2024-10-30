# Designing a thread-safe LRU cache

Designing a thread-safe LRU (Least Recently Used) cache in Java that meets the specified requirements involves a careful selection of data structures, adherence to object-oriented design principles, and ensuring efficient operations. Below is a comprehensive design along with an explanation of the relevant low-level design principles.


## Requirements

- The LRU cache should support the following operations:
  - `put(key, value)`: Insert a key-value pair into the cache. If the cache is at capacity, remove the least recently used item before inserting the new item.
  - `get(key)`: Get the value associated with the given key. If the key exists in the cache, move it to the front of the cache (most recently used) and return its value. If the key does not exist, return -1.
- The cache should have a fixed capacity, specified during initialization.
- The cache should be thread-safe, allowing concurrent access from multiple threads.
- The cache should be efficient in terms of time complexity for both put and get operations, ideally O(1).
- Add TTL functionality to this cache implementation.



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
    long expiryTime; // Time when this entry should expire
    Node prev;
    Node next;

    public Node(int key, int value, long ttl) {
        this.key = key;
        this.value = value;
        this.expiryTime = System.currentTimeMillis() + ttl; // Set expiry time
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
        this.head = new Node(0, 0, 0); // Dummy head
        this.tail = new Node(0, 0, 0); // Dummy tail
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

    private boolean isExpired(Node node) {
        return System.currentTimeMillis() > node.expiryTime;
    }

    public int get(int key) {
        lock.lock();
        try {
            if (cache.containsKey(key)) {
                Node node = cache.get(key);
                if (isExpired(node)) {
                    remove(node);
                    cache.remove(key);
                    return -1; // Entry expired
                }
                remove(node);
                addToFront(node);
                return node.value;
            }
            return -1; // Key not found
        } finally {
            lock.unlock();
        }
    }

    public void put(int key, int value, long ttl) {
        lock.lock();
        try {
            if (cache.containsKey(key)) {
                remove(cache.get(key)); // Remove existing entry
            } else if (cache.size() >= capacity) {
                // Remove the least recently used item (tail's previous node)
                Node lruNode = tail.prev;
                remove(lruNode);
                cache.remove(lruNode.key);
            }

            Node newNode = new Node(key, value, ttl);
            cache.put(key, newNode);
            addToFront(newNode);
        } finally {
            lock.unlock();
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) throws InterruptedException {
        LRUCache cache = new LRUCache(2); // Capacity of 2

        cache.put(1, 100, 5000); // Key 1 with value 100 and TTL of 5000 ms
        cache.put(2, 200, 10000); // Key 2 with value 200 and TTL of 10000 ms

        System.out.println(cache.get(1)); // Returns 100

        Thread.sleep(6000); // Wait for 6 seconds, key 1 should expire

        System.out.println(cache.get(1)); // Returns -1 (expired)
        System.out.println(cache.get(2)); // Returns 200 (still valid)
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

This Java implementation of an LRU cache adheres to solid design principles, promoting maintainability, scalability, and robustness. By combining these principles with efficient data structures, we achieve a thread-safe cache that performs both `put` and `get` operations in O(1) time complexity.implementation of the LRU Cache with TTL integrates the time-to-live feature while maintaining efficient operations and thread safety. The design adheres to solid principles, ensuring that the cache remains robust, maintainable, and scalable. The addition of TTL adds significant functionality by allowing cache entries to expire automatically, which can be essential in scenarios where data validity is time-sensitive.

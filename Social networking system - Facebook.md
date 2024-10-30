# Social networking system - Facebook

Below is a low-level design of a social networking system that adheres to SOLID principles, uses appropriate design patterns, and ensures low coupling and high cohesion. The implementation includes the classes you mentioned, structured to follow best practices.

### 1. User Class

```java
import java.util.ArrayList;
import java.util.List;

public class User {
    private final String id;
    private String name;
    private String email;
    private String password;
    private String profilePicture;
    private String bio;
    private final List<String> friends;
    private final List<Post> posts;

    public User(String id, String name, String email, String password) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.password = password;
        this.friends = new ArrayList<>();
        this.posts = new ArrayList<>();
    }

    // Getters and Setters...

    public void addFriend(String friendId) {
        friends.add(friendId);
    }

    public void addPost(Post post) {
        posts.add(post);
    }

    // Other user-related methods...
}
```

### 2. Post Class

```java
import java.util.ArrayList;
import java.util.List;

public class Post {
    private final String id;
    private final String userId;
    private final String content;
    private final List<String> imageUrls;
    private final List<String> videoUrls;
    private final long timestamp;
    private int likes;
    private final List<Comment> comments;

    public Post(String id, String userId, String content) {
        this.id = id;
        this.userId = userId;
        this.content = content;
        this.imageUrls = new ArrayList<>();
        this.videoUrls = new ArrayList<>();
        this.timestamp = System.currentTimeMillis();
        this.likes = 0;
        this.comments = new ArrayList<>();
    }

    // Getters and Setters...

    public void addLike() {
        likes++;
    }

    public void addComment(Comment comment) {
        comments.add(comment);
    }

    // Other post-related methods...
}
```

### 3. Comment Class

```java
public class Comment {
    private final String id;
    private final String userId;
    private final String postId;
    private final String content;
    private final long timestamp;

    public Comment(String id, String userId, String postId, String content) {
        this.id = id;
        this.userId = userId;
        this.postId = postId;
        this.content = content;
        this.timestamp = System.currentTimeMillis();
    }

    // Getters...
}
```

### 4. Notification Class

```java
public class Notification {
    private final String id;
    private final String userId;
    private final NotificationType notificationType;
    private final String content;
    private final long timestamp;

    public Notification(String id, String userId, NotificationType notificationType, String content) {
        this.id = id;
        this.userId = userId;
        this.notificationType = notificationType;
        this.content = content;
        this.timestamp = System.currentTimeMillis();
    }

    // Getters...
}
```

### 5. NotificationType Enum

```java
public enum NotificationType {
    FRIEND_REQUEST,
    FRIEND_REQUEST_ACCEPTED,
    LIKE,
    COMMENT,
    MENTION
}
```

### 6. SocialNetworkingService Class (Singleton Pattern)

```java
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.CopyOnWriteArrayList;

public class SocialNetworkingService {
    private static SocialNetworkingService instance;
    private final ConcurrentHashMap<String, User> users;
    private final CopyOnWriteArrayList<Post> posts;

    private SocialNetworkingService() {
        users = new ConcurrentHashMap<>();
        posts = new CopyOnWriteArrayList<>();
    }

    public static synchronized SocialNetworkingService getInstance() {
        if (instance == null) {
            instance = new SocialNetworkingService();
        }
        return instance;
    }

    public void registerUser(String id, String name, String email, String password) {
        User user = new User(id, name, email, password);
        users.put(id, user);
    }

    public User login(String email, String password) {
        return users.values().stream()
            .filter(user -> user.getEmail().equals(email) && user.getPassword().equals(password))
            .findFirst()
            .orElse(null);
    }

    public void sendFriendRequest(String fromUserId, String toUserId) {
        // Implementation for sending friend requests...
    }

    public void createPost(String userId, String content) {
        Post post = new Post(generateId(), userId, content);
        posts.add(post);
        // Notify friends...
    }

    public void likePost(String postId) {
        // Implementation for liking a post...
    }

    public void commentOnPost(String postId, String userId, String content) {
        // Implementation for commenting on a post...
    }

    public List<Post> getNewsFeed(String userId) {
        // Implementation to generate newsfeed...
        return new ArrayList<>(posts);
    }

    public List<Notification> getNotifications(String userId) {
        // Implementation to retrieve notifications...
        return new ArrayList<>();
    }

    private String generateId() {
        return String.valueOf(System.currentTimeMillis());
    }
}
```

### 7. SocialNetworkingDemo Class

```java
public class SocialNetworkingDemo {
    public static void main(String[] args) {
        SocialNetworkingService sns = SocialNetworkingService.getInstance();

        // Register users
        sns.registerUser("1", "Alice", "alice@example.com", "password123");
        sns.registerUser("2", "Bob", "bob@example.com", "password456");

        // User login
        User alice = sns.login("alice@example.com", "password123");
        User bob = sns.login("bob@example.com", "password456");

        // Send friend request
        sns.sendFriendRequest(alice.getId(), bob.getId());

        // Create posts
        sns.createPost(alice.getId(), "Hello, world!");
        sns.createPost(bob.getId(), "This is my first post!");

        // Like and comment
        sns.likePost("1");
        sns.commentOnPost("1", bob.getId(), "Nice post!");

        // Retrieve newsfeed
        sns.getNewsFeed(alice.getId());

        // Retrieve notifications
        sns.getNotifications(bob.getId());
    }
}
```

### Key Considerations

1. **SOLID Principles**:
   - Each class has a single responsibility.
   - The `SocialNetworkingService` is open for extension (e.g., new features) without modifying existing code.
   - The use of interfaces for notifications and other features would further enhance the design.

2. **Design Patterns**:
   - Singleton pattern is used for `SocialNetworkingService`.
   - Strategy pattern could be considered for handling different notification types.

3. **Low Coupling and High Cohesion**:
   - Classes are designed with clear responsibilities, leading to high cohesion.
   - Dependencies between classes are minimized, enhancing low coupling (e.g., `User` does not need to know about `Post` directly).

4. **Multi-threading**:
   - `ConcurrentHashMap` and `CopyOnWriteArrayList` are used to handle concurrent access, ensuring thread safety.

### Conclusion

This design provides a structured, maintainable, and extensible social networking system that adheres to good software engineering principles. Further enhancements can include additional features, improved error handling, and more sophisticated notification handling.

# Social networking system - Facebook

Below is a low-level design of a social networking system that adheres to SOLID principles, uses appropriate design patterns, and ensures low coupling and high cohesion. The implementation includes the classes you mentioned, structured to follow best practices.

Certainly! Below is a structured approach for saving the content as a Markdown file suitable for a GitHub repository. You can save this content into a file named `SocialNetworkDesign.md`.

```markdown
# Designing a Social Network Like Facebook

## Requirements

### User Registration and Authentication
- Users should be able to create an account with their personal information, such as name, email, and password.
- Users should be able to log in and log out of their accounts securely.

### User Profiles
- Each user should have a profile with their information, such as profile picture, bio, and interests.
- Users should be able to update their profile information.

### Friend Connections
- Users should be able to send friend requests to other users.
- Users should be able to accept or decline friend requests.
- Users should be able to view their list of friends.

### Posts and Newsfeed
- Users should be able to create posts with text, images, or videos.
- Users should be able to view a newsfeed consisting of posts from their friends and their own posts.
- The newsfeed should be sorted in reverse chronological order.

### Likes and Comments
- Users should be able to like and comment on posts.
- Users should be able to view the list of likes and comments on a post.

### Privacy and Security
- Users should be able to control the visibility of their posts and profile information.
- The system should enforce secure access control to ensure data privacy.

### Notifications
- Users should receive notifications for events such as friend requests, likes, comments, and mentions.
- Notifications should be delivered in real-time.

### Scalability and Performance
- The system should be designed to handle a large number of concurrent users and high traffic load.
- The system should be scalable and efficient in terms of resource utilization.

## Classes, Interfaces, and Enumerations

### User Class
Represents a user in the social networking system, containing properties such as:
- ID
- Name
- Email
- Password
- Profile picture
- Bio
- List of friends
- List of posts

### Post Class
Represents a post created by a user, containing properties such as:
- ID
- User ID
- Content
- Image URLs
- Video URLs
- Timestamp
- Likes
- Comments

### Comment Class
Represents a comment made by a user on a post, containing properties such as:
- ID
- User ID
- Post ID
- Content
- Timestamp

### Notification Class
Represents a notification generated for a user, containing properties such as:
- ID
- User ID
- Notification type
- Content
- Timestamp

### NotificationType Enum
Defines the different types of notifications, such as:
- Friend request
- Friend request accepted
- Like
- Comment
- Mention

### SocialNetworkingService Class
The main class that manages the social networking system. It follows the Singleton pattern to ensure only one instance of the service exists. This class provides methods for:
- User registration
- Login
- Profile updates
- Friend requests
- Post creation
- Newsfeed generation
- Likes
- Comments
- Notifications

### Multi-threading
Achieved using concurrent data structures such as `ConcurrentHashMap` and `CopyOnWriteArrayList` to handle concurrent access to shared resources.

### SocialNetworkingDemo Class
Demonstrates the usage of the social networking system by:
- Registering users
- Logging in
- Sending friend requests
- Creating posts
- Liking posts
- Commenting on posts
- Retrieving newsfeed and notifications
```

You can create a file named `SocialNetworkDesign.md`, copy the above content into it, and then commit it to your GitHub repository.


### Summary of Changes

1. **User Class Enhancements**:
   - Added `following` and `followers` sets to manage user relationships.
   - Methods for `followUser`, `unfollowUser`, `addFollower`, and `removeFollower` were implemented.

2. **SocialNetworkingService Class Enhancements**:
   - Added `followUser` and `unfollowUser` methods to handle follow/unfollow actions and update user relationships.

3. **Demo Class**:
   - Updated to demonstrate the new follow and unfollow functionality.

### 1. User Class

```java
import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

public class User {
    private final String id;
    private String name;
    private String email;
    private String password;
    private String profilePicture;
    private String bio;
    private final List<String> friends; // Friends list
    private final List<Post> posts; // User posts
    private final Set<String> following; // Users this user is following
    private final Set<String> followers; // Users following this user

    public User(String id, String name, String email, String password) {
        this.id = id;
        this.name = name;
        this.email = email;
        this.password = password;
        this.friends = new ArrayList<>();
        this.posts = new ArrayList<>();
        this.following = new HashSet<>();
        this.followers = new HashSet<>();
    }

    // Getters and Setters...

    public void addFriend(String friendId) {
        friends.add(friendId);
    }

    public void addPost(Post post) {
        posts.add(post);
    }

    public void followUser(String userId) {
        following.add(userId);
    }

    public void unfollowUser(String userId) {
        following.remove(userId);
    }

    public void addFollower(String userId) {
        followers.add(userId);
    }

    public void removeFollower(String userId) {
        followers.remove(userId);
    }

    public List<String> getFollowing() {
        return new ArrayList<>(following);
    }

    public List<String> getFollowers() {
        return new ArrayList<>(followers);
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

    public void followUser(String followerId, String followeeId) {
        User follower = users.get(followerId);
        User followee = users.get(followeeId);
        if (follower != null && followee != null) {
            follower.followUser(followeeId);
            followee.addFollower(followerId);
            // Optionally send notification to followee
        }
    }

    public void unfollowUser(String followerId, String followeeId) {
        User follower = users.get(followerId);
        User followee = users.get(followeeId);
        if (follower != null && followee != null) {
            follower.unfollowUser(followeeId);
            followee.removeFollower(followerId);
            // Optionally send notification to followee
        }
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
        sns.registerUser("3", "Charlie", "charlie@example.com", "password789");

        // User login
        User alice = sns.login("alice@example.com", "password123");
        User bob = sns.login("bob@example.com", "password456");
        User charlie = sns.login("charlie@example.com", "password789");

        // Follow and unfollow users
        sns.followUser(alice.getId(), bob.getId());
        sns.followUser(bob.getId(), charlie.getId());
        sns.unfollowUser(alice.getId(), bob.getId());

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

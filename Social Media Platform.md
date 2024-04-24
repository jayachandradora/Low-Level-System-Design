# Social Media Platform

Designing a Social Media Platform involves managing various components such as users, posts, comments, likes, and the system itself to facilitate interactions between users. Here's a low-level design example in Java:

## Components:

**1.  User:** Represents a user of the platform with details like user ID, name, contact information, etc.<br />
**2.  Post:** Represents a post created by a user with details like post ID, content, timestamp, etc.<br />
**3.  Comment:** Represents a comment made on a post with details like comment ID, content, timestamp, etc.<br />
**4.  Like:** Represents a like given by a user on a post or comment.<br />
**5.  SocialMediaPlatform:** Manages users, posts, comments, likes, and facilitates interactions between users.<br />

## Interactions:

**1. User creates a post:**

*  User creates a new post with content.
*  SocialMediaPlatform adds the post to the user's profile and displays it to followers.
  
**2.  User comments on a post:**
*  User writes a comment on a post.
*  SocialMediaPlatform adds the comment to the post and notifies the post owner and other users.
  
**3.  User likes a post or comment:**
*  User likes a post or comment.
*  SocialMediaPlatform records the like and updates the like count.


<details>
  <summary> Click for Endity and Models</summary>

  ```ruby

@Entity
public class User {
    @Id
    private String userId;
    private String name;
    private String contactInfo;

    @OneToMany(mappedBy = "author")
    private List<Post> posts;

    @ManyToMany(mappedBy = "followers")
    private List<User> followers;

    // Constructors, getters, and setters
}

@Entity
public class Post {
    @Id
    private String postId;
    private String content;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User author;

    private Date timestamp;

    @OneToMany(mappedBy = "post")
    private List<Comment> comments;

    private int likeCount;
    // Constructors, getters, and setters
}


@Entity
public class Comment {
    @Id
    private String commentId;
    private String content;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User author;

    @ManyToOne
    @JoinColumn(name = "post_id")
    private Post post;

    private Date timestamp;
    // Constructors, getters, and setters
}

@Entity
public class Like {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID likeId;

    @ManyToOne
    @JoinColumn(name = "user_id")
    private User user;

    @ManyToOne
    @JoinColumn(name = "post_id")
    private Post post;

    @ManyToOne
    @JoinColumn(name = "comment_id")
    private Comment comment;

    // Constructors, getters, and setters
}
  ```
</details>
<details>
  <summary>Expand for Scripts</summary>
  
```ruby
 In the Like entity:
* We added annotations to establish many-to-one relationships with User, Post, and Comment entities.
* Each relationship is mapped by a foreign key column in the likes table (user_id, post_id, comment_id).

Scripts

CREATE TABLE users (
    user_id VARCHAR(255) PRIMARY KEY,
    name VARCHAR(255),
    contact_info VARCHAR(255)
);

In the User entity, the mappedBy = "author" attribute in the @OneToMany annotation indicates that the User entity is the inverse side of the relationship with the Post entity. It correctly maps the posts field in the User entity to the author field in the Post entity.

CREATE TABLE posts (
    post_id VARCHAR(255) PRIMARY KEY,
    content TEXT,
    user_id VARCHAR(255),
    timestamp TIMESTAMP,
    like_count INT,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

In the Post entity, the mappedBy = "post" attribute in the @OneToMany annotation indicates that the Post entity is the inverse side of the relationship with the Comment entity. It correctly maps the comments field in the Post entity to the post field in the Comment entity.

CREATE TABLE comments (
    comment_id VARCHAR(255) PRIMARY KEY,
    content TEXT,
    user_id VARCHAR(255),
    post_id VARCHAR(255),
    timestamp TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (post_id) REFERENCES posts(post_id)
); 
In the Comment entity, the mappedBy attribute is not needed because it does not have a bidirectional relationship with any other entity. Instead, it defines two @ManyToOne relationships with the User and Post entities, mapping the author and post fields respectively.

CREATE TABLE likes (
    like_id UUID PRIMARY KEY,
    user_id VARCHAR(255),
    post_id VARCHAR(255),
    comment_id VARCHAR(255),
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (post_id) REFERENCES posts(post_id),
    FOREIGN KEY (comment_id) REFERENCES comments(comment_id)
);

In the Like entity, the mappedBy attribute is not used because it does not have a bidirectional relationship with any other entity. Instead, it defines three @ManyToOne relationships with the User, Post, and Comment entities, mapping the user, post, and comment fields respectively.

CREATE TABLE followers (
    user_id VARCHAR(255),
    follower_id VARCHAR(255),
    PRIMARY KEY (user_id, follower_id),
    FOREIGN KEY (user_id) REFERENCES users(user_id),
    FOREIGN KEY (follower_id) REFERENCES users(user_id)
);

CREATE TABLE user_posts (
    post_id VARCHAR(255) PRIMARY KEY,
    user_id VARCHAR(255) REFERENCES users(user_id)
);

1. Followers table will represent the many-to-many relationship between users and their followers.

In this script:
* The followers table represents the many-to-many relationship between users and their followers.
* It has two columns: user_id and follower_id, both of which are foreign keys referencing the user_id column in the users table.
* The combination of user_id and follower_id is the primary key to ensure that a user cannot have duplicate followers.

2. User-Posts Relationship table will represent the one-to-many relationship between users and their posts.

In this script:
* The user_posts table represents the one-to-many relationship between users and their posts.
* It has two columns: post_id and user_id, where user_id is a foreign key referencing the user_id column in the users table.

```
</details>


<details>
  <summary>Expand Service Class and Business Logic </summary>
  
```ruby
import java.util.*;

class SocialMediaPlatform {
    private Map<String, User> users;
    private Map<String, Post> posts;

    public SocialMediaPlatform() {
        this.users = new HashMap<>();
        this.posts = new HashMap<>();
    }

    public void registerUser(User user) {
        users.put(user.getUserId(), user);
    }

    public void createPost(String userId, String content) {
        User user = users.get(userId);
        if (user != null) {
            Post post = new Post(UUID.randomUUID().toString(), content, user, new Date(), new ArrayList<>(), 0);
            user.getPosts().add(post);
            posts.put(post.getPostId(), post);
        }
    }

    public void addComment(String postId, Comment comment) {
        Post post = posts.get(postId);
        if (post != null) {
            post.getComments().add(comment);
        }
    }

    public void likePost(String postId, User user) {
        Post post = posts.get(postId);
        if (post != null) {
            Like like = new Like(UUID.randomUUID().toString(), user, post, null);
            post.setLikeCount(post.getLikeCount() + 1);
        }
    }

    public void likeComment(String commentId, User user) {
        // Similar to likePost method, but for comments
    }

    // Other methods for user-specific operations, post-specific operations, etc.
}
```
</details>

<details>
  <summary> Click for Repository Information</summary>
  ```ruby
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, String> {
}

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface PostRepository extends JpaRepository<Post, String> {
}

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface CommentRepository extends JpaRepository<Comment, String> {
}

import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface LikeRepository extends JpaRepository<Comment, String> {
}
  ```
</details>
In this design:

The **User** class represents a user of the platform with details like user ID, name, contact information, etc. <br />
The **Post** class represents a post created by a user with details like post ID, content, author, timestamp, etc. <br />
The **Comment** class represents a comment made on a post with details like comment ID, content, author, timestamp, etc.<br />
The **Like** class represents a like given by a user on a post or comment. <br />
The **SocialMediaPlatform** class manages users, posts, comments, likes, and facilitates interactions between users.<br />

This is a basic example. In a real-world scenario, you would need to consider additional features like authentication, authorization, encryption for sensitive information, search and filtering options, notifications, and more. Additionally, error handling, concurrency control, and database integration would be crucial for a production-level system.




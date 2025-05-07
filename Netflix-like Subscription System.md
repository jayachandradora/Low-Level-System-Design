# Netflix-like Subscription System

Here’s a **Low-Level System Design (LLD)** and **Database Design** for a simplified **Netflix-like subscription system** with these features:

---

### ✅ Functional Requirements

1. **Buy Subscription** (New or Renew)
2. **Search Subscription**
3. **View Subscription**
4. Auto-renewal Monthly
5. Different Subscription Types:

   * Family Pack
   * Child Subscription
6. Monthly plan with yearly auto-renewal
7. Payment via Auto Deduction

---

## 📦 Class Design (Java – OOP Level)

```java
enum SubscriptionType {
    FAMILY, CHILD
}

enum PaymentStatus {
    PENDING, SUCCESS, FAILED
}

class User {
    private String userId;
    private String name;
    private String email;
    private List<Subscription> subscriptions;
}

class Subscription {
    private String subscriptionId;
    private SubscriptionType type;
    private LocalDate startDate;
    private LocalDate endDate;
    private boolean autoRenewal;
    private Payment payment;

    public void renew() {
        // Logic for renewing subscription
    }

    public boolean isActive() {
        return LocalDate.now().isBefore(endDate);
    }
}

class Payment {
    private String paymentId;
    private double amount;
    private LocalDate paymentDate;
    private PaymentStatus status;
    private boolean autoDeduction;
}
```

---

## 🗃️ Database Design (Relational Schema)

### Tables

1. **Users**

```sql
CREATE TABLE users (
    user_id VARCHAR(36) PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

2. **Subscriptions**

```sql
CREATE TABLE subscriptions (
    subscription_id VARCHAR(36) PRIMARY KEY,
    user_id VARCHAR(36),
    type ENUM('FAMILY', 'CHILD'),
    start_date DATE,
    end_date DATE,
    auto_renewal BOOLEAN,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

3. **Payments**

```sql
CREATE TABLE payments (
    payment_id VARCHAR(36) PRIMARY KEY,
    subscription_id VARCHAR(36),
    amount DECIMAL(10, 2),
    payment_date DATE,
    status ENUM('PENDING', 'SUCCESS', 'FAILED'),
    auto_deduction BOOLEAN,
    FOREIGN KEY (subscription_id) REFERENCES subscriptions(subscription_id)
);
```

---

## 🔄 Auto-Renewal (Cron or Scheduler Logic)

* Run daily to check if `subscription.end_date = CURRENT_DATE`
* If `auto_renewal = true`, generate new payment and extend subscription by one month.

---

## 🔍 Operations

1. **Buy/Renew Subscription**

```java
public Subscription buySubscription(User user, SubscriptionType type, boolean autoRenewal, boolean autoDeduct) {
    Subscription subscription = new Subscription();
    subscription.setStartDate(LocalDate.now());
    subscription.setEndDate(LocalDate.now().plusMonths(1));
    subscription.setAutoRenewal(autoRenewal);
    subscription.setType(type);

    Payment payment = new Payment();
    payment.setAmount(getPrice(type));
    payment.setAutoDeduction(autoDeduct);
    payment.setPaymentDate(LocalDate.now());
    payment.setStatus(PaymentStatus.SUCCESS);

    subscription.setPayment(payment);
    // save to DB
    return subscription;
}
```

2. **Search Subscription by User ID or Subscription ID**

```sql
SELECT * FROM subscriptions WHERE user_id = 'xyz';
```

3. **View Subscription**

```sql
SELECT * FROM subscriptions s 
JOIN payments p ON s.subscription_id = p.subscription_id 
WHERE s.subscription_id = 'abc';
```

---

Would you like a **UML diagram** or **API design** as well?

# DB Design 

Designing a **Netflix Subscription database** involves creating a schema that handles:

* Users
* Subscriptions
* Plans
* Content (Movies/Series)
* Viewing History
* Devices
* Payments
* Profiles (within an account)
* Genres / Categories
* Ratings & Reviews

Here's a relational database design with more entities and clear relationships.

---

### 🔷 Entity Relationship Model (Key Entities)

#### 1. **User**

* `user_id` (PK)
* `email`
* `password_hash`
* `name`
* `date_joined`
* `is_active`

#### 2. **Profile**

* `profile_id` (PK)
* `user_id` (FK → User)
* `profile_name`
* `age_restriction`
* `language`

#### 3. **Subscription**

* `subscription_id` (PK)
* `user_id` (FK → User)
* `plan_id` (FK → Plan)
* `start_date`
* `end_date`
* `status` (active, cancelled, paused)

#### 4. **Plan**

* `plan_id` (PK)
* `name` (Basic, Standard, Premium)
* `price`
* `max_profiles`
* `max_devices`
* `resolution` (SD, HD, UHD)

#### 5. **Payment**

* `payment_id` (PK)
* `subscription_id` (FK → Subscription)
* `payment_date`
* `amount`
* `payment_method` (credit card, PayPal, etc.)
* `status`

#### 6. **Device**

* `device_id` (PK)
* `user_id` (FK → User)
* `device_type` (TV, phone, tablet, etc.)
* `device_name`
* `last_used`

#### 7. **Content**

* `content_id` (PK)
* `title`
* `type` (Movie, Series)
* `release_year`
* `description`
* `duration`
* `age_rating`

#### 8. **Episode**

* `episode_id` (PK)
* `content_id` (FK → Content)
* `season_number`
* `episode_number`
* `title`
* `duration`

#### 9. **Genre**

* `genre_id` (PK)
* `name`

#### 10. **ContentGenre**

* `content_id` (FK → Content)
* `genre_id` (FK → Genre)

#### 11. **ViewingHistory**

* `history_id` (PK)
* `profile_id` (FK → Profile)
* `content_id` (FK → Content)
* `watched_on`
* `progress` (in minutes or percentage)

#### 12. **Rating**

* `rating_id` (PK)
* `profile_id` (FK → Profile)
* `content_id` (FK → Content)
* `rating` (e.g., 1–5)
* `review_text`
* `timestamp`

---

### 🔁 Relationships

* One **User** can have many **Profiles** and **Devices**
* One **User** has one active **Subscription**
* Each **Subscription** belongs to one **Plan**
* One **Profile** watches many **Content** items (via **ViewingHistory**)
* Each **Content** item can have multiple **Genres**
* Each **Profile** can rate multiple **Content** items

---

Would you like this schema in a diagram or SQL table creation format?


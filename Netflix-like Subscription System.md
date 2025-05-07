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

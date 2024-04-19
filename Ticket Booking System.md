# Ticket Booking System

Designing a Ticket Booking System involves managing various components such as users, events, tickets, bookings, and the system itself to facilitate ticket reservations and management. Here's a low-level design example in Java:

## Components:

**1.  User:** Represents a user of the system with details like user ID, name, contact information, etc. <br />
**2.  Event:** Represents an event (e.g., concert, movie, sports game) with details like event ID, name, date, venue, etc. <br />
**3.  Ticket:** Represents a ticket for an event with details like ticket ID, event, seat number, price, etc. <br />
**4.  Booking:** Represents a booking made by a user for a ticket with details like booking ID, user, event, ticket, etc. <br />
**5.  BookingSystem:** Manages users, events, tickets, bookings, and facilitates ticket reservations and management. <br />

## Interactions:

**1.  User searches for events:**
*  User searches for events based on criteria like date, venue, category, etc.
*  BookingSystem retrieves and displays available events.

**2.  User selects an event and books tickets:**
*  User selects an event and the number of tickets to book.
*  BookingSystem checks ticket availability, reserves the tickets, and creates a booking record.

**3.  BookingSystem manages bookings and tickets:**
*  Manages the list of registered users and available events.
*  Handles ticket availability, reservation, and cancellation.
*  Records booking details and updates ticket availability.

```ruby
import java.util.*;

class User {
    private String userId;
    private String name;
    private String contactInfo;

    // Constructor, getters and setters
}

class Event {
    private String eventId;
    private String name;
    private Date date;
    private String venue;
    private int totalSeats;
    private int availableSeats;

    // Constructor, getters and setters
}

class Ticket {
    private String ticketId;
    private Event event;
    private int seatNumber;
    private double price;

    // Constructor, getters and setters
}

class Booking {
    private String bookingId;
    private User user;
    private Event event;
    private Ticket ticket;
    private Date bookingDate;

    // Constructor, getters and setters
}

class BookingSystem {
    private List<User> users;
    private List<Event> events;
    private List<Ticket> tickets;
    private List<Booking> bookings;

    public BookingSystem() {
        this.users = new ArrayList<>();
        this.events = new ArrayList<>();
        this.tickets = new ArrayList<>();
        this.bookings = new ArrayList<>();
    }

    public void registerUser(User user) {
        users.add(user);
    }

    public void addEvent(Event event) {
        events.add(event);
    }

    public List<Event> searchEvents(Date startDate, Date endDate, String venue) {
        List<Event> result = new ArrayList<>();
        for (Event event : events) {
            if (event.getDate().after(startDate) && event.getDate().before(endDate) && event.getVenue().equals(venue)) {
                result.add(event);
            }
        }
        return result;
    }

    public boolean bookTickets(User user, Event event, int numTickets) {
        // Check ticket availability
        if (event.getAvailableSeats() >= numTickets) {
            List<Ticket> eventTickets = getAvailableTickets(event, numTickets);
            if (eventTickets.size() == numTickets) {
                // Reserve tickets
                for (Ticket ticket : eventTickets) {
                    ticket.setAvailable(false);
                    event.setAvailableSeats(event.getAvailableSeats() - 1);
                    bookings.add(new Booking(UUID.randomUUID().toString(), user, event, ticket, new Date()));
                }
                return true;
            }
        }
        return false;
    }

    private List<Ticket> getAvailableTickets(Event event, int numTickets) {
        List<Ticket> availableTickets = new ArrayList<>();
        for (Ticket ticket : tickets) {
            if (ticket.getEvent().equals(event) && ticket.isAvailable()) {
                availableTickets.add(ticket);
                if (availableTickets.size() == numTickets) {
                    break;
                }
            }
        }
        return availableTickets;
    }

    // Other methods for canceling bookings, generating reports, etc.
}
```
In this design:

The **User** class represents a user of the system with details like user ID, name, contact information, etc. <br />
The **Event** class represents an event with details like event ID, name, date, venue, total seats, available seats, etc.  <br />
The **Ticket** class represents a ticket for an event with details like ticket ID, event, seat number, price, availability, etc.  <br />
The **Booking** class represents a booking made by a user for a ticket with details like booking ID, user, event, ticket, booking date, etc. <br />
The **BookingSystem** class manages users, events, tickets, bookings, and facilitates ticket reservations and management. <br />

This is a basic example. In a real-world scenario, you would need to consider additional features like authentication, authorization, payment processing, seat selection, notifications, and more. Additionally, error handling, concurrency control, and database integration would be crucial for a production-level system.

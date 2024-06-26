# Flight Booking System 

Designing a Flight Booking System involves several components like flights, passengers, bookings, and the system itself to manage these entities. Here's a low-level design example in Java:

## Components:

**Flight:** Represents a flight with details like flight number, origin, destination, departure time, etc. <br />
**Passenger:** Represents a passenger with details like name, age, contact information, etc. <br />
**Booking:** Represents a booking made by a passenger for a particular flight. <br />
**FlightManager:** Manages the flights available, bookings made, and facilitates booking operations.<br />

## Interactions:

Passenger books a flight.<br />
Passenger selects a flight and provides necessary details.<br />
FlightManager checks availability and creates a booking if seats are available.<br />
Booking confirmation is sent to the passenger.<br />
FlightManager manages flights and bookings:<br />
Manages the list of available flights.<br />
Manages the list of bookings made for each flight.<br />
Handles booking operations like adding, cancelling, or modifying bookings.<br />

```ruby
import java.util.*;

class Flight {
    private String flightNumber;
    private String origin;
    private String destination;
    private Date departureTime;
    private int totalSeats;
    private int availableSeats;

    // Constructor, getters and setters
}

class Passenger {
    private String name;
    private int age;
    private String contactInfo;

    // Constructor, getters and setters
}

class Booking {
    private String bookingId;
    private Flight flight;
    private Passenger passenger;

    // Constructor, getters and setters
}

class FlightManager {
    private List<Flight> flights;
    private Map<Flight, List<Booking>> bookings;

    public FlightManager() {
        this.flights = new ArrayList<>();
        this.bookings = new HashMap<>();
    }

    public void addFlight(Flight flight) {
        flights.add(flight);
        bookings.put(flight, new ArrayList<>());
    }

    public String bookFlight(Flight flight, Passenger passenger) {
        if (!flights.contains(flight)) {
            return "Flight not found.";
        }
        if (flight.getAvailableSeats() <= 0) {
            return "No available seats on this flight.";
        }

        String bookingId = generateBookingId();
        Booking booking = new Booking(bookingId, flight, passenger);
        bookings.get(flight).add(booking);
        flight.setAvailableSeats(flight.getAvailableSeats() - 1);

        return "Booking successful. Your booking ID is " + bookingId;
    }

    private String generateBookingId() {
        // Generate unique booking ID logic
    }

    // Other methods like cancelBooking, getBookingsByFlight, etc.
}

```
In this design:

*     The Flight class represents a flight with details like flight number, origin, destination, etc.
*     The Passenger class represents a passenger with details like name, age, contact information, etc.
*     The Booking class represents a booking made by a passenger for a particular flight.
*     The FlightManager class manages flights available and bookings made.
*     It provides methods to add flights, book flights, cancel bookings, and retrieve bookings.

This is a basic example. In a real-world scenario, you would need to consider additional features like payment processing, seat selection, authentication, and more. Additionally, error handling,
concurrency control, and database integration would be crucial for a production-level system.





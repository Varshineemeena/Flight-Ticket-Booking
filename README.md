package FlightTicket;

import java.util.*;

class Passenger {
    private String seatNumber;
    private boolean withMeal;

    public Passenger(String seatNumber, boolean withMeal) {
        this.seatNumber = seatNumber;
        this.withMeal = withMeal;
    }

    public String getSeatNumber() {
        return seatNumber;
    }

    public boolean isWithMeal() {
        return withMeal;
    }
}

class Flight {
    private String flightNumber;
    private int[][] businessSeating;
    private int[][] economySeating;
    private int businessPrice;
    private int economyPrice;
    private int mealPrice;
    private int bookingCount;
    private List<Passenger> passengers;

    public Flight(String flightNumber, int[][] businessSeating, int[][] economySeating, int businessPrice, int economyPrice) {
        this.flightNumber = flightNumber;
        this.businessSeating = businessSeating;
        this.economySeating = economySeating;
        this.businessPrice = businessPrice;
        this.economyPrice = economyPrice;
        this.mealPrice = 200;
        this.bookingCount = 0;
        this.passengers = new ArrayList<>();
    }

    public String getFlightNumber() {
        return flightNumber;
    }

    public int bookSeats(int numSeats, String seatClass, boolean withMeal) {
        int totalPrice = 0;
        if (seatClass.equals("Business")) {
            totalPrice = bookSeatsInClass(businessSeating, businessPrice, numSeats);
        } else if (seatClass.equals("Economy")) {
            totalPrice = bookSeatsInClass(economySeating, economyPrice, numSeats);
        } else {
            System.out.println("Invalid seat class.");
        }

        if (totalPrice > 0) {
            if (withMeal) {
                totalPrice += numSeats * mealPrice;
            }
            bookingCount++;
            updatePrices();
            return totalPrice;
        } else {
            return 0;
        }
    }

    private int bookSeatsInClass(int[][] seating, int price, int numSeats) {
        int availableSeats = 0;
        for (int[] row : seating) {
            for (int seat : row) {
                if (seat == 0) {
                    availableSeats++;
                }
            }
        }

        if (availableSeats >= numSeats) {
            int totalPrice = price * numSeats;
            for (int[] row : seating) {
                for (int i = 0; i < row.length && numSeats > 0; i++) {
                    if (row[i] == 0) {
                        row[i] = 1; // Mark seat as booked
                        numSeats--;
                        String seatNumber = (seating == businessSeating) ? ("B" + (row.length * row.length - numSeats)) : ("E" + (row.length * row.length - numSeats));
                        boolean withMeal = false;
                        passengers.add(new Passenger(seatNumber, withMeal));
                    }
                }
            }
            return totalPrice;
        } else {
            System.out.println("Not enough available seats.");
            return 0;
        }
    }

    public void cancelBooking(int bookingId) {
        if (bookingCount > 0) {
            bookingCount--;
            updatePrices();
            for (Passenger passenger : passengers) {
                if (passenger.getSeatNumber().startsWith(String.valueOf(bookingId))) {
                    String seatNumber = passenger.getSeatNumber();
                    int row = Integer.parseInt(seatNumber.substring(1, 2));
                    int col = Integer.parseInt(seatNumber.substring(2, 3));
                    int[][] seating = (seatNumber.startsWith("B")) ? businessSeating : economySeating;
                    seating[row - 1][col - 1] = 0; // Mark seat as available
                    passengers.remove(passenger);
                    break;
                }
            }
        } else {
            System.out.println("No bookings to cancel.");
        }
    }

    public void printAvailableSeats() {
        System.out.println("Available seats for Flight " + flightNumber + ":");
        int numRows = businessSeating.length;
        int numCols = businessSeating[0].length;

        System.out.println("Business Class:");
        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j < numCols; j++) {
                if (businessSeating[i][j] == 0) {
                    System.out.print("B" + (i + 1) + (j + 1) + " ");
                }
            }
            System.out.println();
        }

        numRows = economySeating.length;
        numCols = economySeating[0].length;

        System.out.println("Economy Class:");
        for (int i = 0; i < numRows; i++) {
            for (int j = 0; j < numCols; j++) {
                if (economySeating[i][j] == 0) {
                    System.out.print("E" + (i + 1) + (j + 1) + " ");
                }
            }
            System.out.println();
        }
    }

    public void printSeatsWithMeal() {
        System.out.println("Seats with meal for Flight " + flightNumber + ":");
        for (Passenger passenger : passengers) {
            if (passenger.isWithMeal()) {
                System.out.print(passenger.getSeatNumber() + " ");
            }
        }
        System.out.println();
    }

    public void printBookingSummary() {
        System.out.println("Booking Summary for Flight " + flightNumber + ":");
        for (Passenger passenger : passengers) {
            System.out.println("Booking ID: " + passenger.getSeatNumber().charAt(0));
            System.out.println("Seat Number: " + passenger.getSeatNumber().substring(1));
            System.out.println("With Meal: " + passenger.isWithMeal());
            System.out.println();
        }
    }

    private void updatePrices() {
        businessPrice += 200;
        economyPrice += 100;
    }

    public String getBusinessSeating() {
        // TODO Auto-generated method stub
        return null;
    }

    public String getEconomySeating() {
        // TODO Auto-generated method stub
        return null;
    }
}

public class TicketBookingSystem {
    private static int bookingId = 1;
    private static Map<String, Flight> flights = new HashMap<>();

    public static void main(String[] args) {
        initializeFlights(); // Add flight details here

        try (Scanner scanner = new Scanner(System.in)) {
            while (true) {
                System.out.println("Choose an option:");
                System.out.println("1. List flight details");
                System.out.println("2. Book a ticket");
                System.out.println("3. Cancel booking");
                System.out.println("4. Search flights");
                System.out.println("5. Print available seats");
                System.out.println("6. Print seats with meal");
                System.out.println("7. Print booking summary");
                System.out.println("8. Exit");

                int option = scanner.nextInt();

                switch (option) {
                    case 1:
                        listFlightDetails();
                        break;

                    case 2:
                        bookTicket(scanner);
                        break;

                    case 3:
                        cancelBooking(scanner);
                        break;

                    case 4:
                        searchFlights(scanner);
                        break;

                    case 5:
                        printAvailableSeats(scanner);
                        break;

                    case 6:
                        printSeatsWithMeal(scanner);
                        break;

                    case 7:
                        printBookingSummary(scanner);
                        break;

                    case 8:
                        System.exit(0);

                    default:
                        System.out.println("Invalid option.");
                        break;
                }
            }
        }
    }

    private static void initializeFlights() {
        // Add flight details here
        int[][] businessSeatingA112 = new int[][]{{0, 0, 0}, {0, 0, 0}, {0, 0, 0}};
        int[][] economySeatingA112 = new int[][]{{0, 0, 0}, {0, 0, 0}, {0, 0, 0}};
        int businessPriceA112 = 2000;
        int economyPriceA112 = 1000;

        int[][] businessSeatingA113 = new int[][]{{0, 0, 0}, {0, 0, 0}, {0, 0, 0}};
        int[][] economySeatingA113 = new int[][]{{0, 0, 0}, {0, 0, 0}, {0, 0, 0}};
        int businessPriceA113 = 2000;
        int economyPriceA113 = 1000;

        int[][] businessSeatingA114 = new int[][]{{0, 0, 0}, {0, 0, 0}, {0, 0, 0}};
        int[][] economySeatingA114 = new int[][]{{0, 0, 0}, {0, 0, 0}, {0, 0, 0}};
        int businessPriceA114 = 2000;
        int economyPriceA114 = 1000;
        
        Flight flightA112 = new Flight("Flight-A112-Chennai-Mumbai", businessSeatingA112, economySeatingA112, businessPriceA112, economyPriceA112);
        Flight flightA113 = new Flight("Flight-A113-Chennai-Kolkata", businessSeatingA113, economySeatingA113, businessPriceA113, economyPriceA113);

        Flight flightA114 = new Flight("Flight-A114-Chennai-Delhi", businessSeatingA114, economySeatingA114, businessPriceA114, economyPriceA114);
        flights.put("A112", flightA112);
        flights.put("A113", flightA113);
        flights.put("A114", flightA114);
    }

    private static void listFlightDetails() {
        System.out.println("List of Flights:");
        for (Flight flight : flights.values()) {
            System.out.println(flight.getFlightNumber());
        }
    }

    private static void bookTicket(Scanner scanner) {
        System.out.println("Enter flight number: ");
        String flightNumber = scanner.next();

        if (!flights.containsKey(flightNumber)) {
            System.out.println("Flight not found.");
            return;
        }

        Flight flight = flights.get(flightNumber);

        System.out.println("Enter number of seats: ");
        int numSeats = scanner.nextInt();

        System.out.println("Enter seat class (Business/Economy): ");
        String seatClass = scanner.next();

        System.out.println("Add meal (yes/no): ");
        String addMeal = scanner.next();
        boolean withMeal = addMeal.equalsIgnoreCase("yes");

        int totalPrice = flight.bookSeats(numSeats, seatClass, withMeal);
        if (totalPrice > 0) {
            System.out.println("Booking ID: " + bookingId++);
            System.out.println("Total Price: INR " + totalPrice);
        }
    }

    private static void cancelBooking(Scanner scanner) {
        System.out.println("Enter flight number: ");
        String flightNumber = scanner.next();

        if (!flights.containsKey(flightNumber)) {
            System.out.println("Flight not found.");
            return;
        }

        Flight flight = flights.get(flightNumber);

        System.out.println("Enter Booking ID: ");
        int bookingId = scanner.nextInt();

        flight.cancelBooking(bookingId);
        System.out.println("Booking canceled.");
    }

    private static void searchFlights(Scanner scanner) {
        System.out.println("Search flights:");
        System.out.println("1. Filter flights by source and destination");
        System.out.println("2. Filter flights with business class only");
        int searchOption = scanner.nextInt();

        if (searchOption == 1) {
            System.out.println("Enter source location: ");
            String source = scanner.next();
            System.out.println("Enter destination location: ");
            String destination = scanner.next();

            for (Flight flight : flights.values()) {
                if (flight.getFlightNumber().contains(source) && flight.getFlightNumber().contains(destination)) {
                    System.out.println("Flight " + flight.getFlightNumber() + " matches.");
                }
            }
        } else if (searchOption == 2) {
            for (Flight flight : flights.values()) {
                if (flight.getBusinessSeating().length() > 0 && flight.getEconomySeating().length() == 0) {
                    System.out.println("Flight " + flight.getFlightNumber() + " has business class only.");
                }
            }
        } else {
            System.out.println("Invalid search option.");
        }
    }

    private static void printAvailableSeats(Scanner scanner) {
        System.out.println("Enter flight number: ");
        String flightNumber = scanner.next();

        if (!flights.containsKey(flightNumber)) {
            System.out.println("Flight not found.");
            return;
        }

        Flight flight = flights.get(flightNumber);
        flight.printAvailableSeats();
    }

    private static void printSeatsWithMeal(Scanner scanner) {
        System.out.println("Enter flight number: ");
        String flightNumber = scanner.next();

        if (!flights.containsKey(flightNumber)) {
            System.out.println("Flight not found.");
            return;
        }

        Flight flight = flights.get(flightNumber);
        flight.printSeatsWithMeal();
    }

    private static void printBookingSummary(Scanner scanner) {
        System.out.println("Enter flight number: ");
        String flightNumber = scanner.next();

        if (!flights.containsKey(flightNumber)) {
            System.out.println("Flight not found.");
            return;
        }

        Flight flight = flights.get(flightNumber);
        flight.printBookingSummary();
    }
}

<!-- ABOUT THE PROJECT -->
## Hotel Reservation System

### Design Overview:
The system will include the following components:
- **Guest**: Represents the customer making the reservation.
- **Room**: Represents the hotel room with details like room type, price, and availability.
- **Reservation**: Represents a reservation made by a guest for a room.
- **Hotel**: Manages rooms and reservations, providing functionality like booking, searching, and cancellations.
- **Payment**: Represents the payment for a reservation.

```ruby

require 'date'

# Class representing a Guest (Customer)
class Guest
  attr_accessor :name, :email, :phone, :reservations

  def initialize(name, email, phone)
    @name = name
    @email = email
    @phone = phone
    @reservations = []
  end

  def to_s
    "#{name} (#{email}, #{phone})"
  end
end

# Class representing a Room in the hotel
class Room
  attr_accessor :room_number, :room_type, :price_per_night, :status

  def initialize(room_number, room_type, price_per_night)
    @room_number = room_number
    @room_type = room_type
    @price_per_night = price_per_night
    @status = :available  # Room status can be available, reserved, or occupied
  end

  def to_s
    "#{room_type} (Room number: #{@room_number}) - $#{price_per_night} per night"
  end
end

# Class representing a Reservation
class Reservation
  attr_accessor :guest, :room, :check_in_date, :check_out_date, :total_price, :status

  def initialize(guest, room, check_in_date, check_out_date)
    @guest = guest
    @room = room
    @check_in_date = check_in_date
    @check_out_date = check_out_date
    @status = :reserved
    @total_price = calculate_total_price
  end

  # Calculate the total price based on number of nights
  def calculate_total_price
    (check_out_date - check_in_date).to_i * room.price_per_night
  end

  def to_s
    "#{guest.name} reserved Room #{room.room_number} from #{check_in_date} to #{check_out_date} for $#{total_price}."
  end
end

# Class representing the Payment for a Reservation
class Payment
  attr_accessor :reservation, :amount, :status

  def initialize(reservation, amount)
    @reservation = reservation
    @amount = amount
    @status = :pending
  end

  def process_payment
    @status = :completed
    puts "Payment of $#{amount} for reservation #{reservation.room.room_number} completed successfully."
  end

  def to_s
    "Payment for Reservation #{reservation.room.room_number}: $#{amount} (Status: #{status})"
  end
end

# Class representing the Hotel
class Hotel
  attr_accessor :rooms, :guests, :reservations

  def initialize
    @rooms = []
    @guests = []
    @reservations = []
  end

  # Add a room to the hotel
  def add_room(room_number, room_type, price_per_night)
    room = Room.new(room_number, room_type, price_per_night)
    @rooms << room
    puts "Room #{room_number} added to the hotel."
  end

  # Register a guest to the hotel system
  def register_guest(name, email, phone)
    guest = Guest.new(name, email, phone)
    @guests << guest
    guest
  end

  # Search for available rooms within a date range
  def search_available_rooms(check_in_date, check_out_date)
    available_rooms = @rooms.select do |room|
      room.status == :available && available_for_dates?(room, check_in_date, check_out_date)
    end
    available_rooms
  end

  # Check if a room is available for the given dates
  def available_for_dates?(room, check_in_date, check_out_date)
    @reservations.none? do |reservation|
      reservation.room == room && (check_in_date < reservation.check_out_date && check_out_date > reservation.check_in_date)
    end
  end

  # Make a reservation
  def make_reservation(guest, room, check_in_date, check_out_date)
    if room.status == :available
      reservation = Reservation.new(guest, room, check_in_date, check_out_date)
      @reservations << reservation
      room.status = :reserved
      guest.reservations << reservation
      puts "Reservation created for #{guest.name} in Room #{room.room_number}."
      reservation
    else
      puts "Room #{room.room_number} is not available."
      nil
    end
  end

  # Cancel a reservation
  def cancel_reservation(reservation)
    if reservation.status == :reserved
      reservation.room.status = :available
      reservation.status = :cancelled
      puts "Reservation for Room #{reservation.room.room_number} cancelled."
    else
      puts "Cannot cancel reservation. It is already #{reservation.status}."
    end
  end

  # Check-in a guest
  def check_in(reservation)
    if reservation.status == :reserved
      reservation.room.status = :occupied
      reservation.status = :checked_in
      puts "#{reservation.guest.name} checked into Room #{reservation.room.room_number}."
    else
      puts "Reservation cannot be checked in. Status: #{reservation.status}."
    end
  end

  # Check-out a guest
  def check_out(reservation)
    if reservation.status == :checked_in
      reservation.room.status = :available
      reservation.status = :checked_out
      puts "#{reservation.guest.name} checked out from Room #{reservation.room.room_number}."
    else
      puts "Reservation cannot be checked out. Status: #{reservation.status}."
    end
  end

  # Process payment for a reservation
  def process_payment(reservation, amount)
    payment = Payment.new(reservation, amount)
    payment.process_payment
  end
end

```

```ruby

# Initialize the hotel system
hotel = Hotel.new

# Add rooms to the hotel
hotel.add_room(101, "Single", 100)
hotel.add_room(102, "Double", 150)
hotel.add_room(103, "Suite", 300)

# Register guests
guest1 = hotel.register_guest("John Doe", "john.doe@example.com", "123-456-7890")
guest2 = hotel.register_guest("Jane Smith", "jane.smith@example.com", "987-654-3210")

# Search for available rooms
available_rooms = hotel.search_available_rooms(Date.new(2025, 4, 1), Date.new(2025, 4, 5))
puts "Available rooms: "
available_rooms.each { |room| puts room }

# Make a reservation for Guest1
room_for_reservation = available_rooms.first  # Choose the first available room
reservation1 = hotel.make_reservation(guest1, room_for_reservation, Date.new(2025, 4, 1), Date.new(2025, 4, 5))

# Make a payment for the reservation
hotel.process_payment(reservation1, reservation1.total_price)

# Check-in and Check-out process
hotel.check_in(reservation1)
hotel.check_out(reservation1)

# Try to cancel a reservation
hotel.cancel_reservation(reservation1)  # Cannot cancel after check-in

```
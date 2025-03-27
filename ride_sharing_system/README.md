<!-- ABOUT THE PROJECT -->
## Ride-Sharing System

Designing a **Ride-Sharing System** (similar to Uber or Lyft) involves managing drivers, riders, trips, and payments. The system should allow riders to request rides, drivers to accept trips, and the system to calculate the cost of trips, including distance, time, and any other surcharges.

### Design Overview:
- **User** (Rider, Driver): Represents a user in the system. Riders request rides, and drivers fulfill them.
- **Ride Request**: Represents a ride request made by a rider.
- **Driver**: Represents a driver who accepts ride requests.
- **Ride**: Represents a completed trip between a rider and a driver.
- **Payment**: Handles the payment process for a completed ride.
- **RideSharingSystem**: The controller that manages all users, rides, and interactions.


```ruby

# Class representing a User (either Rider or Driver)
class User
  attr_accessor :id, :name, :email, :location, :rating, :trip_history

  def initialize(id, name, email, location)
    @id = id
    @name = name
    @email = email
    @location = location
    @rating = 0.0
    @trip_history = []
  end

  def update_location(new_location)
    @location = new_location
  end
end

# Class representing a Rider, which is a type of User
class Rider < User
  def initialize(id, name, email, location)
    super(id, name, email, location)
  end
end

# Class representing a Driver, which is a type of User
class Driver < User
  attr_accessor :is_available

  def initialize(id, name, email, location)
    super(id, name, email, location)
    @is_available = true
  end

  def start_trip
    @is_available = false
  end

  def end_trip
    @is_available = true
  end
end

# Class representing a Ride Request made by a Rider
class RideRequest
  attr_accessor :rider, :pickup_location, :dropoff_location, :status

  def initialize(rider, pickup_location, dropoff_location)
    @rider = rider
    @pickup_location = pickup_location
    @dropoff_location = dropoff_location
    @status = :pending  # Status can be :pending, :matched, :in_progress, :completed, :cancelled
  end
end

# Class representing a Ride (Trip) between a Rider and a Driver
class Ride
  attr_accessor :rider, :driver, :distance, :duration, :fare, :status

  def initialize(rider, driver, distance, duration)
    @rider = rider
    @driver = driver
    @distance = distance
    @duration = duration
    @fare = calculate_fare
    @status = :in_progress
  end

  def calculate_fare
    # A basic fare calculation based on distance and duration
    base_fare = 5 # Base fare in dollars
    per_mile = 2 # Cost per mile
    per_minute = 0.5 # Cost per minute

    base_fare + (per_mile * @distance) + (per_minute * @duration)
  end

  def complete_ride
    @status = :completed
    @driver.end_trip
    @rider.trip_history << self
    @driver.trip_history << self
  end
end

# Class responsible for managing the ride-sharing system
class RideSharingSystem
  attr_accessor :users, :drivers, :riders, :ride_requests, :rides

  def initialize
    @users = []
    @drivers = []
    @riders = []
    @ride_requests = []
    @rides = []
  end

  def add_user(user)
    @users << user
    if user.is_a?(Driver)
      @drivers << user
    elsif user.is_a?(Rider)
      @riders << user
    end
  end

  def request_ride(rider, pickup_location, dropoff_location)
    ride_request = RideRequest.new(rider, pickup_location, dropoff_location)
    @ride_requests << ride_request
    match_ride_request(ride_request)
  end

  def match_ride_request(ride_request)
    # Find the nearest available driver to the rider's location
    available_driver = find_available_driver(ride_request)
    if available_driver
      ride_request.status = :matched
      available_driver.start_trip
      ride = Ride.new(ride_request.rider, available_driver, calculate_distance(ride_request), calculate_duration(ride_request))
      @rides << ride
      ride.complete_ride
      puts "Ride completed between #{ride_request.rider.name} and #{available_driver.name}."
    else
      puts "No available drivers at the moment."
    end
  end

  def find_available_driver(ride_request)
    available_driver = @drivers.select { |driver| driver.is_available }.min_by { |driver| distance_between(driver.location, ride_request.pickup_location) }
    available_driver
  end

  def calculate_distance(ride_request)
    # For simplicity, we'll simulate a basic distance calculation between pickup and dropoff locations
    (ride_request.pickup_location - ride_request.dropoff_location).abs
  end

  def calculate_duration(ride_request)
    # Simulate a duration based on the distance (assuming 1 mile per minute)
    calculate_distance(ride_request)
  end

  def process_payment(ride)
    # A simple payment process: charge the rider the fare and pay the driver
    puts "Charging rider #{ride.rider.name} $#{ride.fare}."
    puts "Paying driver #{ride.driver.name} $#{ride.fare * 0.8}." # Assume driver gets 80% of the fare
  end
end

```

```ruby

# Initialize the ride-sharing system
ride_sharing_system = RideSharingSystem.new

# Add some drivers and riders to the system
driver1 = Driver.new(1, "Alice", "alice@example.com", 5)
driver2 = Driver.new(2, "Bob", "bob@example.com", 10)
rider1 = Rider.new(1, "Charlie", "charlie@example.com", 3)

ride_sharing_system.add_user(driver1)
ride_sharing_system.add_user(driver2)
ride_sharing_system.add_user(rider1)

# Rider requests a ride
ride_sharing_system.request_ride(rider1, 3, 8)

# The system will find the nearest available driver, calculate the distance, calculate the fare, and complete the ride

```
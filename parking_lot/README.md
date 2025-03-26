<!-- ABOUT THE PROJECT -->
## Parking Lot System

The basic components in the parking lot system would be:
- **Parking Lot:** The main system managing parking spots.
- **Parking Spot:** A specific spot where a car can be parked.
- **Vehicle:** The car that will be parked in the lot.
- **Parking Ticket (optional):** An object that represents a parking ticket issued when a vehicle parks.
- **Motorcycles** can park in any spot.
- **Cars** can park in **compact** spots or **regular** spots.
- **Vans** will take up 3 regular spots at once.

```ruby

# Vehicle class representing a generic vehicle
class Vehicle
  attr_reader :license_plate, :type

  def initialize(license_plate, type)
    @license_plate = license_plate
    @type = type
  end
end

# Motorcycle class representing a motorcycle
class Motorcycle < Vehicle
  def initialize(license_plate)
    super(license_plate, :motorcycle)
  end
end

# Car class representing a car
class Car < Vehicle
  def initialize(license_plate)
    super(license_plate, :car)
  end
end

# Van class representing a van
class Van < Vehicle
  def initialize(license_plate)
    super(license_plate, :van)
  end
end

# ParkingSpot class representing a parking space in the parking lot
class ParkingSpot
  attr_reader :spot_number, :spot_type, :vehicle

  def initialize(spot_number, spot_type)
    @spot_number = spot_number
    @spot_type = spot_type # :compact, :regular, :large
    @vehicle = nil
  end

  def park(vehicle)
    if @vehicle.nil? && can_park?(vehicle)
      @vehicle = vehicle
      puts "#{vehicle.type.capitalize} with license plate #{vehicle.license_plate} parked at spot #{@spot_number}."
    else
      puts "Cannot park vehicle with license plate #{vehicle.license_plate} at spot #{@spot_number}."
    end
  end

  def unpark
    if @vehicle
      puts "#{@vehicle.type.capitalize} with license plate #{@vehicle.license_plate} is leaving spot #{@spot_number}."
      @vehicle = nil
    else
      puts "Spot #{@spot_number} is already empty."
    end
  end

  def is_empty?
    @vehicle.nil?
  end

  private

  # Check if the vehicle can park in this spot
  def can_park?(vehicle)
    case vehicle.type
    when :motorcycle
      true # Motorcycles can park anywhere
    when :car
      # Cars can park in compact or regular spots
      [:compact, :regular].include?(@spot_type)
    when :van
      # Vans need 3 regular spots together
      @spot_type == :regular
    else
      false
    end
  end
end

# ParkingLot class that manages parking spots and vehicles
class ParkingLot
  attr_reader :spots, :capacity

  def initialize(capacity)
    @capacity = capacity
    @spots = []
    (1..capacity).each do |spot_number|
      # Assume alternating pattern of spot types for simplicity
      spot_type = case spot_number % 3
                  when 0 then :large
                  when 1 then :compact
                  else :regular
                  end
      @spots << ParkingSpot.new(spot_number, spot_type)
    end
  end

  def park_vehicle(vehicle)
    if vehicle.is_a?(Van)
      park_van(vehicle)
    else
      available_spot = @spots.find { |spot| spot.is_empty? && spot.can_park?(vehicle) }
      if available_spot
        available_spot.park(vehicle)
      else
        puts "Sorry, no available spots for vehicle with license plate #{vehicle.license_plate}."
      end
    end
  end

  def unpark_vehicle(vehicle)
    parked_spot = @spots.find { |spot| !spot.is_empty? && spot.vehicle == vehicle }
    if parked_spot
      parked_spot.unpark
    else
      puts "Vehicle #{vehicle.license_plate} is not in the parking lot."
    end
  end

  def park_van(van)
    # A van takes 3 consecutive regular spots
    available_spots = @spots.each_cons(3).find do |spot1, spot2, spot3|
      spot1.is_empty? && spot2.is_empty? && spot3.is_empty? &&
        spot1.spot_type == :regular && spot2.spot_type == :regular && spot3.spot_type == :regular
    end

    if available_spots
      available_spots.each { |spot| spot.park(van) }
    else
      puts "Sorry, no available spots for van with license plate #{van.license_plate}."
    end
  end

  def display_occupied_spots
    occupied = @spots.select { |spot| !spot.is_empty? }
    if occupied.empty?
      puts "No vehicles are currently parked."
    else
      occupied.each do |spot|
        puts "Spot #{spot.spot_number} (#{spot.spot_type}) is occupied by vehicle #{spot.vehicle.license_plate}."
      end
    end
  end
end

```

## Example Usage:

```ruby

# Create vehicles
motorcycle = Motorcycle.new("MOTO123")
car = Car.new("CAR456")
van = Van.new("VAN789")

# Create a parking lot with 10 spots
lot = ParkingLot.new(10)

# Park vehicles
lot.park_vehicle(motorcycle)  # Motorcycle can park in any available spot
lot.park_vehicle(car)         # Car can park in compact or regular spots
lot.park_vehicle(van)         # Van needs 3 consecutive regular spots

# Display current occupied spots
lot.display_occupied_spots

# Unpark a vehicle
lot.unpark_vehicle(car)  # Unpark the car

# Display occupied spots after unparking
lot.display_occupied_spots

```
<!-- ABOUT THE PROJECT -->
## Car Rental System

-**Car**: Represents a car in the rental fleet.
-**Customer**: Represents a customer who can rent cars.
-**RentalService**: Manages the car rental operations like renting out and returning cars.

## Key Requirements:

- Customers can rent available cars.
- A car can be rented only if it is available.
- The system should handle the status of each car (rented or available).
- Customers can return cars.
- A customer should be able to rent multiple cars (if desired).
- The rental service can track the total number of cars rented by a customer.

```ruby

# Car class
class Car
  attr_reader :model, :license_plate
  attr_accessor :available

  def initialize(model, license_plate)
    @model = model
    @license_plate = license_plate
    @available = true  # Initially, all cars are available.
  end
end

# Customer class
class Customer
  attr_reader :name, :rented_cars

  def initialize(name)
    @name = name
    @rented_cars = []
  end

  def rent_car(car)
    if car.available
      @rented_cars << car
      car.available = false
      puts "#{@name} rented the car: '#{car.model}' (License Plate: #{car.license_plate})"
    else
      puts "Sorry, the car '#{car.model}' is already rented."
    end
  end

  def return_car(car)
    if @rented_cars.include?(car)
      @rented_cars.delete(car)
      car.available = true
      puts "#{@name} returned the car: '#{car.model}' (License Plate: #{car.license_plate})"
    else
      puts "#{@name} cannot return the car '#{car.model}' because it was not rented."
    end
  end
end

# RentalService class
class RentalService
  attr_reader :cars, :customers

  def initialize
    @cars = []
    @customers = []
  end

  def add_car(car)
    @cars << car
    puts "Added car '#{car.model}' (License Plate: #{car.license_plate}) to the fleet."
  end

  def register_customer(customer)
    @customers << customer
    puts "Registered customer '#{customer.name}'."
  end

  def available_cars
    @cars.select { |car| car.available }
  end

  def rent_car_to_customer(customer, car)
    if available_cars.include?(car)
      customer.rent_car(car)
    else
      puts "Sorry, the car '#{car.model}' is not available."
    end
  end

  def return_car_from_customer(customer, car)
    customer.return_car(car)
  end
end

```

## Example Usage:

```ruby

# Create some cars
car1 = Car.new("Toyota Corolla", "ABC123")
car2 = Car.new("Honda Civic", "XYZ789")
car3 = Car.new("Ford Mustang", "LMN456")

# Create a rental service and add cars to the fleet
rental_service = RentalService.new
rental_service.add_car(car1)
rental_service.add_car(car2)
rental_service.add_car(car3)

# Create a customer and register them
customer = Customer.new("John Doe")
rental_service.register_customer(customer)

# Rent a car
rental_service.rent_car_to_customer(customer, car1)  # Should succeed
rental_service.rent_car_to_customer(customer, car2)  # Should succeed
rental_service.rent_car_to_customer(customer, car1)  # Should fail (already rented)

# Return a car
rental_service.return_car_from_customer(customer, car1)  # Should succeed
rental_service.return_car_from_customer(customer, car3)  # Should fail (was not rented)

# Output expected:
# Added car 'Toyota Corolla' (License Plate: ABC123) to the fleet.
# Added car 'Honda Civic' (License Plate: XYZ789) to the fleet.
# Added car 'Ford Mustang' (License Plate: LMN456) to the fleet.
# Registered customer 'John Doe'.
# John Doe rented the car: 'Toyota Corolla' (License Plate: ABC123)
# John Doe rented the car: 'Honda Civic' (License Plate: XYZ789)
# Sorry, the car 'Toyota Corolla' is already rented.
# John Doe returned the car: 'Toyota Corolla' (License Plate: ABC123)
# John Doe cannot return the car 'Ford Mustang' because it was not rented.

```
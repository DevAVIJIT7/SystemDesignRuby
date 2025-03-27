<!-- ABOUT THE PROJECT -->
## Elevator System

Designing an **Elevator System** requires handling multiple floors, multiple elevators, and managing requests to call the elevator. The system should ensure that elevators serve the floors efficiently, with a priority on minimizing wait times for users. We'll need to consider different types of requests (e.g., going up or down, floors that need to be served), the state of the elevators (e.g., idle, moving, or in use), and the control mechanisms for handling requests.

### Design Overview:
The system will include the following components:
- **Elevator**: Represents an elevator with attributes such as its current position, direction of travel, and request queue.
- **Floor**: Represents a floor with a request queue for up and down requests.
- **ElevatorController**: Manages the operation of multiple elevators, receiving and dispatching floor requests to the nearest elevator.
- **Request**: Represents a request from a floor for an elevator (either up or down).
- **Building**: Represents the building and houses the elevators and floors.

```ruby

# Class representing a request for the elevator
class Request
  attr_reader :floor, :direction

  def initialize(floor, direction)
    @floor = floor
    @direction = direction # :up or :down
  end
end

# Class representing a single Elevator
class Elevator
  attr_accessor :current_floor, :direction, :requests

  def initialize
    @current_floor = 0  # Assume the elevator starts at ground floor
    @direction = nil     # Elevator is initially idle
    @requests = []       # A list of requests for floors to visit
  end

  def add_request(request)
    @requests << request
    puts "Request added for floor #{request.floor} going #{request.direction}"
  end

  def move
    return if @requests.empty?

    # Sort requests by floor number (for simplicity, not handling priority)
    @requests.sort_by! { |request| request.floor }
    
    # Process all requests in the order of floor numbers
    @requests.each do |request|
      puts "Elevator moving to floor #{request.floor} (#{request.direction})"
      @current_floor = request.floor
    end

    # After serving all requests, reset the requests list
    @requests.clear
  end

  def is_idle?
    @requests.empty?
  end
end

# Class representing a single Floor
class Floor
  attr_reader :floor_number

  def initialize(floor_number)
    @floor_number = floor_number
    @up_requests = []   # Requests to go up from this floor
    @down_requests = [] # Requests to go down from this floor
  end

  def request_elevator(direction)
    request = Request.new(@floor_number, direction)
    if direction == :up
      @up_requests << request
    elsif direction == :down
      @down_requests << request
    end
    request
  end

  def get_requests(direction)
    direction == :up ? @up_requests : @down_requests
  end
end

# Class responsible for managing all elevators
class ElevatorController
  attr_reader :elevators, :floors

  def initialize(num_floors, num_elevators)
    @elevators = Array.new(num_elevators) { Elevator.new }
    @floors = Array.new(num_floors) { |i| Floor.new(i) }
  end

  # Dispatches a request to the nearest available elevator
  def dispatch_elevator(floor_number, direction)
    floor = @floors[floor_number]
    request = floor.request_elevator(direction)

    # Find the nearest idle elevator or elevator going in the same direction
    available_elevator = find_nearest_elevator(floor_number, direction)
    available_elevator.add_request(request)
    available_elevator.move
  end

  # Find the nearest elevator to the floor with respect to direction
  def find_nearest_elevator(floor_number, direction)
    idle_elevators = @elevators.select(&:is_idle?)

    if idle_elevators.any?
      # Choose the idle elevator closest to the requested floor
      return idle_elevators.min_by { |elevator| (elevator.current_floor - floor_number).abs }
    else
      # If no idle elevator, choose the one that's closest to the requested floor
      return @elevators.min_by { |elevator| (elevator.current_floor - floor_number).abs }
    end
  end

  # Simulate the system running
  def simulate
    loop do
      # Check if any elevator is idle and if there are pending requests
      @elevators.each do |elevator|
        elevator.move
      end

      break if @elevators.all?(&:is_idle?)
    end
  end
end

```

```ruby

# Initialize the elevator controller with 10 floors and 3 elevators
elevator_system = ElevatorController.new(10, 3)

# Request elevators for floors 2 and 4
elevator_system.dispatch_elevator(2, :up)  # Request for going up from floor 2
elevator_system.dispatch_elevator(4, :down) # Request for going down from floor 4

# Request elevator from floor 5 to go up
elevator_system.dispatch_elevator(5, :up)

# Simulate the system and process the requests
elevator_system.simulate

```
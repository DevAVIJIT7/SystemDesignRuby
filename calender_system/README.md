## Calender Event System

We'll implement the following components:
- **Event**: Represents an event with a name, start time, and end time.
- **Calendar**: Manages a list of events and provides methods to add, remove, and view events.
- **DateHelper**: A utility class to help with date-related calculations.

```ruby

require 'date'

# Event class
class Event
  attr_accessor :name, :start_time, :end_time, :recurrence

  def initialize(name, start_time, end_time, recurrence = nil)
    @name = name
    @start_time = start_time
    @end_time = end_time
    @recurrence = recurrence
  end

  def duration
    (@end_time - @start_time).to_i / 60  # Returns the duration in minutes
  end
end

# Calendar class
class Calendar
  attr_accessor :events

  def initialize
    @events = []
  end

  def add_event(event)
    @events << event
    puts "Event '#{event.name}' added."
  end

  def remove_event(event_name)
    @events.delete_if { |event| event.name == event_name }
    puts "Event '#{event_name}' removed."
  end

  def update_event(event_name, new_name, new_start_time, new_end_time)
    event = @events.find { |e| e.name == event_name }
    if event
      event.name = new_name
      event.start_time = new_start_time
      event.end_time = new_end_time
      puts "Event '#{event_name}' updated."
    else
      puts "Event '#{event_name}' not found."
    end
  end

  def view_events_for_day(date)
    events_for_day = @events.select do |event|
      event.start_time.to_date == date
    end
    display_events(events_for_day)
  end

  def view_events_for_week(week_start_date)
    week_end_date = week_start_date + 6
    events_for_week = @events.select do |event|
      event.start_time.to_date.between?(week_start_date, week_end_date)
    end
    display_events(events_for_week)
  end

  def view_events_for_month(month, year)
    events_for_month = @events.select do |event|
      event.start_time.month == month && event.start_time.year == year
    end
    display_events(events_for_month)
  end

  def display_events(events)
    if events.empty?
      puts "No events found."
    else
      events.each do |event|
        puts "#{event.name} - From: #{event.start_time} To: #{event.end_time}"
      end
    end
  end

  def add_recurring_event(name, start_time, end_time, recurrence, number_of_occurrences)
    number_of_occurrences.times do |i|
      # Adjust the start and end time based on the recurrence (e.g., weekly, daily)
      new_start_time = start_time + (i * recurrence)
      new_end_time = end_time + (i * recurrence)
      add_event(Event.new(name, new_start_time, new_end_time))
    end
  end
end

```

## Example usage:

```ruby

# Create a calendar
calendar = Calendar.new

# Create some events
event1 = Event.new("Meeting with Bob", DateTime.new(2025, 3, 27, 10, 0, 0), DateTime.new(2025, 3, 27, 11, 0, 0))
event2 = Event.new("Lunch with Alice", DateTime.new(2025, 3, 27, 12, 0, 0), DateTime.new(2025, 3, 27, 13, 0, 0))

# Add events to the calendar
calendar.add_event(event1)
calendar.add_event(event2)

# View events for a specific day
puts "Events for 2025-03-27:"
calendar.view_events_for_day(Date.new(2025, 3, 27))

# Update an event
calendar.update_event("Lunch with Alice", "Lunch with Alice (Updated)", DateTime.new(2025, 3, 27, 12, 30, 0), DateTime.new(2025, 3, 27, 13, 30, 0))

# Remove an event
calendar.remove_event("Meeting with Bob")

# View events for the month (March 2025)
puts "\nEvents for March 2025:"
calendar.view_events_for_month(3, 2025)

# Add a recurring event (e.g., every Monday for 4 weeks)
calendar.add_recurring_event("Weekly Team Sync", DateTime.new(2025, 3, 30, 9, 0, 0), DateTime.new(2025, 3, 30, 10, 0, 0), 7, 4)  # Every 7 days (weekly)

# View events for a week
puts "\nEvents for the week starting 2025-03-30:"
calendar.view_events_for_week(Date.new(2025, 3, 30))

```


<!-- ABOUT THE PROJECT -->
## Online Auction System

### Design Overview:
The system will include the following components:
- **User**: Represents a user in the system (either a buyer or a seller).
- **Item**: Represents the item being auctioned.
- **Bid**: Represents a bid placed by a user.
- **Auction**: Represents an auction for an item.
- **AuctionSystem**: Manages users, auctions, bids, and the auction lifecycle.

```ruby
require 'time'
require 'securerandom'

# Class representing a Bid
class Bid
  attr_reader :user, :amount, :timestamp

  def initialize(user, amount)
    @user = user
    @amount = amount
    @timestamp = Time.now
  end

  def to_s
    "#{@user.username} bid #{@amount} at #{@timestamp.strftime('%Y-%m-%d %H:%M:%S')}"
  end
end

# Class representing a User
class User
  attr_accessor :username, :email, :bids

  def initialize(username, email)
    @username = username
    @email = email
    @bids = []  # Bids placed by the user
  end

  # Place a bid on an auction
  def place_bid(auction, amount)
    if auction.closed?
      puts "Error: Auction is closed."
      return
    end

    if amount <= auction.highest_bid&.amount.to_f
      puts "Error: Bid must be higher than the current highest bid."
      return
    end

    bid = Bid.new(self, amount)
    auction.place_bid(bid)
    @bids << bid
    puts "#{@username} placed a bid of #{amount} on item #{auction.item.name}."
  end
end

# Class representing an Item for Auction
class Item
  attr_accessor :name, :description

  def initialize(name, description)
    @name = name
    @description = description
  end
end

# Class representing an Auction
class Auction
  attr_accessor :item, :starting_price, :end_time, :bids, :status, :seller

  def initialize(item, starting_price, seller, duration_minutes = 60)
    @item = item
    @starting_price = starting_price
    @seller = seller
    @end_time = Time.now + duration_minutes * 60
    @bids = []
    @status = :active
  end

  # Place a bid on the auction
  def place_bid(bid)
    @bids << bid
  end

  # Get the highest bid in the auction
  def highest_bid
    @bids.max_by(&:amount)
  end

  # Close the auction and determine the winner
  def close_auction
    if Time.now < @end_time
      puts "Error: Auction has not yet ended."
      return
    end

    @status = :closed
    if highest_bid
      puts "Auction for #{item.name} is closed."
      puts "The winning bid is #{highest_bid.amount} by #{highest_bid.user.username}."
    else
      puts "Auction for #{item.name} ended with no bids."
    end
  end

  # Check if the auction is closed
  def closed?
    @status == :closed
  end
end

# Class to manage the Online Auction System
class AuctionSystem
  def initialize
    @users = []  # List of registered users
    @auctions = []  # List of active auctions
  end

  # Register a new user
  def register_user(username, email)
    user = User.new(username, email)
    @users << user
    puts "#{username} has been successfully registered."
    user
  end

  # Create a new auction
  def create_auction(item_name, item_description, starting_price, seller_username, duration_minutes = 60)
    seller = find_user(seller_username)
    return unless seller

    item = Item.new(item_name, item_description)
    auction = Auction.new(item, starting_price, seller, duration_minutes)
    @auctions << auction
    puts "Auction for #{item_name} created by #{seller.username}. Starting price: #{starting_price}."
    auction
  end

  # Find a user by username
  def find_user(username)
    @users.find { |user| user.username == username }
  end

  # Get all active auctions
  def active_auctions
    @auctions.select { |auction| auction.status == :active }
  end

  # Close an auction by item name
  def close_auction(item_name)
    auction = @auctions.find { |a| a.item.name == item_name && a.status == :active }
    if auction
      auction.close_auction
    else
      puts "Auction not found or already closed."
    end
  end
end

```

```ruby

# Initialize the auction system
auction_system = AuctionSystem.new

# Register users
john = auction_system.register_user("JohnDoe", "johndoe@example.com")
jane = auction_system.register_user("JaneDoe", "janedoe@example.com")
alice = auction_system.register_user("Alice", "alice@example.com")

# Create an auction for an item
auction = auction_system.create_auction("Vintage Watch", "A rare, vintage watch in great condition.", 100, "JohnDoe", 30)

# Users place bids on the auction
john.place_bid(auction, 120)
jane.place_bid(auction, 150)
alice.place_bid(auction, 180)

# Close the auction
auction_system.close_auction("Vintage Watch")

# Try placing a bid after the auction is closed
john.place_bid(auction, 200)  # This should fail because the auction is closed

```
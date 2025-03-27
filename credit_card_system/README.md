<!-- ABOUT THE PROJECT -->
## Credit Card Payment System

### Design Overview:
The system will include the following components:
- **CreditCard**: Represents a credit card with details such as card number, expiration date, credit limit, and balance.
- **PaymentProcessor**: Handles processing payments, verifying credit card details, and making payments.
- **Transaction**: Represents a transaction made using the credit card.
- **CreditCardAccount**: Represents the account to which the credit card is linked (user account).
- **CreditCardPaymentSystem**: Orchestrates the overall operations and manages users, cards, and transactions.

```ruby

require 'securerandom'

# Class representing a Transaction
class Transaction
  attr_reader :amount, :timestamp, :type

  def initialize(amount, type)
    @amount = amount
    @type = type
    @timestamp = Time.now
  end

  def to_s
    "#{@type} of #{@amount} at #{@timestamp.strftime('%Y-%m-%d %H:%M:%S')}"
  end
end

# Class representing a Credit Card
class CreditCard
  attr_accessor :card_number, :expiration_date, :credit_limit, :balance, :transactions

  def initialize
    @card_number = generate_card_number
    @expiration_date = generate_expiration_date
    @credit_limit = 5000  # Default credit limit
    @balance = 0  # Start with a balance of 0
    @transactions = []  # Array to store transactions
  end

  # Method to generate a valid credit card number (dummy generation)
  def generate_card_number
    SecureRandom.random_number(10**16).to_s.rjust(16, '0')  # Generates a 16-digit number
  end

  # Method to generate a random expiration date (2 years from now)
  def generate_expiration_date
    expiration_month = (1..12).to_a.sample
    expiration_year = (Time.now.year + 2)
    "#{expiration_month.to_s.rjust(2, '0')}/#{expiration_year}"
  end

  # Method to make a payment on the card
  def make_payment(amount)
    if amount <= 0
      puts "Error: Payment amount must be positive."
      return
    end

    if @balance + amount > @credit_limit
      puts "Error: Payment exceeds available credit limit."
      return
    end

    @balance += amount
    @transactions << Transaction.new(amount, 'Payment')
    puts "Payment of #{amount} successful. New balance: #{@balance}"
  end

  # Method to charge a payment on the card
  def charge_payment(amount)
    if amount <= 0
      puts "Error: Charge amount must be positive."
      return
    end

    if @balance - amount < 0
      puts "Error: Insufficient funds on the card."
      return
    end

    @balance -= amount
    @transactions << Transaction.new(amount, 'Charge')
    puts "Charge of #{amount} successful. New balance: #{@balance}"
  end

  # Method to check the available balance
  def available_balance
    @credit_limit - @balance
  end
end

# Class representing a user's Credit Card Account
class CreditCardAccount
  attr_reader :name, :credit_card

  def initialize(name)
    @name = name
    @credit_card = CreditCard.new
  end

  # Method to check the balance on the credit card
  def check_balance
    puts "Available balance on the card: #{credit_card.available_balance}"
  end

  # Method to view transaction history
  def view_transaction_history
    if credit_card.transactions.empty?
      puts "No transactions available."
    else
      credit_card.transactions.each { |transaction| puts transaction }
    end
  end
end

# Class to manage the Payment System
class CreditCardPaymentSystem
  def initialize
    @users = []  # Store users in the system
  end

  # Method to register a new user
  def register_user(name)
    user = CreditCardAccount.new(name)
    @users << user
    puts "#{name} has been successfully registered."
    user
  end

  # Method to find a user by name
  def find_user(name)
    @users.find { |user| user.name == name }
  end

  # Process a payment for a user
  def process_payment(user_name, amount)
    user = find_user(user_name)
    if user
      user.credit_card.make_payment(amount)
    else
      puts "User not found."
    end
  end

  # Process a charge for a user
  def process_charge(user_name, amount)
    user = find_user(user_name)
    if user
      user.credit_card.charge_payment(amount)
    else
      puts "User not found."
    end
  end
end

```

```ruby

# Initialize the Payment System
payment_system = CreditCardPaymentSystem.new

# Register users
john = payment_system.register_user("John")
mary = payment_system.register_user("Mary")

# John makes a payment (deposit) to his card
payment_system.process_payment("John", 1000)

# Mary makes a payment (deposit) to her card
payment_system.process_payment("Mary", 2000)

# John makes a charge (spend) on his card
payment_system.process_charge("John", 500)

# Check balances
john.check_balance
mary.check_balance

# View transaction history
puts "\nJohn's Transaction History:"
john.view_transaction_history

puts "\nMary's Transaction History:"
mary.view_transaction_history

# Trying to charge more than the available balance
payment_system.process_charge("John", 600)

# Trying to make a payment that exceeds the credit limit
payment_system.process_payment("Mary", 3000)

```
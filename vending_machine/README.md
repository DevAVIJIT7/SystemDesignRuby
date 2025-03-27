<!-- ABOUT THE PROJECT -->
## Vending Machine System

Designing a **Vending Machine System** involves managing the items available for purchase, tracking inventory, handling payments, and dispensing products. The system should allow users to select items, make payments, and receive the correct product, and should also handle inventory updates and maintenance alerts when items run out.

### Design Overview:
- **Product**: Represents an individual item in the vending machine.
- **VendingMachine**: Manages the items, user interaction, inventory, and payment system.
- **Payment**: Handles the payment process, including validating payment amounts.
- **Transaction**: Represents a completed transaction (item dispensed, payment made).
- **User**: Represents a user who interacts with the vending machine.

```ruby

# Class representing a Product in the vending machine
class Product
  attr_accessor :name, :price, :quantity

  def initialize(name, price, quantity)
    @name = name
    @price = price
    @quantity = quantity
  end

  def reduce_quantity
    @quantity -= 1 if @quantity > 0
  end

  def is_in_stock?
    @quantity > 0
  end
end

# Class representing a Payment made by the user
class Payment
  attr_accessor :amount_paid

  def initialize(amount_paid)
    @amount_paid = amount_paid
  end

  def is_enough?(price)
    @amount_paid >= price
  end

  def calculate_change(price)
    @amount_paid - price
  end
end

# Class representing a Transaction (successful purchase)
class Transaction
  attr_accessor :product, :payment, :change

  def initialize(product, payment)
    @product = product
    @payment = payment
    @change = payment.calculate_change(product.price)
  end

  def print_receipt
    puts "Purchased: #{@product.name}"
    puts "Amount Paid: $#{@payment.amount_paid}"
    puts "Change: $#{@change}"
  end
end

# Class representing the Vending Machine System
class VendingMachine
  attr_accessor :products

  def initialize
    @products = []  # List of products in the vending machine
  end

  # Add a product to the vending machine
  def add_product(name, price, quantity)
    product = Product.new(name, price, quantity)
    @products << product
  end

  # Display available products
  def display_products
    puts "Available products:"
    @products.each_with_index do |product, index|
      puts "#{index + 1}. #{product.name} - $#{product.price} (Stock: #{product.quantity})"
    end
  end

  # Allow the user to select a product and make a payment
  def purchase_product(product_index, amount_paid)
    product = @products[product_index - 1]

    if !product.is_in_stock?
      puts "Sorry, the product is out of stock."
      return
    end

    payment = Payment.new(amount_paid)

    if payment.is_enough?(product.price)
      # Process the payment and reduce inventory
      product.reduce_quantity
      transaction = Transaction.new(product, payment)
      transaction.print_receipt
    else
      puts "Insufficient funds. Please insert more money."
    end
  end

  # Restock a product
  def restock_product(product_index, quantity)
    product = @products[product_index - 1]
    product.quantity += quantity
    puts "#{product.name} has been restocked. New stock: #{product.quantity}"
  end
end

```

```ruby

# Initialize the vending machine and add products
vending_machine = VendingMachine.new
vending_machine.add_product("Soda", 1.50, 10)
vending_machine.add_product("Chips", 1.00, 5)
vending_machine.add_product("Candy", 0.75, 8)

# Display available products to the user
vending_machine.display_products

# User purchases a product
puts "Please insert money: $2.00"
vending_machine.purchase_product(1, 2.00)  # User selects product 1 (Soda) and pays $2.00

# Display available products after purchase
vending_machine.display_products

# Restock products
vending_machine.restock_product(2, 10)  # Restock Chips

```
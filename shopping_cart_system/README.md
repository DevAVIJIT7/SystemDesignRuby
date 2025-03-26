<!-- ABOUT THE PROJECT -->
## Parking Lot System

We’ll implement the following components:

- **Product**: Represents an item with a name, price, and quantity.
- **ShoppingCart**: Handles adding/removing items, viewing the cart, and calculating the total.
- **Discount**: Handles applying discount codes.
- **Order**: Finalizes the cart and prints the receipt.

```ruby

# Product class
class Product
  attr_accessor :name, :price, :quantity

  def initialize(name, price, quantity = 1)
    @name = name
    @price = price
    @quantity = quantity
  end
end

# ShoppingCart class
class ShoppingCart
  attr_accessor :items, :discounts

  def initialize
    @items = []
    @discounts = []
  end

  # Add product to cart
  def add_product(product, quantity = 1)
    existing_product = @items.find { |item| item.name == product.name }
    
    if existing_product
      existing_product.quantity += quantity
    else
      @items << Product.new(product.name, product.price, quantity)
    end
    puts "#{product.name} added to cart."
  end

  # Remove product from cart
  def remove_product(product_name)
    @items.delete_if { |item| item.name == product_name }
    puts "#{product_name} removed from cart."
  end

  # View cart
  def view_cart
    if @items.empty?
      puts "Your cart is empty."
    else
      puts "Your Cart:"
      @items.each do |item|
        puts "#{item.name} (x#{item.quantity}) - $#{item.price * item.quantity}"
      end
    end
  end

  # Apply a discount to the cart
  def apply_discount(discount)
    @discounts << discount
    puts "Discount '#{discount.code}' applied."
  end

  # Calculate total price after applying discounts
  def total_price
    subtotal = @items.reduce(0) { |sum, item| sum + item.price * item.quantity }
    total_discount = @discounts.reduce(0) { |sum, discount| sum + discount.calculate(subtotal) }

    total = subtotal - total_discount
    { subtotal: subtotal, total_discount: total_discount, total: total }
  end
end

# Discount class
class Discount
  attr_accessor :code, :discount_type, :amount_or_percentage

  def initialize(code, discount_type, amount_or_percentage)
    @code = code
    @discount_type = discount_type
    @amount_or_percentage = amount_or_percentage
  end

  # Calculate discount based on type
  def calculate(subtotal)
    if @discount_type == 'percentage'
      subtotal * @amount_or_percentage / 100.0
    elsif @discount_type == 'fixed'
      @amount_or_percentage
    else
      0
    end
  end
end

# Order class
class Order
  attr_reader :shopping_cart

  def initialize(shopping_cart)
    @shopping_cart = shopping_cart
  end

  def checkout
    totals = @shopping_cart.total_price
    subtotal = totals[:subtotal]
    total_discount = totals[:total_discount]
    total = totals[:total]

    puts "\n--- Order Summary ---"
    @shopping_cart.view_cart
    puts "Subtotal: $#{'%.2f' % subtotal}"
    puts "Total Discount: $#{'%.2f' % total_discount}"
    puts "Total: $#{'%.2f' % total}"

    puts "\nThank you for your purchase!"
  end
end

```

## Example Usage:

```ruby

# Create some products
product1 = Product.new("Laptop", 1000)
product2 = Product.new("Smartphone", 700)
product3 = Product.new("Headphones", 150)

# Create shopping cart and add products
cart = ShoppingCart.new
cart.add_product(product1, 1)
cart.add_product(product2, 2)  # 2 smartphones
cart.add_product(product3, 3)  # 3 headphones

# View the cart
cart.view_cart

# Apply a discount code
discount = Discount.new('SAVE10', 'percentage', 10)  # 10% off
cart.apply_discount(discount)

# Calculate total before checkout
totals = cart.total_price
puts "\nSubtotal: $#{'%.2f' % totals[:subtotal]}"
puts "Total Discount: $#{'%.2f' % totals[:total_discount]}"
puts "Total after discount: $#{'%.2f' % totals[:total]}"

# Checkout and finalize the order
order = Order.new(cart)
order.checkout

# Remove a product from the cart and checkout again
cart.remove_product("Smartphone")
order.checkout

```
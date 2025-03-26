<!-- ABOUT THE PROJECT -->
## Library System

We'll need the following basic components:
- **Book**: Represents a book in the library.
- **User**: Represents a user who can borrow and return books.
- **Library**: Manages the collection of books and users.

```ruby

# Book class
class Book
  attr_reader :title, :author
  attr_accessor :available

  def initialize(title, author)
    @title = title
    @author = author
    @available = true
  end
end

# User class
class User
  attr_reader :name, :borrowed_books

  def initialize(name)
    @name = name
    @borrowed_books = []
  end

  def borrow_book(book)
    if book.available
      @borrowed_books << book
      book.available = false
      puts "#{@name} borrowed '#{book.title}'"
    else
      puts "Sorry, '#{book.title}' is not available."
    end
  end

  def return_book(book)
    if @borrowed_books.include?(book)
      @borrowed_books.delete(book)
      book.available = true
      puts "#{@name} returned '#{book.title}'"
    else
      puts "#{@name} did not borrow '#{book.title}'."
    end
  end
end

# Library class
class Library
  attr_reader :books, :users

  def initialize
    @books = []
    @users = []
  end

  def add_book(book)
    @books << book
    puts "Added '#{book.title}' to the library."
  end

  def register_user(user)
    @users << user
    puts "Registered user '#{user.name}'"
  end
end

```

## Example Usage:

```ruby

# Create some books
book1 = Book.new("The Great Gatsby", "F. Scott Fitzgerald")
book2 = Book.new("1984", "George Orwell")
book3 = Book.new("Moby-Dick", "Herman Melville")

# Create a library and add books
library = Library.new
library.add_book(book1)
library.add_book(book2)
library.add_book(book3)

# Create a user and register them
user = User.new("Alice")
library.register_user(user)

# User borrows and returns books
user.borrow_book(book1)  # Should succeed
user.borrow_book(book1)  # Should fail (book already borrowed)
user.return_book(book1)  # Should succeed
user.return_book(book2)  # Should fail (book not borrowed)

# Output expected:
# Added 'The Great Gatsby' to the library.
# Added '1984' to the library.
# Added 'Moby-Dick' to the library.
# Registered user 'Alice'
# Alice borrowed 'The Great Gatsby'
# Sorry, 'The Great Gatsby' is not available.
# Alice returned 'The Great Gatsby'
# Alice did not borrow '1984'.

```
<!-- ABOUT THE PROJECT -->
## URL Shortening System

### Design Overview:
- **URLShortener**: Main class that handles URL shortening and redirection.
- **URLDatabase**: Stores the mapping between the shortened URL and the original URL.
- **ShortenedURL**: Represents a shortened URL, storing both the short URL and the original long URL.


```ruby

require 'securerandom'

# URLDatabase class to store and retrieve URLs
class URLDatabase
  def initialize
    @database = {}
  end

  # Store the mapping between short and long URL
  def store(short_url, long_url)
    @database[short_url] = long_url
  end

  # Retrieve the long URL for a given short URL
  def retrieve(short_url)
    @database[short_url]
  end

  # Check if a short URL already exists
  def exists?(short_url)
    @database.key?(short_url)
  end
end

# ShortenedURL class to represent a shortened URL
class ShortenedURL
  attr_reader :short_url, :long_url

  def initialize(long_url, short_url = nil)
    @long_url = long_url
    @short_url = short_url || generate_short_url
  end

  private

  # Generate a short, unique URL using a random string
  def generate_short_url
    SecureRandom.urlsafe_base64(6)  # Generates a random 6-character string
  end
end

# URLShortener class to handle shortening and redirection
class URLShortener
  def initialize
    @url_database = URLDatabase.new
  end

  # Shorten the given URL
  def shorten(long_url)
    shortened_url = ShortenedURL.new(long_url)
    @url_database.store(shortened_url.short_url, long_url)
    shortened_url.short_url
  end

  # Redirect to the original long URL given a short URL
  def redirect(short_url)
    long_url = @url_database.retrieve(short_url)
    if long_url
      puts "Redirecting to: #{long_url}"
      long_url
    else
      puts "Error: Short URL not found!"
      nil
    end
  end

  # Check if a short URL exists
  def url_exists?(short_url)
    @url_database.exists?(short_url)
  end
end

```

```ruby

# Initialize the URLShortener system
url_shortener = URLShortener.new

# Shorten a long URL
long_url = "https://www.example.com/some/very/long/url"
short_url = url_shortener.shorten(long_url)
puts "Shortened URL: #{short_url}"

# Simulate visiting the shortened URL (redirecting)
redirected_url = url_shortener.redirect(short_url)

# Simulate visiting a non-existing short URL
invalid_short_url = "abcdef"
url_shortener.redirect(invalid_short_url)

# Check if a URL exists
puts "URL exists: #{url_shortener.url_exists?(short_url)}"
puts "URL exists: #{url_shortener.url_exists?(invalid_short_url)}"

```
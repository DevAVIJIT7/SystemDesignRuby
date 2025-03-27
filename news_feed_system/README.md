<!-- ABOUT THE PROJECT -->
## News Feed System

### Design Overview:
The basic components in the parking lot system would be:
- **User**: Represents a user who can create posts and follow other users.
- **Post**: Represents a single post made by a user (it can contain text, links, images, etc.).
- **NewsFeed**: Represents the news feed of a user, containing posts from people they follow.
- **Follow System**: Allows a user to follow other users and receive their posts in the feed.
- **Feed Generation**: Generates and displays a personalized feed for each user.

```ruby

# Class representing a Post
class Post
  attr_accessor :content, :user, :timestamp

  def initialize(content, user)
    @content = content
    @user = user
    @timestamp = Time.now  # Timestamp for when the post was created
  end

  def to_s
    "#{@user.username} at #{@timestamp.strftime('%Y-%m-%d %H:%M:%S')}: #{@content}"
  end
end

# Class representing a User
class User
  attr_accessor :username, :posts, :following, :followers

  def initialize(username)
    @username = username
    @posts = []  # List of posts by this user
    @following = []  # List of users this user is following
    @followers = []  # List of users following this user
  end

  # Create a new post
  def create_post(content)
    post = Post.new(content, self)
    @posts << post
    post
  end

  # Follow another user
  def follow(user)
    return if @following.include?(user)  # Prevent duplicate follows
    @following << user
    user.add_follower(self)  # Add this user to the followed user's followers list
  end

  # Add a follower to this user's list
  def add_follower(user)
    @followers << user
  end

  # Get all posts from users this user follows
  def get_feed
    feed = []
    @following.each do |followed_user|
      feed.concat(followed_user.posts)  # Get posts from all followed users
    end
    feed.sort_by(&:timestamp).reverse  # Sort posts by timestamp (most recent first)
  end
end

# NewsFeed class to manage users and posts
class NewsFeedSystem
  def initialize
    @users = []  # All users in the system
  end

  # Register a new user
  def register_user(username)
    user = User.new(username)
    @users << user
    user
  end

  # Find a user by their username
  def find_user(username)
    @users.find { |user| user.username == username }
  end

  # Display a user's feed
  def display_feed(user)
    feed = user.get_feed
    if feed.empty?
      puts "#{user.username}'s feed is empty."
    else
      feed.each { |post| puts post }
    end
  end
end

```

```ruby

# Initialize the NewsFeedSystem
news_feed_system = NewsFeedSystem.new

# Register users
alice = news_feed_system.register_user("Alice")
bob = news_feed_system.register_user("Bob")
charlie = news_feed_system.register_user("Charlie")

# Users create posts
alice.create_post("Hello world! This is Alice.")
bob.create_post("Good morning, everyone!")
charlie.create_post("Just had a great lunch!")

# Users follow each other
alice.follow(bob)
bob.follow(charlie)

# Display Alice's feed (should include Bob's posts)
puts "\n--- Alice's Feed ---"
news_feed_system.display_feed(alice)

# Display Bob's feed (should include Charlie's posts)
puts "\n--- Bob's Feed ---"
news_feed_system.display_feed(bob)

# Display Charlie's feed (should be empty)
puts "\n--- Charlie's Feed ---"
news_feed_system.display_feed(charlie)

```
<!-- ABOUT THE PROJECT -->
## Chat Application

### Design Components:
- **User**: A user who can send and receive messages.
- **Message**: Represents a message in a chat.
- **Chat**: A chat could be either a one-on-one conversation or a group conversation.
- **Notification**: For sending alerts to users when new messages are received.
- **ChatApp**: The main app to manage users, chats, and messages.

```ruby

require 'date'

# User class
class User
  attr_accessor :username, :status, :chats

  def initialize(username)
    @username = username
    @status = 'online'  # Default status is online
    @chats = []
  end

  def send_message(receiver, message_text)
    message = Message.new(self, receiver, message_text)
    receiver.receive_message(message)
  end

  def receive_message(message)
    # Add message to chat history
    chat = find_or_create_chat(message)
    chat.add_message(message)
    puts "New message from #{message.sender.username}: #{message.text}"
    # Here, we would send a real notification, but for simplicity, just print it out.
    Notification.new(self, message.sender).send
  end

  def find_or_create_chat(message)
    # Find an existing chat or create a new one if not found
    chat = @chats.find { |c| c.includes?(message.sender) }
    chat ||= Chat.new(self, message.sender)  # Create a new chat if none exists
    @chats << chat unless @chats.include?(chat)
    chat
  end
end

# Message class
class Message
  attr_accessor :sender, :receiver, :text, :status, :timestamp

  def initialize(sender, receiver, text)
    @sender = sender
    @receiver = receiver
    @text = text
    @status = 'sent'  # 'sent', 'delivered', 'read'
    @timestamp = DateTime.now
  end
end

# Chat class (represents a one-on-one or group chat)
class Chat
  attr_accessor :users, :messages

  def initialize(user1, user2)
    @users = [user1, user2]
    @messages = []
  end

  def includes?(user)
    @users.include?(user)
  end

  def add_message(message)
    @messages << message
  end

  def display_chat
    @messages.each do |message|
      puts "#{message.timestamp}: #{message.sender.username} to #{message.receiver.username} - #{message.text} (Status: #{message.status})"
    end
  end
end

# GroupChat class (extends Chat to allow multiple users)
class GroupChat < Chat
  def initialize(users)
    super(users.first, users.last)  # Call superclass initializer (pairing two users)
    @users = users
  end

  def add_user(user)
    @users << user unless @users.include?(user)
  end
end

# Notification class
class Notification
  def initialize(receiver, sender)
    @receiver = receiver
    @sender = sender
  end

  def send
    puts "Notification: #{@sender.username} sent a new message to #{@receiver.username}."
  end
end

# ChatApp class to manage the app
class ChatApp
  def initialize
    @users = []
  end

  def register_user(username)
    user = User.new(username)
    @users << user
    user
  end

  def find_user(username)
    @users.find { |user| user.username == username }
  end

  def display_chat_history(user1, user2)
    chat = user1.chats.find { |c| c.includes?(user2) }
    chat.display_chat if chat
  end
end

```

## Example Usage:

```ruby

# Create a new ChatApp instance
app = ChatApp.new

# Register users
alice = app.register_user("Alice")
bob = app.register_user("Bob")
charlie = app.register_user("Charlie")

# Alice and Bob start chatting
alice.send_message(bob, "Hey Bob, how are you?")
bob.send_message(alice, "I'm good, Alice! How about you?")

# Alice and Charlie create a group chat
group_chat = GroupChat.new([alice, bob, charlie])
alice.send_message(charlie, "Welcome to the group chat, Charlie!")

# View the chat history between Alice and Bob
puts "\n--- Chat History between Alice and Bob ---"
app.display_chat_history(alice, bob)

# View the chat history in the group chat
puts "\n--- Group Chat Messages ---"
group_chat.display_chat

# Demonstrate adding a new user to the group
group_chat.add_user(charlie)
charlie.send_message(bob, "Hi Bob, nice to meet you in the group!")

```
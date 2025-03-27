<!-- ABOUT THE PROJECT -->
## File Storage System

### Design Overview:
- **File**: Represents a file with attributes like name, size, type, and creation time.
- **Folder**: Represents a collection of files. It can contain other folders (like directories).
- **StorageSystem**: The core system that allows creating folders, uploading files, viewing files, and deleting files.


```ruby
require 'date'

# File class representing an individual file
class File
  attr_accessor :name, :size, :type, :creation_date

  def initialize(name, size, type)
    @name = name
    @size = size  # Size in bytes
    @type = type  # E.g., "text", "image", "pdf"
    @creation_date = DateTime.now
  end

  def to_s
    "#{@name} (#{@type}) - #{@size} bytes - Created on #{@creation_date}"
  end
end

# Folder class represents a collection of files and subfolders
class Folder
  attr_accessor :name, :subfolders, :files

  def initialize(name)
    @name = name
    @subfolders = []  # Array of Folder objects
    @files = []       # Array of File objects
  end

  def add_file(file)
    @files << file
    puts "#{file.name} added to folder '#{name}'."
  end

  def add_subfolder(folder)
    @subfolders << folder
    puts "Subfolder '#{folder.name}' created in folder '#{name}'."
  end

  def list_files
    puts "\nFiles in folder '#{name}':"
    if @files.empty?
      puts "No files in this folder."
    else
      @files.each { |file| puts file }
    end
  end

  def list_subfolders
    puts "\nSubfolders in folder '#{name}':"
    if @subfolders.empty?
      puts "No subfolders in this folder."
    else
      @subfolders.each { |folder| puts folder.name }
    end
  end
end

# StorageSystem class to manage folders and files
class StorageSystem
  attr_accessor :root_folder

  def initialize
    @root_folder = Folder.new('root')
  end

  def create_folder(folder_name, parent_folder = @root_folder)
    new_folder = Folder.new(folder_name)
    parent_folder.add_subfolder(new_folder)
    new_folder
  end

  def upload_file(file_name, file_size, file_type, folder = @root_folder)
    new_file = File.new(file_name, file_size, file_type)
    folder.add_file(new_file)
    new_file
  end

  def delete_file(file_name, folder = @root_folder)
    file = folder.files.find { |f| f.name == file_name }
    if file
      folder.files.delete(file)
      puts "#{file_name} deleted from folder '#{folder.name}'."
    else
      puts "File '#{file_name}' not found in folder '#{folder.name}'."
    end
  end

  def delete_folder(folder_name, parent_folder = @root_folder)
    folder = parent_folder.subfolders.find { |f| f.name == folder_name }
    if folder
      parent_folder.subfolders.delete(folder)
      puts "Folder '#{folder_name}' deleted."
    else
      puts "Folder '#{folder_name}' not found."
    end
  end

  def list_files_in_folder(folder = @root_folder)
    folder.list_files
  end

  def list_subfolders_in_folder(folder = @root_folder)
    folder.list_subfolders
  end
end

```

```ruby

## Example Usage:

# Create a new storage system
storage = StorageSystem.new

# Create folders
folder1 = storage.create_folder("Documents")
folder2 = storage.create_folder("Images", folder1)
folder3 = storage.create_folder("Videos", folder1)

# Upload files
file1 = storage.upload_file("file1.txt", 500, "text", folder1)
file2 = storage.upload_file("image1.jpg", 1500, "image", folder2)
file3 = storage.upload_file("video1.mp4", 2000, "video", folder3)

# List files in the root folder
storage.list_files_in_folder(storage.root_folder)

# List files in a specific folder (e.g., Documents)
storage.list_files_in_folder(folder1)

# List subfolders in a specific folder (e.g., Documents)
storage.list_subfolders_in_folder(folder1)

# Delete a file from a folder
storage.delete_file("file1.txt", folder1)

# Delete a folder
storage.delete_folder("Images", folder1)

# List files and subfolders again after deletion
storage.list_files_in_folder(folder1)
storage.list_subfolders_in_folder(folder1)

```
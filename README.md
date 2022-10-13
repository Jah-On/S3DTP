# S3DTP
S3DTP is the Simple Secure Socket Data Transport Protocol.

How to install:
  - Clone this repo however you like. 
  - Go into the extracted/cloned folder and run ```python3 -m pip install -r requirements.txt``` followed by ```python3 -m pip install .```
  - You're all set!

How to read the argument comments:
  - ```Optional:``` indicates that you don't need to pass the argument for the command to run but you may need to if you are trying to invoke specific behavior. Passing an optional argument is done by ```your_arg_name_here=your_value_here```.
  - The first word indicates the data type that is required for proper functionality.
  - The second word is the arg name. This is not neccessary for required arguments but is for optional ones.
  - After that are comments showing the default value and what it does where neccessary. 
  - Comma indicates a new argument

Documentation:
```python
class User:

  # Makes new user
  def __init__(Optional: string username ("" by default), Optional: string password ("" by default (None)), Optional: int access (S3DTP.READ by default), Optional: string path ("" by default (working directory))):
    # Return type: None

class Server:

  # This is where the write to memory operations are stored. It is a dictionary with a mapping of the bytestring name and the bytes data that was recieved
  memstorage = {}
  
  # Stores the value of the last changed value
  lastChanged = b''
  
  # Stores the number of active connections
  peers = 0

  # Starts server
  def __init__(string IP Address, Optional: bool encryption (True by default), Optional: int maxPeers (-1 by default (unlimited))):
    # Return type: None

  # Adds user to server
  def addUser(User user):
    # Return type: None
    # Raises exception if arg type is not of User type

  # Shuts down server
  def close():
    # Return type: None

class Client:
  # Connects/(re)connects to (the) same/a (different) server
  def __init__/__call__(string IP Address, Optional: string username ("" by default), Optional: string password ("" by default (None))):
    Return type: None

  # Reads data from file and sends it to the server
  def write_from_file(string filepath, Optional: int mode (S3DTP.FILE by default), Optional: string name ("" by default (Will use file name))):
    # Return type: bool (Indicates whether the operation was a success)

  # Reads data from buffer and sends it to the server
  def write_from_memory(bytes data, string filepath, Optional: int mode (S3DTP.FILE by default)):
    # Return type: bool (Indicates whether the operation was a success)

  # Reads from server
  def read(string name, Optional: string filepath ("" by default (See return type comment))):
    # Return type: bytes (If "filepath' is blank then it will return the data, otherwise it will return a blank byte string (0xFF will be returned upon error code from server))

  # Gets a list of files and directories
  def ls(Optional: int mode (Location in this context) (S3DTP.FILE by default), Optional: string filepath ("" by default (User root directory))):
    # Return type: list of byte strings as names

  # Deletes file, folder, or memory
  def rm(string path (or memory name):
    # Return type: bool (Indicates whether the operation was a success)

```

A quick start example for a server (That prints new messages written to memory):
```python

import S3DTP as dt
import time

# Starts an encrypted server that is binded to LAN
server = dt.Server("")

# Adds a user to the server. This user has no name, no password, uses the running directory to store files, and can read and write.
server.addUser(dt.User(access = dt.RW))

# Makes a variable to store the previously changed value
last = server.lastChanged

while True:
  # Blocks until change occurs
  while (last == server.lastChanged):
    time.sleep(0.01)
  # Will attempt to print from memstorage with the last changed value
  try:
    print(server.memstorage[server.lastChanged])
  # Which will fail if the name is a file or doesn't exist
  except:
    pass
  last = server.lastChanged

```

A quick start example for a client to match the above example:
```python

import S3DTP at dt

# Connects to the server at localhost
client = dt.Client("localhost")

# Used to generate names
counter = 0
while True:
  # Grabs input from user
  userInput = input("What message do you want to send to the server? ")
  
  # Sends the message to the server indicating a write to memory. The name is the counter number. 
  client.write_from_memory(bytes(userInput, "utf8"), mode=dt.MEM, name=str(counter))
  # Increment counter
  counter+=1

```
Both the client and server have loggers for posting bugs/tracing problems. 

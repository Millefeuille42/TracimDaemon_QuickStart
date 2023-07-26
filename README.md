
# TracimDaemon - Quickstart

TracimDaemon is a master daemon for the TracimDaemon project, providing an event-based system, a "Local" Tracim event API, and a "Local" Tracim API. This quickstart guide will walk you through the basic usage and configuration of TracimDaemon.

## Features

- Event-based system
- "Local" Tracim event API
- "Local" Tracim API

## Usage

### Configuration

Required folders and files will be created upon first boot.

1. TracimDaemon will attempt to find the path to a config folder using the following selectors, in order of priority:
   - First program argument
   - User's config folder
   - User's home folder + `.config/`

2. If the path to the config folder is not provided, the selector fails. For the first argument selector, the user must provide it; for the other two, the system is responsible.

3. The config folder will be referred to as `dir` from this point onwards.

4. TracimDaemon will then try to read the `dir/TracimDaemon` folder. If it does not exist, the daemon will create it along with a default config file and notification folder.

5. The configuration file for TracimDaemon is in JSON format and looks like this:

```json
{
  "tracim": {
    "url": "http://localhost:8080/api",
    "username": "Me",
    "mail": "me@example.com",
    "password": "S3crƎtP4s$woRd"
  },
  "socket_path": "/path/to/sock"
}
```

6. Explanation of the configuration fields:
   - `tracim`: Information about the Tracim server and user
     - `url`: URL of the Tracim server, including the `api` route
     - `username`: Username of the Tracim user, if required
     - `mail`: Email address of the Tracim user, if required
     - `password`: Password of the Tracim user
   - `socket_path`: Path to the socket file

### Runtime

TracimDaemon, by itself, only logs incoming events. To make use of it, you need to hook a plugin to it, such as [TracimPushNotification](https://github.com/Millefeuille42/TracimPushNotification) project.

1. When starting a plugin, the plugin will notify TracimDaemon of it's existence through the master socket.

2. TracimDaemon will then hook these plugins and start sending events to them as they occur.

## Plugins

Writing plugins can be done in any language due to the simple daemon protocol. However several libraries exists. Here we will use the python library.

TracimDaemonSDK serves as an SDK for the TracimDaemon project. This documentation is designed to provide a quick introduction to using the TracimDaemonSDK.

### Installation

You can install the TracimDaemonSDK using `pip`:

bash

`pip install tracim_daemon_sdk` 

### Creating a TracimDaemon Client

To use the TracimDaemonSDK, you first need to create a TracimDaemon client by importing the `TracimDaemonClient` class and providing the appropriate socket paths:

```python
from tracim_daemon_sdk import TracimDaemonClient

client = TracimDaemonClient(
    master_socket_path='/home/user/.config/TracimDaemon/master.sock',
    client_socket_path='/home/user/.config/MiniClient/client.sock',
)
```

### Creating and Listening to the Client Socket

After creating the TracimDaemon client, you need to create and listen to the client socket:

python

`client.create_client_socket()` 

To ensure proper closing of the socket, it is recommended to wrap the rest of the code inside a try/finally block:

```python
try:
    # Your code for handling events goes here
finally:
    client.close()
```

### Setting Up Event Handlers

Event handlers are used to process different events received from the TracimDaemon. You can set up your event handlers as follows:

```python
from tracim_daemon_sdk import TracimDaemonClient
from tracim_daemon_sdk.event import DAEMON_TRACIM_EVENT
from tracim_daemon_sdk.daemon_event import DaemonEvent
from tracim_daemon_sdk.data import TLMEvent
from tracim_daemon_sdk.helper import decode_json

def default_event_handler(c: TracimDaemonClient, e: DaemonEvent):
    tlm: TLMEvent = decode_json(e.data)
    print(tlm.event_type)

client.event_handlers[DAEMON_TRACIM_EVENT] = default_event_handler` 
```

In the example above, we define a default event handler that processes the `DAEMON_TRACIM_EVENT` event type. The `decode_json` helper function is used to parse the data associated with the event, and we print the event type.

## Registering and Listening to Events

After setting up your event handlers, you need to register the client with the TracimDaemon and start listening to events:

```python
client.register_to_master()
client.listen_to_events()
``` 

With this code, your TracimDaemonSDK client is ready to process events from the TracimDaemon.

### Conclusion

This quickstart documentation provides a simple guide for mid to low-level developers to get started with the TracimDaemonSDK. It covers the installation, creating a TracimDaemon client, setting up event handlers, and listening to events. You can further explore the TracimDaemonSDK documentation for advanced features and functionalities. Happy coding!

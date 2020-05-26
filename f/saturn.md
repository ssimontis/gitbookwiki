---
description: Web Server framework inspired by Elixir's Phoenix
---

# Saturn

* Built on top of ASP.NET Core and the existing _Giraffe_ library for F\#
* Provides a higher layer of abstraction to increase dev productivity while still realizing performance gains of Giraffe and Kestrel
* Made up of several system components

### Application

* Starts and ends request lifecycle
* Handles requests until router takes over
* Provides a core set of plugs \(middleware\) which are applied to all requests
* Dispatches incoming requests to a router
* Enables application hosting and configuration

### Router

* Parses incoming requests and dispatches them to the proper controller action
* Binds parameters on controller actions to data contained in routed request
* Provides utility methods to create route paths or expose resources at a given URL
* Defines named pipelines through which requests may pass
* Groups of plugs can be applied to a particular routing group

### Controllers

* Provide a number of actions which are responsible for fulfilling requests
* Prepare data and pass it to the view layer
* Invoke rendering of response via views
* Perform redirects
* Return data as an API using JSON or XML

### Views

* Render templates
* Act as presentation layer
* Providde helper functions to transform presentation data

### Channels

* Manage sockets for real time communication
* Analogous to controllers but allow bidirectional communication through persistent connections

### Scaffolding scripts

* **dotnet new** template provides a base application
* **dotnet saturn** CLI tool controls migrations and scaffolds new application components


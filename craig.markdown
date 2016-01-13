---
layout: page
title: "Craig"
description: "Server Backend and Booking Website"
header-img: "img/craig-1.png"
---



# How do you build a Web Service?
For multifaceted projects where a number of heterogenous clients are required to operate on a single dataset, there needs to be a single source of truth. For Internet of Things projects this normally takes the form of a web service with communication to the clients through Hyper Text Transport Protocol (HTTP) or socket based communication. In this project, we make use of both.

## HTTP
This is used for the brunt of the client to server communication, as the stateless nature of the protocol allows for requests to be triggered from the client at their will. HTTP requests take the following form:

| Segment | Example | Description |
| Method | GET / POST / PUT / DELETE | Specifies what kind of result is expected from this request. Method types are explained below |
| Request URI | /bleeprs | Specifies the resource being requested |
| Body | id=2 | Used to send parameters to the server. In a GET request these are visible in the browser. |

### REST (REpresentational State Transfer)
REST is a pattern for implementing web services over HTTP which uses method types and the URI to carry out actions on server data.

* **GET** requests either list all members of a collection (for a */collection* request) or retrieve details about a particular member of a collection (e.g */collection/1*)
* **POST** requests create new members of a collection
* **PUT** requests on a collection member modify that member
* **DELETE** requests destroy a member or a collection

This pattern is used in a number of high profile data driven APIs, making it a good choice for a web service of this type.

## Sockets
Unfortunately HTTP is less useful for server initiated communication, so in order to facilitate the real time aspects of this project without the overhead of continuously polling the server at a short interval we need to use sockets. A client can simply hold open a connection with the server, which the server can write to at will. As the socket connection is client initiated, the server does not need to be aware of the user's IP address and the connection can exist regardless of the firewall situation on the clientside.

# Design Decisions
As the project was going to need both stateless and realtime communication, we needed to use both of the above. The server was going to need to store information relevant to the Bleepr and its state persistently, and relay updates of this information to relevant parties.

At this prototyping stage of the project it was decided that authentication and authorisation was going to be an unnecessary complication, and therefore was excluded. In the future, key based authentication could be used to secure the communication with the restaurant based device, with communication to human clients authenticated with a Single Sign On standard such as OAuth 2.

## Choosing a Web Framework
There are many Web Frameworks to choose from, so in this project it was decided that the Ruby on Rails framework had features that might be found useful. Of particular interest was the ease of implementing RESTful services, and the newly released realtime sockets library.

Usefully for the timeframe of the project, Rails prefers convention over configuration so for the most part we were able to utilise the default behaviour and avoid a significant volume of boilerplate that might be required in less fully featured frameworks.

# Implementation

## Models

In the case of this project there are a number of associated data models to support the varying use cases.

![Entity Relation Diagram](../img/erd.png)

These are implemented using standard ActiveRecord models attached to our MySQL database, with appropriate indexes, primary keys and foreign keys to speed up querying.

Some data operations performed by the clients may not make sense from a real world perspective, for example having simultaneous bookings on one table. These constraints are enforced using ActiveRecord validations on the models, such as this one for the aforementioned condition:

    def only_one_occupied_occupancy_per_table
      if occupied && table.occupancies.where(occupied: true).count > 0
        errors.add(:occupied, "table is already occupied")
      end
    end

The English like syntax makes it easy to specify new validations, and the way errors are specified as being errors with a particular attribute allows the client to indicate to the end user where the problem is and how to resolve it.

Modifications to the models are possible by using the database migration facility built into Rails, so the database can be nondestructively restructured to incorporate future work while continuing to utilise the existing code.

## Controllers

In Rails parlance, these are the actions exposed to the clients over HTTP. For the most part these take the "CRUD" pattern that REST enforces; with actions for creating, reading, updating and deleting data entities. However in order to ease the interaction with the relay client, a few actions were implemented to carry out common actions such as assigning an order to a table or getting a list of tables that are occupied.

## Views

The clients were to receive responses in JSON format, as parsing libraries are ubiquitous across all platforms without the additional overhead of XML, for example. 

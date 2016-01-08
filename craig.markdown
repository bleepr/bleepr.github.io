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

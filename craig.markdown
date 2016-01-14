---
layout: page
title: "Craig"
description: "VP Data Warehousing"
header-img: "img/craig-1.png"
---

My role in the project was providing the API web service to facilitate the storage and management of data for the project.


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

At this prototyping stage of the project it was decided that authentication and authorisation was going to be an unnecessary complication, and therefore was excluded. In the future, key based authentication could be used to secure the communication with the restaurant based device, with communication to human clients authenticated with a Single Sign On standard such as [OAuth 2](http://oauth.net/2/).

## Choosing a Web Framework
There are many Web Frameworks to choose from, so in this project it was decided that the Ruby on Rails framework had features that might be found useful. Of particular interest was the ease of implementing RESTful services, and the newly released realtime sockets library.

Usefully for the timeframe of the project, Rails prefers convention over configuration so for the most part we were able to utilise the default behaviour and avoid a significant volume of boilerplate that might be required in less fully featured frameworks.

<hr>

# Implementation

## Models

In the case of this project there are a number of associated data models to support the varying use cases.

![Entity Relation Diagram](../img/erd.png)

These are implemented using standard [ActiveRecord](https://github.com/rails/rails/tree/master/activerecord) models attached to our MySQL database, with appropriate indexes, primary keys and foreign keys to speed up querying.

Some data operations performed by the clients may not make sense from a real world perspective, for example having simultaneous bookings on one table. These constraints are enforced using ActiveRecord validations on the models, such as this one for the aforementioned condition:

    def only_one_occupied_occupancy_per_table
      if occupied && table.occupancies.where(occupied: true).count > 0
        errors.add(:occupied, "table is already occupied")
      end
    end

The English like syntax makes it easy to specify new validations, and the way errors are specified as being errors with a particular attribute allows the client to indicate to the end user where the problem is and how to resolve it.

Modifications to the models are possible by using the database migration facility built into Rails, so the database can be nondestructively restructured to incorporate future work while continuing to utilise the existing code.

## Controllers

In Rails parlance, these are the actions exposed to the clients over <abbr title="HyperText Transfer Protocol">HTTP</abbr>. For the most part these take the <abbr title="Create Read Update Delete">"CRUD"</abbr> pattern that <abbr title="REpresentational State Transfer">REST</abbr> enforces; with actions for creating, reading, updating and deleting data entities. However in order to ease the interaction with the relay client, a few actions were implemented to carry out common actions such as assigning an order to a table or getting a list of tables that are occupied.

## Views

The clients were to receive responses in <abbr title="JavaScript Object Notation">JSON</abbr> format, as parsing libraries are ubiquitous across all platforms without the additional overhead of XML, for example. In order to incorporate data from associated models into responses, the [JBuilder](https://github.com/rails/jbuilder) domain specific language for JSON was used in actions on customer entities. This provides a lightweight syntax to customise JSON responses, as follows:

    json.(@customer, :id, :first_name, :last_name, :email, :phone, :created_at, :updated_at)
    json.cards @customer.cards, :id

Creates JSON:

    {
      "id": 2,
      "first_name": "Cammy",
      "last_name": "Gray",
      "email": "craig@craigsnowden.com",
      "phone": "07540635316",
      "created_at": "2015-10-14T05:35:43.994Z",
      "updated_at": "2015-10-22T15:48:03.674Z",
      "cards": [
        {
          "id": "1217709"
        },
        {
          "id": "134 177 103 143 223"
        }
      ]
    }

## Channels

In order to provide real time updates to clients, we use a new feature for Rails called [ActionCable](https://github.com/rails/actioncable). Due to not having been released at the time of the project a prerelease version of the project was used, which introduced problems relating to this.

At the time of the project messages containing arrays or sub-objects would be string escaped inside another JSON object; meaning that the client would have to parse the wrapper object, unescape the wrapped object and parse that. Unfortunately no one appears to have taken notice of this issue, which was [reported on the Rails project](https://github.com/rails/rails/issues/22675).

<hr>

# Evaluation

This project was not without its challenges, however the design decisions have stood up to the implementation requirements.
As the project utilised the Model View Controller pattern (as laid out above), future enhancements can be made with ease to accommodate changing hardware requirements. As a number of external libraries have been used, the project can benefit from the continuing development of these and gain additional features either for "free" or for little time investment.

Over the project period 10,000 requests were served, only using 34 MiB bandwidth. This would suggest that in a real life application of the project, the service would not have a noticeable impact on the other internet operations of a food service operation, and could indeed operate over a cellular link using a 4G dongle installed in the relay device.

## Future Work

As ActionCable is still early in its life, developments to this should be followed to ensure that best practices are implemented. It has now been integrated into the Rails core, and with the imminent release of Rails 5, upgrading to this would gain the memory usage benefits of the latest versions of Ruby.

At this time, push notifications to Android are handled by Pushbullet as a workaround - a future project would implement the standard Google Cloud Notifications service to provide better user experience on the staff side.

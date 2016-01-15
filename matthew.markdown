---
layout: page
title: "Matthew"
description: "Android"
header-img: "img/matt-header.png"
---
## Introduction
For our project, we settled upon the idea of a Bleepr, an Internet-of-Things device designed for use in a food service environment.
Our system provdes restauranteurs with a web and mobile interface to a cloud-based service for managing their venue, with on-table
customer interaction devices (the eponymous Bleepr) interacting with the service via means of a Bluetooth Low-Power relay. Customers can 
use the Bleepr device to perform common restaurant activities, such as calling a waiter and requesting the bill, and  additionally can monitor
their order status as it progresses through the kitchen.

The device benefits staff by acting as a table marker, allowing waiting staff to know where on the floor the meal is to be served, and
benefits management by providing a method of tracking various restaurant metrics such as mean time to order completion, customer visit counts
and heat maps of table activity.

We modelled the system as 3 sections: Frontends, Relay and Hardware. The frontends section consists of the mobile and web staff applications,
as well as a table booking application. The relay section consists of the hardware and software components needed to communicate with both
the cloud service and the Bleepr hardware. Finally, the hardware section consists of the design and implementation of the Bleepr hardware and
firmware.

For my part in the project, I was responsible for the implementation of the mobile frontend, targetting the Android platform. The source
code for which can be found at:

<https://github.com/bleepr/bleepr-droid>

## Requirements
The minimum viable featureset we decided on for the mobile frontend required that the app be able to:

* **Display information on table status**: Neccessary to alert staff on the orders associated with a table and the current occupancy state
* **Display information on future table occupancy events**: Useful to prepare for incoming orders and direct customers to tables which will be free for an appropriate time
* **Display information on open and in-progress orders**: Necessary for kitchen staff to see what each table requires, and for waitstaff to see what remains to be served
* **Allow order state to be changed and the change to be reflected in the cloud service**: Necessary for synchronisation between restaurant staff

## Design and Implementation
To begin with, I sketched a mockup of the various activities the application would need to consist of to meet these requirements. As
we have no need to show the full extent of restaurant metrics to the service staff, and as we'd like to create an application which is
simple to use and impedes the activities of service staff in a minimal way, it was decided very early on that we should omit the ability
to view most of the metrics the system would be capable of generating. The resultant mockup can be seen below, in Figure 1:

![Mockup](/img/matthew/mockup.png)

*Figure 1: The mockup of application activities*

This diagram shows the intended states of application and how state changes would be initiated by a user. We begin with a login
activity, which upon successful authentication brings users to a menu where they can select whether they wish to see orders or tables.
Clicking one of the menu buttons takes the user to a seperate-yet-similar flow for tables and orders, in which items are presented in 
a scrolling list format, and selecting a list item brings the user to a detail page. Further, in this diagram, I had planned for a
heatmap button on the detail page of certain items to show certain metrics to staff in a more presentable fashion, however this was
scrapped for the sake of time.

Next, I created a skeleton application, using the stock components provided to produce barebones implementations of all the above-described
activities, and link them together to create the desired user flow. As I chose to target a recent version of Android, I investigated
the recommended Material Design visual language and attempted to theme the application as such (though some areas have been left unthemed
due to a desire to implement missing functionality over missing design for the sake of time).

Conceptually, the resultant application was to be quite simple, but I architected the application as such:

* A Frontend (UI code)
* A ContentManager (which maintains a local cache of requested API data in an SQLite database)
* A background service for sending and recieving API requests/responses (populates the ContentManager)
* A background service for managing GCM messages (intended for use as part of an integrated notification service, rather than the application-external notifications currently provided)

Roughly, the flow of control through the application is such that the user performs an action (i.e. view the table list, or an order) and
the ContentManager is called upon to retrieve data relating to the user's request. At certain points during the application's execution,
we send a refresh event to the API background service to refresh the data managed by the ContentManager. This service then calls upon
Google's Volley library to perform network requests - this was chosen over the standard library network classes as it provides a cleaner
method of handling JSON data, and automatically handles network request scheduling. We invoke a GET request to an API endpoint, recieve
a JSON blob, and the library will decode this for us into a useful data structure, and pass it to a "request successful" event handler.

Depending on the nature of the request, the event handler used will extract the relevant information from the JSON blob, insert this into
the ContentManager, and dispatch new network requests to collect more data if necessary.

This service is also used to push new data to the API when the user makes a change, this is handled similarly to the GET request case, but
we use a POST request instead, supplying a JSON blob that we wish to send to the API. In parallel, the GCM service awaits new messages from 
Google Cloud Messaging, but we did not have time to fully implement this, so no messages are ever sent to the application, and thus no
notifications can be generated natively within the application (we use Pushbullet to simulate this behaviour presently).

My reasoning for this architecture is in part to ensure that the code is modular, maintainable and easily extenable (good properties for later
reuse), but is done mostly to ensure a good user experience - it would be possible to handle all of this in a single component, from a 
technical standpoint, but the way Android works means that all UI code must be on a single thread, and if we block that thread with network
requests or database queries, then the application will appear unresponsive to the user.

Some screenshots of the application are included below to illustrate the design and state flow:

| ![Login](/img/matthew/login.png) | ![Menu](/img/matthew/menu.png) | ![Order List](/img/matthew/order-list.png) |
| ![Table List](/img/matthew/table-list.png) | ![Table Detail](/img/matthew/table-detail.png) | ![Order Detail](/img/matthew/order-detail.png)  |

*Figure 2: A selection of screenshots of the various activities in the application*

As we have no multi-tenancy system in place for user accounts in this prototype version, the login screen takes dummy information. In the
event we were to implement one, handling logins would likely be handled via a seperate background service in a manner similar to the API requests,
but handling proper authentication flow (e.g. via OAuth2) as well.

Additionally, I created a custom list item layout to display table occupancy information. This was done to provide a better at-a-glance view
of occupancies, rather than having to view a detail activity for each occupancy the user wishes to check. This layout can be seen in the
screenshot below:

![Table Occupancies](/img/matthew/table-occupancies.png)

*Figure 3: A screenshot of the table occupancies activity, with custom list item*

This shows staff who is occupying the table over a given timespan, the timespan they are occupying it for (potentially indefinite in the event
of no future bookings) and if there was a reservation made in advance. This was achieved by creating a custom XML layout to achieve the look
I wanted for the occupancies items, and then backing that with a custom ListAdapter, which handles populating all of the fields of that layout
(and colouring the text and background appropriately) based on data retrieved from the cache. The JodaTime library proved useful here for
managing date-time values, and constructing well formatted human readable versions of them.

## Evaluation, Improvements and Summary
The application is internally architected in terms of activities and services, each with a clear purpose. This was intentional (and is in some
ways enforced upon you by the Android libraries), in order to make future extensions and maintenance easy to achieve. It has been tested
and works without crashes on my Nexus 5 phone running Android 6, and meets the minimum requirements stated above.

However, with more time it would be nice to add access to more advanced kinds of metrics (such as the previously mentioned heatmaps), as well
as handling refreshes in a more intelligent manner to save network bandwidth and cache space, and the theming could be slightly more conistent.
Furthermore, a useful feature would be the ability to assign customers RFID cards from within the app, allowing service staff to perform this
common task at the door or table.

Additionally, the GCM notification code is mostly in-place on client side, but if we had more time it would be nice to make it work end-to-end.

## References

* Android API Guide: <http://developer.android.com/guide/index.html>
* Volley library documentation: <http://developer.android.com/training/volley/index.html>
* JodaTime documentation: <http://www.joda.org/joda-time/>
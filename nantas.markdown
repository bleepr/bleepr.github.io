---
layout: page
title: "Nantas"
description: "Management portal, data analysis and 3D printing"
header-img: "img/nantas/bg.png"
---

# Web front-end

## Introduction
Bleepr is a system meant to make both the consumer and the restaurateur
experience better and significantly more modern. It brings the connectivity and
interface to the Internet of Things while maintaining the interpersonal
experience you get in restaurants and pubs, and it does so by being both
comprehensive and seamless in its execution. To satisfy such requirements we
created two kinds of user interfaces: the Bleepr box and the application
front-ends.

I was personally responsible for the creation of the web interface for the
staff, the design of some useful data analytic and the testing of the server API
for all the interfaces together with Craig.

## Specifications
The purpose of my role was mostly to build the web front-end for the members of
the staff of the restaurant. The system needed to:

* support multiple users and multiple typologies of users (that is,
  owners/admins and standard staff). That is:
  - provide the ability to add admins and standard users
  - provide the ability to delete admins and standard users
* support multiple Bleeprs. That is:
  - provide a way to add one or multiple Bleeprs to the system.
  - provide a way to delete one or multiple Bleeprs to the system.
* allow the staff to visualise the status of the restaurant and the Bleeprs
  linked to the system.

Additionally the system needed to display some of the collected information
about the restaurant, to allow the staff to gain knowledge about long-term
trends and to get some insights on possible issues in the restaurant.

Finally since we recognised that there are a huge variety of services that offer
some form of restaurant services, the architecture and the design needed to
focus on modularity, expandability and possible self-hosting.

## Design
Choosing the tools to build a system that would satisfy all the requirements was
not the easiest task. Craig decided to use Ruby on Rails to make the API, but
there wasn't any real need to have to deal with the Ruby environment for the
front-end, and while it is true that adding an another ecosystem would increase
the bus-factor risks and maintenance costs, we would have then acquired the
chance to make the design leaner, more modular and completely dissociated with
the server code. As we in fact expected , after a few design iterations and some
refactoring the system ended up being quite nice.

### A Pythonic User Interface

We built the frontend using a very popular web microframework written in
Python called Flask. Out of the many frameworks available in the python
environment Flask was chosen for its bootstrapping capabilities, its simple interface
and the huge popularity and availability of plugins. We excluded using another popular web
framework, Django, because of the included Object Relational Mapping (ORM) and
its general lack of flexibility. Since most of the work was done in a separated
back-end, using a simple ans small framework was definitely a sound choice.

Python was also chosen because of its remarkably broad selection of
state-of-the-art scientific libraries. In particular, many widely used machine
learning and data analysis libraries were already available to use for both
Python 2 and Python 3, giving us the ability to choose even the language
version. We based the entire data pipeline on `numpy`, the standard numerical
Python library, and we used the `matplotlib` library to generate all the
necessary images and graphs.

The interaction with the RESTful API was done using the `request` library for
communication, GET/PUT/DELETE requests and pinging, and the `json` library for
the formatting and parsing of the API.

### Website design

![Design diagram](/img/nantas/diag.png)


#### Login

User management is usually a feature that is very easy to get wrong when
building a website, so we decided to employ a widely used industry Flask plugin
called `flask-login` to provide sessions management to the portal. With this not
only we bootstrapped that feature relatively quickly, but relying on well tested
code meant that most of the work ended up being about understanding and using
the interface for user management without having to worry about writing up the
functionality from scratch. All the data was securely encoded and transmitted
using the API.

#### Dashboard

The main page of the portal was designed to show the staff the state of the
restaurant at the time of use, and to provide a quick reference to the user we
decided to place a map of the restaurant in the dashboard; this map would then
show information such as booked and used tables, active Bleeprs and other
relevant data regarding orders tables. This required to add an interface to
allow the staff to manage tables and to interactively build the map. To do so we
added an API endpoint that would store information about position and size of
tables with respect to the size of the restaurant. In computer vision it's
common practice to use a point map to do geometrical modelling of flat parts.
![flats](/img/nantas/flats.png) We used the same idea to allow the user to
specify the geometry using text files. To make it easy for the Android app and
other user interfaces, we also provided an explicit end point where to download
the map and the filled visual status map.

Additionally we also decided to provide more information by colouring the tables
depending on their status (booked, full, empty) and dynamically loading the new
map every minute by reloading the page. All of this was done in real time, but
the download, data parsing and map creation didn't significantly affect the
loading of the dashboard.

![flats](/img/nantas/maps.png)

#### Analytics

The Bleepr system was designed so that the experience of the restaurant could
also improve over time. For this to happen the system needed to provide an
insight in the "performances" of the service and some feedback from the user. We
built the system to log information about bookings and orders, we logged arrival
and leaving times and we provided the users a way to give a rating of the
experience at the end of their meals. This allowed the creation of a
continuously updating page where all this data would be graphed and analysed to
provide useful information to the staff. The `DataProvider` module was created
to provide an interface to download and parse the data from the API and a
`DataAnalyser` module was created to analyse and provide graphs and tables to
the website. The design was completely modular and based on standard object
oriented programming (OOP) practises so that adding new data or new insights
would be pretty much automatic.

For the demo we provided the following information:

* A heatmap of the restaurant based on usage. This would allow the staff to see
  the "hot zones" of the restaurant and optimise both personnel and tables
  locations. Because of the sparsity of the data it wasn't possible to implement
  this feature in a straightforward way: a useful heatmap is hard to create if
  the dimension of the data is not really related to the space in which the
  heatmap has to be shown. You can visualise this problem by imagining a 3d
  histogram where the bars start at the center of the tables in the dashboard
  map. To instead fill the entire map with a 2D heatmap and solve the problem we
  instead fit 2D gaussian distributions using the shape of the tables to modify
  the two sizes of the gaussians and the amount of uses of the tables to change
  the relative intensity of the table gaussian. Doing this iteratively for each
  table ended up working very well:
  ![heatmap](/img/nantas/heatmap.png)
* A few graphs showing booking and orders trends over previous weeks and months
  (all configurable by uploading standard INI files in the `settings` page)
  useful to quickly spot seasonal performance issues or the overall growth of
  the service. ![plots](/img/nantas/plots.png)
* Predicted happiness rating and user feedback trends. Extremely useful to get
  immediate feedback regarding changes and possible trends. The prediction was
  done by training a linear regressor recursively by splitting a month worth of
  feedback data in training and target sets. This allowed to get a good estimate
  roughly based on the gradient of the last few points in the dataset.
  ![happiness](/img/nantas/happiness.png)
  
#### Settings

To manage all the configuration of both the server data and the frontend, we
built a settings page and made it accessible only by the admin users. We added
functionality such as user management (addition, deletion, permission
management), Bleeprs configuration (MAC addresses, assigned tables) and table
configuration (as described in the dashboard section). Additionally we added a
tool to format and export all the data so that users could import a simple CSV
file in spreadsheet tool such as LibreOffice Calc, Excel or other data analysis
or visualisation tools.


## Evaluation
How did the system fare? Could had it been better?

In terms of data analysis, it is clear that the system is comprehensive and
robust in terms of what's visualised, but much of the power of machine learning
has not been used yet. For instance using only the gradient of the last few data
points to provide estimates of future values greatly limits the prediction's
accuracy. A better way to use the data would be to construct a model not only
based on previous data, but also on environmental data such as weather, city
events, tourism data and so on.

## Conclusions

The web front end was functional and completely up to the requirements by the
time of the demonstration. The data providers and visualisers gave a reasonable
idea of both the status of the restaurant and possible data-driven trends, and
the interface to manage the Bleeprs got actually used to perform the demo. Given
the effort spent in clearly splitting the module, the design ended up being
clearly modular and usable in professional settings. Because of lack of User
Experience training, adjustment to the visual interface and a visual re-design
are still clearly needed, but the project has been definitely satisfactory.

Would have been nice to have a web tool to "draw" the map instead of using text data.

# Bleepr Design

## Design choices

We needed a 3d printed box. Then another. 'cause it was cool. Turns out making
it pretty was pretty hard.

## Technical details

3d printer + material details. Internal refinements. Cool stuff.

## End product

All the photos.

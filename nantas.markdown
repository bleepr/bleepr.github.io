---
layout: page
title: "Nantas"
description: "1% of the world population owns over 50% of the wealth"
header-img: "img/post-bg-02.jpg"
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

I was personally responsible for the creation of the web interface for the staff,
the design of some useful data analytic and the testing of the server API for
all the interfaces together with Craig.

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

Modularity, expandability, self-hosting

## Design
Choosing the tools to build a system that would satisfy all the requirements was
not the easiest task. Craig to build the API using Ruby on Rails, but the
back-end environment had to be split from the front-end for several reasons, so
there wasn't any need to have to deal with the Ruby environment. Because of the
many data science libraries and web tools available to Python, we decided to
build the entire front-end using tools available in the Python environment. As a
bonus Cameron decided to build the relay framework with Python as well, which
meant that we could share code for interfacing with the RESTful API.

After a few design iterations and some refactoring, the system shaped itself
nicely as shown in the figure below.

DESIGN FIGURE HERE

The front-end was built using a very popular web microframework written in
Python called Flask. Out of the many frameworks available in the python
environment Flask was chosen for its bootstrapping capabilities, its simplicity
and the availability of plugins. We excluded using another popular web
framework, Django, because of the included Object Relational Mapping (ORM) and
its general lack of flexibility. Since most of the work was done in a separated
back-end, using a simple ans small framework was a sound choice.

User management is usually a feature that is very easy to get wrong when
building a website, so we decided to employ a widely used industry Flask plugin
called `flask-login` to provide sessions management to the portal.

Python is truly an awesome language for data scientists, as some of the most
used machine learning and data analysis libraries are available to both Python 2
and 3. We based the entire data pipeline on `numpy`, the standard numerical
Python library, and we used the `matplotlib` library to generate all the
necessary images.

The main page of the portal was designed to show the staff the situation of the
restaurant at the time of use. To provide a quick reference to the user we
decided to place a map of the restaurant in the dashboard; this map would then
show information such as booked and used tables, active Bleeprs and
other information relevant to the tables. This required to add an interface to
allow the staff to manage tables and to interactively build the map, and to
modify the global API to allow the Android app to use the same information if
needed. To make it easy for the Android app, we also provided an explicit end
point where to download the map and the full visual status map.

Additionally, all the data is exportable in a neat CSV file that can then be
imported in spreadsheet tool such as LibreOffice Calc.

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

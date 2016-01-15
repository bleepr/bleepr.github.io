---
layout: page
title: "Scott"
description: "Chief Executive Firmware Engineer/Vice President Communications"
header-img: "img/Scott/animatedscreen.jpg"
---



# Introduction
The bleepr device is built of an Arduino microcontroller,
connected sensors and a display. It was absolutely critical to
ensure all these components worked together to give us the smart
restaurant solution we needed.

The hardware and firmware on the bleepr go hand in hand, and
naturally anyone working on the actual device will have had a
part in both, but I have focused on the firmware of the device,
and bringing each of our components together into a revolutionary
culinary experience.

# Requirements
From the start we knew there were a few major keys for success on the firmware side:

* **Communication to the Pi:** The bleepr should be able to send
data back to the rest of the system.
* **Clear interface for the user:** The bleepr should be able to
be able to used by anyone if it wants to **continue the journey to
more success**
* **Customisability:** The bleepr will need to be customisable
in terms of colour schemes (and bleeps!) if we want different
restaurants to use it.

# Design and Implementation

## Choosing the Arduino over the mbed
While we originally started work using the mbed, we realised
quickly in development that it was not the ideal device for
our purpose. Using an arduino instead had a couple of
advantages for us:

* Libraries supported for all connected devices
* Familiarity with the system/Ease of use
* Increased number of output pins
* Small form factor bluetooth functionality

This meant we could focus our time on building the bleepr
functionality, rather than porting libraries.

## Interface
An extremely important part of the **bleepr** is making sure that it is easy to use for
any visitor to the restaurant. As a result of this we wanted to make sure the
interface was as clean and simple as possible.

As discussed in the hardware section, a button system was chosen
due to it being cheaper and easier to implement but we did run
into an interesting interface issue with this. An
unexpected familiarity with touch screens left of a lot of users bashing the screen with their fingers before trying the
buttons. This may have been an anomaly, due to our sample set
being mostly technically minded people but still something we
considered for bulding into future devices.

The ILL9341 library for the screen allowed us to create simple
shapes, which explains the menu's minimalist design.

The menu design was first built early on and was kept throughout the project. We
have a main animated "idle" screen which is used when there is no customer at the table,
and a simple options screen to show anyone at the table available
actions of the bleepr.

[menu screen]
[animated screen]

The systems are clear for most users who are informed on how to
use the system (which should be done when they receive the
  card).



## Bluetooth to Pi
The Bluetooth Low Energy (BLE) communication between the **bleepr** and the **Pi
Relay** for the prototype is quite simple. In fact, there are only 5 commands
sent from **bleepr** to Pi!

All commands follow a similar template; some action from the user is
sensed by the **bleepr**, for example, a card scan or a button press, which will
prompt the **bleepr**
to send an asynchronous command (listed below) to the Pi. The **bleepr** will then
wait for the Pi to respond. If the Pi responds with the relevant signal (an OK
or some data) the **bleepr** will continue with the action, potentially using some
data that has been sent back. After a certain amount of time with no response,
the **bleepr** can display an "action failed" notice and continue back to whatever
state it was in before.

## Place in the System
The **bleepr** has a a self-centered view of the world. It has no knowledge of
rest of the system including any other **bleepr** device. The only interaction
the **bleepr** has is sensing a user's input, and sending out commands via BLE.
A **bleepr** does not need any input to be paired, as this is done automatically
by the relay.

We decided to use this method as it allowed the **bleepr** system to be modular and easily expandable.

## Code Structure
The Arduino operates with a single thread, which presented us
with some problems when we wanted to do concurrent tasks, such
as animating the screen while communicating up to the Pi.

As animation requires timing, the most straightforward method
would e to use the built in *delay()* function. However, this
blocks any other processes on the device so I had to build
something new.

The solution was to build using a hierarchical state machine.
An outer state machine controls the formal state of the machine, and is changed usually from signals from the sensors. The
inner state machines are built similarly to the Arduino
infrastructure, with a setup and loop state with an additional
close state. This meant that animations could be set up by
measuring the time since they started playing on the device
while still being able to perform critical tasks like reading
sensor input.

The best example of this is on the idle screen, where our most
complex animation plays. We can scan a card at any time and it
will be read immediately.

[video of card scan]

## Song name

*video to go here*

While originally put in as an easter egg, this demonstration does show the
**bleepr** is capable of a great deal of customisation. The **bleepr** is
currently set up with a default colour profile, but in future more theme
profiles could be added for restaurants to choose from to fit their colour
scheme.

Further work could make these profiles give a fully customisable setup of colour
schemes and bleeps which would be automatically transmitted to any **bleepr**
added to the system.



## Evaluation and Further Improvements
I hate Uni

## Summary
I hate Uni

---
layout: page
title: "Scott"
description: "Firmware/Communications"
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
be able to used by anyone if it wants to be used in many different situations.
* **Customisability:** The bleepr will need to be customisable
in terms of colour schemes (and bleeps!) if we want different
restaurants to use it.

# Design and Implementation

## Arduino vs mbed
While we originally started work using the mbed, we realised
quickly in development that it was not the ideal device for
our purpose. Using an arduino instead had a couple of
advantages for us:

* Libraries supported for all connected devices
* Familiarity with the system/Ease of use
* Increased number of output pins gave greater options in firmware
* Small form factor bluetooth functionality

This meant we could focus our time on building the bleepr
functionality, rather than porting libraries.

## Human Bleepr Relations
An extremely important part of the bleepr is making sure that it is easy to use for
any visitor to the restaurant. As a result of this we wanted to make sure the
interface was as clean and simple as possible.

As discussed in the hardware section, a button system was chosen
due to it being cheaper and easier to implement but we did run
into an interesting interface issue with this. An
unexpected familiarity with touch screens left of a lot of users bashing the screen with their fingers before trying the
buttons. This may have been an anomaly, due to our sample set
being mostly technically minded people but still something we
considered for building into future devices.

The ILL9341 library for the screen allowed us to create simple
shapes, which explains the menu's minimalist design.

The menu design was first built early on and was kept throughout the project. We
have a main animated "idle" screen which is used when there is no customer at the table,
and a simple options screen to show anyone at the table available
actions of the bleepr.

![Idle screen](/img/Scott/idlescreen.jpg)
*The (animated) Idle screen (before card scan)*

![MainMenu](/img/Scott/mainmenu.jpg)
*The main menu screen (after card scan)*


The systems are clear for most users who are informed on how to
use the system (which should be done when they receive the
  card).



## Bleepr Control to Major Pi
The bleepr has a a self-centered view of the world. It has no knowledge of
rest of the system including any other bleepr device. The only interaction
the bleepr has is sensing a user's input, and sending out commands via BLE.
A bleepr does not need any input to be paired, as this is done automatically
by the relay.

We decided to use this method as it allowed the bleepr system to be modular and easily expandable.


The Bluetooth Low Energy (BLE) communication between the bleepr and the **Pi
Relay** for the prototype is quite simple. In fact, there are only 5 commands
sent from bleepr to Pi!

All commands follow a similar template; some action from the user is
sensed by the bleepr, for example, a card scan or a button press, which will
prompt the bleepr
to send an asynchronous command to the Pi. The bleepr will then
wait for the Pi to respond. If the Pi responds with the relevant signal (an OK
or some data) the bleepr will continue with the action, potentially using some
data that has been sent back. After a certain amount of time with no response,
the bleepr can display an "action failed" notice and continue back to whatever
state it was in before.

In the future we would like to implement asynchronous commands, as the current system was set up to keep everything simple. This added functionality would give us a much wider feature set to the bleepr.



## Code Structure
The Arduino operates with a single thread, which presented us
with some problems when we wanted to do concurrent tasks, such
as animating the screen while communicating up to the Pi.

As animation requires timing, the most straightforward method
would be to use the built in *delay()* function. However, this
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

<div class="well">
<iframe width="560" height="315" src="https://www.youtube.com/embed/hkUgBzYawSg?feature=player_detailpage" frameborder="0" allowfullscreen></iframe>
</div>

This groundwork for the system is easily expandable (to the limit of the Arduino's memory) so in the future, more options can be added easily.

## Making Bleepr Your Own

<div class="well">
<iframe width="560" height="315" src="https://www.youtube.com/embed/e8AOnWCj-hU?feature=player_detailpage" frameborder="0" allowfullscreen></iframe>
</div>

While originally put in as an easter egg, this demonstration does show the
bleepr is capable of a great deal of customisation. The bleepr is
currently set up with a default colour profile, but in future more theme
profiles could be added for restaurants to choose from to fit their colour
scheme.

![RGB](/img/theo/rgbled.jpg)
*A very colourful scheme*

Further work could make these profiles give a fully customisable setup of colour
schemes and bleeps which would be automatically transmitted to any bleepr
added to the system.

## Evaluation and Further Improvements
I believe our firmware has lived up to our original design goals
to a basic level. We can communicate easily to the Pi, the
user interface is easy to use and the bleepr has the ability to
be customisable.

However, we have only laid the foundations for these requirements, and there are still a great deal of Improvements
we could do as we would build further iterations of the project.

The biggest improvements I would make as we do move into the
future for each of our requirements would be:

* **Communications:** Asynchronous communication from the Pi to the Bleepr would open up a lot more functionality to the device,
such as live updates on order status.
* **Interface:** Working to improve the screen and potentially
switching to a touch screen if the budget allowed.
* **Customisability:** Adding profiles beyond the default, and
building the interface on one of the web systems to allowed these to be configured off the device.

## Summary
I believe my parts of the project, in both hardware and firmware were essential in ensuring our project's success. the project worked extremely well in the end and we still have a lot of ideas for features to add and improve if we were to move forward.

I thoroughly enjoyed working on this, despite all the long nights and early morning, and I hope that this shows on the bleepr.

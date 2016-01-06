---
layout: page
title: "Scott"
description: "Chief Executive Firmware Engineer/Vice President Communications"
header-img: "img/post-bg-03.jpg"
---
## Firmware
The hardest part, and the most underappreciated. I am the most valuable member
of the team.

It's a draft pls don't judge pls.

## Introduction
What makes a **bleepr** a man.

## Choosing the Arduino over the mbed
The mbed was crap and we didn't like it.

# Communications

## Place in the System
The **bleepr** has a a self-centered view of the world. It has no knowledge of
rest of the system including any other **bleepr** device. The only interaction
the **bleepr** has is sensing a user's input, and sending out commands via BLE.
A **bleepr** does not need any input to be paired, as this is done automatically
by the relay. We decided to use this method as it allowed the **bleepr** system
to be modular and easily expandable.

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

# Future Developments

## Song name

*video to go here*

While originally put in as an easter egg, this demonstration does show the
**bleepr** is capable of a great deal of customisation. The **bleepr** is
currently set up with a default colour profile, but in future more theme
profiles could be added for restaurants to choose from to fit their colour
scheme.

Further work could make these profiles give a fully customisable setup of colour
schemes and bleeps which would be automatically transmitted to any **bleepr**
addded to the system.

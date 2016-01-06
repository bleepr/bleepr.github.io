---
layout: page
title: "Scott"
description: "Chief Executive Firmware Engineer/VP Communications"
header-img: "img/post-bg-03.jpg"
---
## Firmware
The hardest part, and the most underappreciated. I am the most valuable member
of the team.

## Introduction
What makes a bleepr a man.

## Choosing the Arduino over the mbed
The mbed was crap and we didn't like it.

## Communications
The Bluetooth Low Energy (BLE) communication between the **bleepr** and the **Pi
Relay** for the prototype is quite simple. In fact, there are only 5 commands
sent from bleepr to Pi!

All commands follow a similar template; some action from the user is
sensed by the bleepr, for example, a card scan or a button press, which will prompt the bleepr
to send an asynchronous command (listed below) to the Pi. The bleepr will then
wait for the Pi to respond. If the Pi responds with the relevant signal (an OK
or some data) the bleepr will continue with the action, potentially using some
data that has been sent back. After a certain amount of time with no response,
the bleepr can display an "action failed" notice and continue back to whatever
state it was in before.


## Song name
I'm no stranger to love

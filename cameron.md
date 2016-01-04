---
layout: page
title: "Cameron"
description: "Relay"
header-img: "img/about-bg.jpg"
---
# Bleepr Relay
The relay is a network device that acts as a bridge between the Bleepr
devices and a regular TCP/IP network.  The device connects to the network
over a regular ethernet connection and connects to the bleepr devices using
Bluetooth 4.0 Low Energy.  Messages can then be forwarded from the devices
to the network and from the network to devices.

## Hardware
The Bleepr relay is based around a Rasperry Pi 2 in a custom 3D printed
case.  This has a 900MHz Quad Core ARM Cortex-A7 CPU with 1gb of RAM and is
equipped with a MicroSD card which holds the software as well as
configuration files on a FAT32 formatted partition.  This means that the
device can be easily configured by connecting the SD card to a regular
computer and modifying the configuration files.

The device has a Bluetooth 4.0 USB adapter installed for communicating
with devices.  A WiFi adapter could also be fitted to allow the relay to
connect to the network wirelessly if required, the casing has enough space
available to accommodate most small USB adapters.

The hardware can be powered from a single micro USB connection so it can be
powered from a common USB wall adapter or any other 5v power source.  A
set of passive PoE injectors could be employed to send power to the device
over the ethernet cable, this means that the relay does not need to be
placed near a power socket.

## Software
The relay runs on Linux, a special cut down distribution called DietPi is
used which comes with the bare minimum packages installed to prevent the relay
wasting system resources running services that are never used.

Supervisord is used to manage all the processes running on the device - It
starts them automatically on boot and handles restarting processes if any fail.

The main software stack is built up with three components.  The "relay" program
handles connecting to and exchanging messages to and from the bleepr devices.
It also sends messages that it recieves from the devices out to the RESTful API.

The "socket server" listens on the Websocket and receives messages addressed to
each bleepr device.  These messages are stored in a Redis key-value store which
is then read by the relay process which forwards the message on to its
destination device.

A Redis server sits between these two acting as a key value store to allow
messages to be forwarded asynchronously between the socket and the Bleepr
device.

The relay is written in Python using the pygatt library which provides a simple
interface between Python and "gatttool" which is the Linux command line utility
for communicating with Bluetooth devices using the Generic Attribute Protocol
(GATT). I had to manually override the connect() method provided by this
library to allow me to specify the "LE Address Type" argument as the default of
"public" is incompatible with the hardware we are using.

The software is entirely asynchronous - It uses GATT Notifications to receive 

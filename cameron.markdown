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
with devices.

The hardware can be powered from a single micro USB connection so it can be
powered from a common USB wall adapter or any other 5v power source.

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
each bleepr device the devices are addressed by the MAC address of the bluetooth
adapter.  These messages are stored in a Redis key-value store which is then
read by the relay process which forwards the message on to its destination
device.

A Redis server sits between these two acting as a key value store to allow
messages to be forwarded asynchronously between the socket and the Bleepr
device.

The relay is written in Python using the pygatt library which provides a simple
interface between Python and "gatttool" which is the Linux command line utility
for communicating with Bluetooth devices using the Generic Attribute Protocol
(GATT). I had to manually override the connect() method provided by this
library to allow me to specify the "LE Address Type" argument as the default of
"public" is incompatible with the hardware we are using.

The software is entirely asynchronous - It uses GATT Notifications so that when
a Bleepr device sends a message, a callback is called on the relay.  The relay
also uses Redis's publish/subscribe structure to call a callback when a certain
Redis key is modified.  This means that there are virtually no delays between a
message being sent to the relay and it being handled and forwarded on.

The relay spawns a separate process for each Bleepr device that it is servicing,
this means that it can handle multiple devices at once without having to switch
between devices.  This vastly improves performance as it means that the relay
can maintain a bluetooth connection to all of the devices at once which saves on
the rather large overhead of connecting to each device in turn which can take
up to 1 second to complete.

## Improvements
### Ability to scan for and serve devices at the same time
In order to scan for new bluetooth devices the relay's bluetooth adapter needs
to switch mode, this means that it will drop all connections to current devices.
This makes it impractical to automatically scan for devices on a regular basis
as other devices are unable to communicate with the relay while this is taking
place.  This could be solved by fitting a second bluetooth adapter to the relay
dedicated to scanning for devices, this way we can continuously scan for devices
while still servicing all of the connected devices using the other adapter. The
hardware of the relay is already capable of accommodating a second adapter so
in order to implement this all that would need done would be to fit the second
adapter and then update the software to handle this.

### WiFi support
Currently the relay must be connected to a regular wired ethernet network. While
this is relatively easy to accommodate, in some situations it may be preferable
to connect the relay to a WiFi network.  In this case a USB WiFi adapter could
be fitted inside the relay (the casing has enough space to accommodate this).
Since the relay already runs Linux it would be very simple to configure and
enable the WiFi adapter to connect to a network, the rest of the software would
work without modification.

### Power over Ethernet support
Currently the relay can be powered from any 5v supply however this means that a
power cable must be able to be run to every relay.  If this was impractical,
power over ethernet (PoE could be used) to provide power to the relay over the
same ethernet cable that it uses for its network connection.  In its simplest
form a passive system could be used where a custom "power injector" could be
placed at one end of the cable which would be connected to the 5v supply and a
simple adapter on the relay's end could be used to route the power to the
relay's regular power input, this could be implemented totally separately as a
pair of adapters without any modifications to the relay itself.  Alternatively
the relay could be modified with additional power regulation circuitry to allow
it to be powered from a standard IEEE 802.3af PoE capable network.  This would
need to be able to step the voltage down from at least 48v to the 5v required
by the relay.
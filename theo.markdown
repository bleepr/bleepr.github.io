---
layout: page
title: "Theo"
description: "I did the hardware-y things"
header-img: "img/pcb.gif"
---

## My Role
My role in the team was to determine the appropriate hardware to use for our device, come up with a design for the device, build breadboard prototypes at various stages of the course, and to construct the final device using a custom PCB and 3D printed case.

## Initial Work
During the development of our project plan, I was involved in trying to interface the mbed development board (Nordic Semiconductor NRF51-DK) with the hardware components which we wanted to use. We were able to demonstrate communication with the board from our server, and to the server from the board. This used a "bridge", which was created using a Raspberry Pi 2, to connect the board to the server. The board connected to the bridge with bluetooth, and the bridge then connected to the server over its internet connection.

When we finished developing the idea for our project, we decided to move to using an Arduino Nano as the "brain" of the device. This decision was taken as the components we wanted to use, in particular the RFID reader and the LCD display, were not readily compatible with the mbed development board and this was proving to be too costly in terms of time to remedy.

## The Hardware
The Bleepr's hardware is installed on a custom designed and printed PCB, and powered by a rechargeable USB power bank.

An Arduino Nano (ATmega328 at 16MHz) is used as a microcontroller for all of the components on the PCB. This allowed us to combine our existing experience programming Arduino-compatible devices with the many open-source libraries that exist for the hardware that we wanted to use for Bleepr.

The RFID reader that we used for the Bleepr was based on the MFRC-522 IC, a 13.56MHz contactless communication chip which operates on the ISO/IEC 14443 protocol. This, coincidentally, is the protocol used by the university's matriculation cards, giving us a readily available supply of identification cards to demonstrate the device's functionality, and also to describe how the device could be used in the university very easily. The RFID reader was connected to the Arduino Nano using an SPI interface through the PCB.

The LCD screen on the device used an SPI interface to connect to the Arduino Nano, however we had enough pins available on the Arduino that we didn't need to use one SPI interface for both this and the RFID reader, and thus didn't need to worry about enabling and disabling devices to communicate with each of them. The LCD we chose was based on the ILI9341 controller, and had a 2.2" backlit colour display with a resolution of 320 x 240. This allowed a high degree of clarity and readability and allowed us to fit a lot of information on screen at once. This LCD was chosen for its compact size and low cost.

The Bluetooth module used to connect the Bleepr to the relay was based on the HM-10 IC, which allowed communication using the Bluetooth 4.0 (LE) protocol. This module was chosen for its low cost and low energy usage characteristics. This component was simple to implement because it has its own microcontroller on board to handle setting up the correct communication protocols and to encode and decode messages. It then communicated with the Arduino as an emulated serial device, so we could simply use existing serial libraries to send and receive messages to and from the relay.

The buttons used on the Bleepr were chosen as we needed push buttons rather than toggle switches, for their low cost, and because they fit with the aesthetic design of the device. They also included precise measurements of their size, which helped in designing the 3D printed case to fit them precisely.

## Design

## Summary
In summary I was the most important team member and did all the dank shit.

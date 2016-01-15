---
layout: page
title: "Theo"
description: "Hardware Engineering"
header-img: "img/pcb.gif"
---

## My Role
My role in the team was to determine the appropriate hardware to use for our device, come up with a design for the device, build breadboard prototypes at various stages of the course, and to construct the final device using a custom PCB and 3D printed case. The hardware role naturally involved working very closely with our firmware engineer, Scott. Some of our work overlapped, but I will focus mainly on the hardware tasks that I completed to avoid duplication in our reports.

## Initial Work
During the development of our project plan, I was involved in trying to interface the mbed development board (*Nordic Semiconductor NRF51-DK*) with the hardware components which we wanted to use. We were able to demonstrate communication with the board from our server, and to the server from the board. This used a "relay", which was created using a Raspberry Pi 2, to connect the board to the server. The board connected to the relay with bluetooth, and the relay then connected to the server over its internet connection.

When we finished developing the idea for our project, we decided to move to using an Arduino Nano as the "brain" of the device. This decision was taken as the components we wanted to use, in particular the RFID reader and the LCD display, were not readily compatible with the mbed development board and this was proving to be too costly in terms of time to remedy.

## The Hardware
The Bleepr's hardware is installed on a custom designed and printed PCB, and powered by a rechargeable USB power bank.

<img src="/img/theo/pcb-physical.jpg" height="250">
*Figure 0: PCB*

An Arduino Nano (ATmega328 at 16MHz) is used as the controller for all of the components on the PCB. This allowed us to combine our existing experience programming Arduino-compatible devices with the many open-source libraries that exist for the hardware that we wanted to use for Bleepr.

<img src="/img/theo/arduino.jpg" height="250">
*Figure 1: Arduino Nano*

The RFID reader that we used for the Bleepr was based on the MFRC-522 IC, a 13.56MHz contactless communication chip which operates on the ISO/IEC 14443 protocol. This, coincidentally, is the protocol used by the university's matriculation cards, giving us a readily available supply of identification cards to demonstrate the device's functionality, and also to describe how the device could be used in the university very easily. The RFID reader was connected to the Arduino Nano using an SPI interface through the PCB.

<img src="/img/theo/rfid.jpg" height="250">
*Figure 2: MFRC-522 RFID Reader*

The LCD screen on the device used an SPI interface to connect to the Arduino Nano, however we had enough pins available on the Arduino that we didn't need to use one SPI interface for both this and the RFID reader, and thus didn't need to worry about enabling and disabling devices to communicate with each of them. The LCD we chose was based on the ILI9341 controller, and had a 2.2" backlit colour display with a resolution of 320 x 240. This allowed a high degree of clarity and readability and allowed us to fit a lot of information on screen at once. This LCD was chosen for its compact size and low cost.

<img src="/img/theo/lcd.jpg" height="250">
*Figure 3: 2.2" LCD Screen*

The Bluetooth module used to connect the Bleepr to the relay was based on the HM-10 IC, which allowed communication using the Bluetooth 4.0 (LE) protocol. This module was chosen for its low cost and low energy usage characteristics. This component was simple to implement because it has its own microcontroller on board to handle setting up the correct communication protocols and to encode and decode messages. It then communicated with the Arduino as an emulated serial device, so we could simply use existing serial libraries to send and receive messages to and from the relay.

<img src="/img/theo/bt.jpg" height="250">
*Figure 4: HM-10 Bluetooth 4.0 LE Module*

The buttons used on the Bleepr were chosen as we needed push buttons rather than toggle switches, for their low cost, and because they fit with the aesthetic design of the device. They also included precise measurements of their size, which helped in designing the 3D printed case to fit them precisely.

<img src="/img/theo/buttons.jpg" height="250">
*Figure 5: Buttons installed in device*

A speaker and RGB LED strip were also used in the device. These allowed extra notifications to be given to the user.

<img src="/img/theo/speaker.jpg" height="250">
*Figure 6: Speaker*

To power the device we used a standard rechargeable micro-USB battery pack. This allowed a considerably higher capacity than the other batteries available to us at a very good value. It also gave the advantage of being able to recharge the battery without removing it from the device. In our prototype, the USB charging cable can be pulled out of the base of the device, but a production model would be able to easily incorporate a charging port or even wireless charging capability using a standard such as Qi, which we planned to implement in the final model. This would be quite a trivial feature to implement, but would improve the usability of the device as a compatible wireless charger could be installed on the restaurant tables to keep the device charged while in use, removing the possibility that the battery could run out while in use.

## Design
We decided to arrange the interface with the buttons around the screen, in a similar fashion to an ATM, as shown in figure 7 below. This allowed buttons to be used as soft keys, not fixed to a single function but changing based on what is being showed on the screen.

<img src="/img/theo/buttons-screen.jpg" height="250">
*Figure 7: Buttons and main menu on screen*

We included space for a speaker on the bottom of the device. This allowed us to create short sound "chimes" to notify users of updates on their order and various other notifications.

The RGB LED was placed around the edge of the inside of the base of the device. This allowed the light to be diffused slightly through the clear plastic section of the device. This was used to indicate the status of the order, as well as various other notifications such as the success or failure of reading an identity card.

<img src="/img/theo/rgbled.jpg" height="250">
*Figure 8: RGB LED Strip lit up*

## Evaluation

While the device that we built was functional and demonstrated the system we intended to create well, a number of improvements could have been made. The PCB design would have been much cleaner if we had used a double-sided board, as for the RFID interface I had to install it with jumper cables because the Arduino could only use certain pins for its interface. In a production model we could have iterated through multiple different PCB designs and used a double sided board, which would have improved the neatness of the layout of the components. It also could allow us to decrease the *z*-height of the device itself, which would improve the aesthetic quality of the device.

The screen we used was very slow to write to, which meant that drawing shapes and text on it was visibly slow. This was also partly down to using a slow microcontroller. In a production model it would be nicer to use a faster microcontroller and screen, so that the interface isn't so sluggish when the user is using it. Another issue with the screen was that users instinctively thought it was a touch screen when first using the device. This was partly a result of the ubiquity of touch screens in modern phones and tablets, but also a result of our software running on the device which had the screen split into four sections, which made it seem as though the sections were large, pressable areas of the screen. In a production model we would investigate the use of a touch screen on the device, or if we didn't decide to use one we would perform more extensive user testing to determine a method of making it more obvious to the user that the screen is not touch enabled.

The implementation of the battery in the device was not very good. It was a hastily cobbled together solution, as we needed a battery which could output enough current to power the screen, LEDs, Bluetooth and speaker concurrently. We found that using standard rechargeable 9V batteries gave us very short battery life and were not able to power all of our hardware at once - the LED strip would not light fully and caused the software on the Arduino to freeze. The use of the rechargeable USB battery gave us a much higher capacity (2200mAh vs 280mAh from the 9V batteries), and allowed us to drive all of the components in the device without issue. In a production model, however, I would prefer to use a battery which fits into the device better, as we had issues getting the USB connector to fit inside the device. Another improvement to the battery as mentioned previously would be to implement a more elegant charging solution, whether that be a charging port or wireless charging technology.

The speaker we used for notifications was a very basic model. This was restricted to playing sequences of square waves, as we didn't have the capability to play any music files through the Arduino, which would have increased the cost and complexity of the design. However this wasn't a problem as we only wanted short, simple tones to be played to get the attention of the user, who would then find further information via the display. In a production model, it would possibly be nicer to use a more high-quality speaker and add capability to play sound files, so that we could add higher quality notification chimes and even options like speech notifications.

The RFID reader in the device worked as we expected, and was quite intuitive for the user as the screen gave instruction on how to log in to the device with your ID card, and feedback when your card had been accepted or rejected. We did run into trouble with it however, as it would not work if placed directly behind the screen of the device, probably because the signal would not make it through the metallic body of the screen component. We would have preferred this to work as it is the most intuitive position for the reader to be - the user expects to be able to scan their card on top of the screen, as it shows a graphic implying that the reader is placed there. However we managed to make a compromise where the reader is placed just below the screen (in the *y*-direction) in the device. The signal was able to penetrate the plastic case of the device easily, and the reader was close enough to the screen that it seemed to the user that it was actually scanning when the card was placed on the screen, even though it was actually reading from just next to the screen. In a production model I would prefer to use a more high-powered RFID reader, or use a thinner screen component, so that the reader could be placed directly behind the screen for a more intuitive user experience.

The HM-10 Bluetooth module we used performed very well. It was extremely easy to implement into our device as it just appeared to the microcontroller to be an attached serial device which could be sent messages and could have messages received from it. We didn't need to implement any of the actual Bluetooth protocol measures that we would have needed to do with other Bluetooth modules, as the HM-10 uses its own microcontroller and firmware to connect to other devices, encode messages for transmission, and decode received messages. In our prototype we bought a pre-packaged module which was connected to a breakout board, which dealt with converting voltage levels and added pins for connecting to a breadboard. In a production model I would probably use the module itself without the breakout board, and integrate the microcontroller and Bluetooth antenna into the main PCB. However for our purposes for the Bleepr prototype, the module served us very well.

The RGB LED strip was quite a late addition to our design. It is probably the least functional component in the device, however that does not mean it was not added for purely aesthetic reasons. It allowed us to make more intuitive feedback to the user on various events, such as their ID card being accepted or rejected, or order updates. The LED strip itself was simple to integrate into our hardware, running on a single pin serial connection. However it was a great addition to the device, and during informal user testing it was clear that users found the colour notifications an intuitive and pleasing addition to the device. The RGB LED strip would probably be included in the production model unchanged.

## Conclusion
I am, on the whole, very pleased with how the hardware came together for Bleepr. While there is a lot I'd like to improve about the device, as I wrote above in the evaluation, I'm still pleased with what we were able to achieve. We managed to create a device which looks good, is very functional, and performs well, in a short period of time.

If we were able to develop a more refined version 2 of Bleepr, I believe it would be a compelling device to be used in many restaurant and gastropub settings, and would be a very popular addition. The hardware that I designed and constructed is a demonstration of what is possible for this project, but I believe if we had an increased budget, in terms of both time and money, we could create a much more refined and capable device for the Bleepr system.

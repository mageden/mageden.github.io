---
layout: page
title: Pentair - Usability of Mobile Dashboard Display
---

## Project Overview

An iOS mobile application developed for monitoring aquaponic systems. Can gather data through bluetooth or direct probe input. Data is saved on the device with timestamps and then can be sent to an off-site analyst. Goal of the device is to replace current bulky mobile meters with a smaller design able to connect to multiple meters. Aquaponic systems can be extremely sensitive to small changes in the water, making the primary goal of the product to reduce user error. In order to improve the usability and decrease the potential for error, I conducted some preliminary research. This information was used to generate a wireframe for AB testing. Current and modified designs were tested for usability along a series of relevant tasks. Information from usability testing was used to improve the design and incorporated in a second iteration.

### Task analysis

| Step  | Task | Comments |
| ------------- | ------------- | ------------- |
| 1  | Open the application  |
| 1.1  | Locate application icon from iPhone home screen.  |
| 1.2  | Open application by tapping on the icon.  | |
| 2  | Select transmitter  | |
| 2.1  | Make sure the transmitter is attached to a probe and is within Bluetooth range.  |  Users may get confused on the difference between the transmitter and probe. The probe measures the required parameters (TGP, DO, etc.) while the transmitter connects the probe to the iPhone(while also measuring barometric pressure)|
| 2.2  | Wake the probe and transmitter from sleep mode   | |
| 2.3  | First screen will ask for you to select a transmitter to connect to,  | |
| 2.4 | Under the list of “Available” select the transmitter you intend to use by tapping it.  | Users may have issues identifying the correct probe if the probes aren’t already named. |
| 2.5  | The transmitter will begin automatic connection | |
| 3 | Calibrate Probe  | |
| 3.1 | A prompt will ask if you want to calibrate device, giving the option to either “Dismiss” or “Calibrate.” |  Users may go too long without calibrating the device. |
| ... | ...  | ... |

### Alternative Designs

<img src="/projects/img/pentair_old.png" width="215" style="padding:20px"/>
<img src="/projects/img/pentair_proto1.png" width="220" style="padding:20px"/>
<img src="/projects/img/pentair_proto2.png" width="222" style="padding:20px"/>

### Usability Testing

Wireframes were constructed using Envision and then tested on novice users for errors and time to completion for information inquiries. Users had trouble interpreting whether the arrows in the original prototype were saying the value needed to be increased, or was too high. Navigation in the dashboard through arrows was also confusing for most users. Adjustments were made, and the second iteration addressed these issues through swipe based navigation and only displaying if the range was within or outside of the range.

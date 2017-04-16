---
layout: page
title: Pentair - Usability of Mobile Dashboard Display
---

## Project Overview

This project was done in collaboration with Federico Scholcover and Jesse Farnsworth as a class project. We collaborated with Pentair for a little over the semester, with the goal of improving the usability of their new iOS mobile application, which was developed for monitoring aquaponic systems. The device could connect to various aquaponic probes through bluetooth or direct connection, and then the data can be associated with various saved "Sites". The new application and device are intended to replace the current bulky mobile meters, which usually can only connect directly to a single type of probe. These meters can be used either for spot checking various locations, or for the management of complex aquaponic systems.

### Task analysis

We started by conducting a task analysis, for which a brief segment is presented here. The steps of the task analysis were constructed after discussing the use and context in which the device would be handled with the Pentair team.

| Step  | Task | Possible Errors |
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

After conducting the task analysis we identified a short number of critical design flaws. The first was the ease with which users could misspecify the data they saved to an incorrect site. This mistake would be difficult to catch both at the time, as there was little redundancy, and after the fact. The other criticism was based on the fact that the individuals who would be handling these meters often weren't the experts. As the user wasn't the expert, there needed to be some way to have the device display some of what the expert knew, both to help the user interpret their results and to decrease the load of the experts from the individuals using the device.

### Alternative Designs

On the left is the dashboard of the original design. Little information is displayed about the meaning of the values, and the user had to scroll through a one dimensional wheel to view other measures when there were more than three. In the middle is the design we proposed, and subsequently tested. The primary changes were presenting more information per screen, pairing measures with indications of if they were in an acceptable range (based on expert guidance for that site), redundant information about the site currently being viewed, and additional information about the transmitter that was currently connected. On the right is the final proposed design, based on criticism from users during usability testing. The primary change is the removal of the large arrows and highlighting the currently selected value, for which additional information was displayed.

<img src="/projects/img/pentair_old.png" width="215" style="padding:20px"/>
<img src="/projects/img/pentair_proto1.png" width="220" style="padding:20px"/>
<img src="/projects/img/pentair_proto2.png" width="222" style="padding:20px"/>

### Usability Testing

Wireframes were constructed using Invision and then tested on novice users for errors and time to completion for information inquiries. Users had trouble interpreting whether the arrows in the original prototype were saying the value needed to be increased, or was too high. Navigation in the dashboard through arrows was also confusing for most users. Adjustments were made, and the second iteration addressed these issues through swipe based navigation and only displaying if the range was within or outside of the range. Overall reception towards the new design was positive, and navigation times were much faster with the modifications. No differences in error using the device were found, but this may be due to the difficulty of the tasks. 

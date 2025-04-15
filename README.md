# GPS F3X Tracker for Ethos Version 1.3 - NOTHING has been tested in real till now!

### Support RCGPS-F3x

Tested with FrSky X20 pro
Copy gpstrack & setup folder to transmitter $RADIO/script/

### Installation guide and user manual

## Contents
1. [General Description](#GeneralDescription)
2. [Requirements](#Requirements)
3. [Known limitations](#Knownlimitations)
4. [Installation](#Installation)
5. [Configuration](#Configuration)
6. [Locations.lua](#Locations.lua)
7. [Event modes](#Eventmodes)
8. [Usage on a slope](#Usageonaslope)
9. [Change log](#Changelog)
10. [Development plan](#Developmentplan)
11. [License](#License)

<a name="GeneralDescription"></a>
## 1. General Description
GPS F3X Tracker for Ethos is the LUA application for FrSky transmitters with Ethos operating system. It supports training of slope racing events (F3F). Whole competition event is supported, starting with initial 30 sec countdown sequence, crossing base A line for the first time and counting and time measuring of 10 legs. Via a GPS sensor the turn lines are identified and an acoustic signal is given while crossing them. A supported GPS sensor must be placed in the model and connected and configured to telemetry subsystem.

There are also two modes for F3B available, where in case of speed task the time for 4 legs is measured, in case of distance task number of legs done in allocated time is counted.

The application is based on ideas of Frank Schreiber's F3F Tool for Jeti and on code of Alex Barnitzke's gpsF3XTracker for OpenTx. Its code is published under the MIT License.

This manual describes how to install, configure and use the GPS F3X Tracker.

<a name="Requirements"></a>
## 2. Requirements
- GPS sensor: SM-Modelbau GPS-Logger 3, FrSky GPS ADV and generally any other GPS with 3-axes gyro and with data rate 10Hz are supported. The Logger 3 is strongly recommended as FrSky GPS ADV lacks gyro
- Ethos: versions 1.6.2 and newer are supported (previous versions have various issues in areas used by the appication)
- Transmitter: The application supports units with touchscreen resolution 800*480

<a name="Knownlimitations"></a>
## 3. Known limitations
- Due to accuracy of GPS sensors (e.g. FrSky GPS ADV has accuracy approx 2.5m CEP) and telemetry latency the turn signals are not 100% precise, but still give a good F3F-experience. Issue can be worse for F3B speed tasks
- Sometimes there is a GPS-drift of given start point. In this case the whole course might drift to left or right some meters, because the turn positions are calculated in relation to the start point
- The application supports GPS coordinates with 6 decimals (accuracy cca 10 cm)
- Max 14 fully defined and one "live" event sites are supported
- Application texts and menus are only in English. Speech announcements are given in language configured in the transmitter
- Application is resource demanding. There should not be many other widgets/system tools/task/sources running on transmitter, otherwise operation accuracy of application can be compromised. It is valid also vice versa, so other applications can be affected by F3X Tracker

<a name="Installation"></a>
## 4. Installation
Unzip the installation package and place all files into directory /SCRIPTS on your transmitter. Folders gpstraca (keeping setup part) and gpstrack (keeping operation part) should not be changed. Please note all modules, excluding locations.lua, are in the compiled form (*.luac).
Start the transmitter and configure two widgets "GPS F3X Tracker Setup" and "GPS F3X Tracker" when a target model is selected. The application is capable to partly modify size of text to size of widget windows, however, for accommodation of all information properly it is recommended to use at least for example half height & full wide layout for both setup and main widget (in such case widget titles should be switched off), or full height & half wide layout:

<img width="400" alt="image" src="https://github.com/user-attachments/assets/e4d8eca7-e725-4077-a29b-f2770170c97f" />

<img width="400" alt="image" src="https://github.com/user-attachments/assets/8fbf9129-bfb1-431f-a6fb-604375786ba1" />



<a name="Configuration"></a>
## 5. Configuration
- GPS sensor: set data rate to 10Hz = 0.1s (or higher if possible). Crosscheck names of available GPS sensors and rename, if needed. The application generally expects sensors altitude, coordinates, speed, date, satellites, accelerations:
	- SM-Modelbau GPS-Logger 3: "GAlt", "GPS", "GSpd", "Date", "GSats" (default name is "0870"!), "AccX", "AccY", "AccZ"
	- FrSky GPS ADV:  "GPS Alt", "GPS", "GPS Speed", "GPS Clock"
	- Other GPS: "GAlt", "GPS", "GSpd", "Date", "AccX", "AccY", "AccZ"

- "GPS F3X Tracker Setup" widget configuration:
	- Event place: any item from list of places in locations.lua file
	- Course direction: course bearing from the left base to the right base in degrees (*)
	- Course difference: change of standard course length (*)
	- Competition type: any type from supported types (f3f_training, f3f_competition, f3b_distance, f3b_speed, f3f_debug) (*)
	- Base A is on left: set  true  if it is so (default status) (**)
	- Lock GPS Home position switch: any 2-position switch or functional switch, mandatory

	(*) These items are available only for "Live Position & Direction event", otherwise are locked as they are determined by event information from locations.lua file

	(**) This item is available only for F3F event types, for F3B event types is Base A always on left

<img width="395" alt="image" src="https://github.com/user-attachments/assets/6cf638e7-4f24-420f-ba8c-5a5a99623477" />

- "GPS F3X Tracker" widget configuration:
	- Start race switch: any 2-position switch or functional switch, mandatory
	- Elevator output channel: mandatory only for FrSky GPS, used for simulation of accelerometer
	- Input debug GPS latitude and longitude: used for emulation of GPS input in debug mode  (suggested analog sources elevator and rudder), not mandatory

<img width="395" alt="image" src="https://github.com/user-attachments/assets/0afcba7a-6224-4a54-b183-ccbd8a749ae4" />

<a name="Locations.lua"></a>
## 6. Locations.lua
File Locations.lua, located in "/scripts/gpstrack/gpstrack" folder, keeps event location items in the format "Name of event site, home latitude, home longitude, course direction, course length difference, event type". Event types are:
- type 1: f3f training 
- type 2: f3f competition
- type 3: f3b distance
- type 4: f3b speed
- type 5: f3f debug

Default Location.lua looks like below. You can edit it as per your needs, but please do not delete the first row, which defines "live" event location. Default event type in such case is f3f training (type 1), however you can change it during configuration. Home position for “live” event location is defined from current GPS information. Please do not remove the last entry either (create new sites before that item) and keep its name "Last Entry". Max 15 event sites are supported:

    {name = "Live Position & Direction", lat = 0.0, lon = 0.0, dir = 0.0, dif = 0, comp = 1},
    {name = "Debug", lat = 53.550707, lon = 9.923472,dir = 9.0, dif = 20, comp = 5},
    {name = "Loechle", lat = 47.701974, lon = 8.3558498, dir = 152.0, dif = 10, comp = 2},
    {name = "F3B Distance site", lat = 53.333333, lon = 51.987654, dir = 19.9, dif = 0, comp = 3},
    {name = "Toftum Bjerge", lat = 56.542000, lon =  8.521000, dir = 244.0, dif = -7, comp = 1},
    {name = "F3B Speed site", lat = 53.555555, lon = 51.987654, dir = 10.9, dif = 0, comp = 4},
    {name = "Trutnov", lat = 51.234567, lon = 15.678901, dir =  10.0, dif = 40, comp = 2},
    {name = "Test site", lat = 31.212000, lon = 121.400000, dir =   0.1, dif = 1, comp = 1},
    {name = "Last Entry", lat = 0.0, lon = 0.0, dir = 0.0, dif = 0, comp = 1}

Notes:
- home latitude and longitude is for F3F events position of a center of the course
- home latitude and longitude is for F3B events position of a baseline A of the course
- course length of competition event types is defined as per F3X rules - F3F 100m and F3B 150m. F3F debug has its course length set to 30m. You can change this default course length for a particular event site via item "dif" if needed - course is longer when value is positive and course is shorter when value is negative

You can edit the file on a PC or via embedded editor:

<img width="396" alt="image" src="https://github.com/user-attachments/assets/4cc2ba3c-1c72-4042-ba66-a8a88256a971" />

Use button “Edit event place” at the bottom of the site configuration screen to enter the editor. The original screen will be replaced by a new screen allowing editing of all parameters:

<img width="396" alt="image" src="https://github.com/user-attachments/assets/c9244f6e-66f6-4ce4-acc4-27c22fc4f2e3" />

Button “Save” saves modified site as below:
    1. Creation of a new site from the “Live Position & Direction” event place – if list of sites is not full, new line with provided information will be created in the Locations table, just before the "Last Entry" line. Do not forget to change name of new event place. If list of sites is full, save operation will be refused and indicated by message "List of sites is full!"
    2. Editing of an existing site – changed parameter(s) will be written into relevant site line in the Locations table 

Editor can be closed by standard “back” widget Ethos button

Note: deletion of site lines in the Locations table is possible only via PC

<a name="Eventmodes"></a>
## 7. Event modes
The application supports F3F-competition, F3F-training, F3F-debug, F3B-speed and F3B-distance event types. They behave differently as below:
- F3F-competition: it follows F3F rules, so it begins with 30 sec timer and starts the run timer when the plane enters the competition place from outside via base A toward base B for the fist time or when the initial timer expires. It then measures time for 10 laps between bases
- F3F-training: it does not use the 30 sec and starts directly with entering the competition place from outside via base A toward base B for the fist time
- F3F-debug: similar to F3F-training, however it uses emulation of GPS input (via configured sources "Input debug GPS latitude and longitude") for simulation of flight around the set home position
- F3B-speed: at this moment only measures time for 4 laps since entering the competition place from outside via base A toward base B for the fist time. No check for the overall competition time is implemented
- F3B-distance: it measures number of laps made in 4 minutes since entering the competition place from outside via base A toward base B for the fist time 

The actual status is indicated by individual rows in the "GPS F3X Tracker" widget screen:
- Comp: "waiting for start...", "started..." - just after switching the "Start race switch" on, "canceled..." - cancellation can be done by switching off/on/off of the "Start race switch", "start climbing..." - during initial event phase (so 30 sec max), "out of course" - plane between bases, "race timer started..." - initial 30 sec expired and plane between bases, "in course..." - plane outside of bases, "timer started..." - initial 30 sec expired and plane  outside of bases
- Runtime: time used for individual event
- Course: "center", "leftOutside", "leftInside", "rightOutside", "rightInside" - distance from the center is provided
- V, Dst, H: speed, distance from the center and height
- GPS: actual GPS position
- Runs: list of last events of the same type with their runtime

<img width="346" alt="image" src="https://github.com/user-attachments/assets/b5b4293e-1c30-43bd-8200-6f2051d68252" />

Announcements and sounds: 
- Beep after switching the "Start race switch" on
- Initial F3F timer countdown announcements: 30, 20, 10, 5, 4, 3, 2, 1 sec
- F3B-distance timer countdown announcements: minutes and every 10 sec for last minute
- Beeps when crossing base, tone based on condition
- Lap time announcements on even laps for F3F-traning event type
- Overall runtime at event end for F3F-x event types

<a name="Usageonaslope"></a>
## 8. Usage on a slope
- Switch on RC system and give the GPS sensor enough time for initiation and satellite detection (it can take 30+ seconds)! Open  GPS F3X Tracker Setup  widget and select "Live Position & Direction event place" or any pre-configured place.
- For F3F and "Live Position & Direction event place":
	- Set "Competition type" and "Base A is on left" configuration items
	- Go with your model to the center of the course
	- Wait for stable information in the "GPS" row in the "GPS F3X Tracker Setup" widget screen
 	- Take the cardinal direction from the left base perpendicular to the right base and set it to the "Course direction" item
	- Lock the position with the "Lock GPS Home position switch" - such status will be indicated by change of item name to "GPS Home lck" 
	- Now your flight configuration is ready, you do not start from the exact home place

- For F3B and "Live Position & Direction event place":
	- Set "Competition type" configuration item
	- Go with your model to the baseline A of the course
	- Wait for stable information in the "GPS" row in the "GPS F3X Tracker Setup" widget screen
	- Take the cardinal direction from baseline A perpendicular to baseline B and set it to the "Course direction" item
	- Lock the position with the "Lock GPS Home position switch" - such status will be indicated by change of item name to "GPS Home lck" 
	- Now your flight configuration is ready

- For other F3F/F3B event places (pre-configured in the Location.lua file):
	-Your flight configuration is ready - all parameters, excluding "Base A is on left" are set in the Location.lua file

- Go to the "GPS F3X Tracker" widget screen
	- The initial status is indicated by statement "waiting for start..." 

<img width="329" alt="image" src="https://github.com/user-attachments/assets/659d80f2-1ac1-4279-b225-70fb40e7d9e5" />

- Start new event with the "Start race switch"

<a name="Changelog"></a>
## 9. Change log
V1.1:
- List of locations in the file Locations.lua has been enhanced by item "dif", which modifies default course length (F3F 100m, F3B 150m), positive number: course is longer, negative number: course is shorter
- Number of visible GPS satellites is available on “GPS F3X Tracker Setup” widget screen (only for SM-Modelbau GPS-Logger 3)
- Error in timestamp function has been fixed

V1.2: improved management of fonts

V1.3: created function for editing location table in the locations.lua

<a name="Developmentplan"></a>
## 10. Development plan
The application hasn't been thoroughly tested so far and it is highly probable there will be necessary to change or enhance some parts. Do not hesitate to comment and come with ideas. At this moment we plan:
- Implement position estimation function, if tests will show it is needed

<a name="License"></a>
## 11. License
Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

Copyright © 2025 Milan Repik


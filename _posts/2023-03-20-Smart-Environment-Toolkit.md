---
title: Smart Environment Toolkit
date: 2023-03-20
categories: [University Projects, 3rd year]
tags: [java, c++, python, flask, grafana, influx, sql, microbits]  # TAG names should always be lowercase
---

Note this project was completed in a group of 3 people with a rotating scrum master between each 3 of us and my role was everything except front end (I made the graphing page for the front end but that was just embedding grafana through an iFrame) and I didn't really work on the microbits embedded side of things this time unlike the [previous project (Smart Lab)](https://michael-perdue.github.io/posts/Smart-Lab/) that I did work on the microbits extensively and this projects extends that previous project. So I was focused on the Base Station, API, InfluxDB, MySQL DB and grafana for this project.

# The structure of the backend of the system

Here is a diagram of our backend structure:

![](https://michael-perdue.github.io/assets/ToolkitDiagram.jpg)

After the first project the [Smart Lab](https://michael-perdue.github.io/posts/Smart-Lab/) we already had a extensive idea on the limitation of a microbit and how to best utilise the radio of the microbits. So the microbits communicate with each other over radio on a specified band and radio group then the all the sensor microbits will find the closest basecamp microbit and this basecamp then sends this data over serail to the base station. The base station (a java progrma continually running on the pc) then processes this data, filters our junk messages and sends the data to the API through the appropriate routes. The REST API (written in python) will then process this data and if its specific data points it will send it to influxDB (which is what influx was built to store) through flux statements and if it is anything relating to roles of the microbits then it will be stored into the MySQL DB. Grafana also uses influxDB and using flux statements combines the MySQL data which was a much nicer way of dealing with both DB's then using Grafana's built in functions which don't particularly work. The api and website both auto deploy through a docker container on the VPS which lets us easily update the live API and Website with just a click of a button. The front end website then gets various sets of data, graphs etc all through the API routes and it can manage the microbits and the whole system through these different routes. The website lets you add and remove users, set roles, change the microbits roles, change the structure of the environment.

This brings me onto the logical structure of the system which can be seen in this diagram below:

![](https://michael-perdue.github.io/assets/SET-structure.png)

This structure allows for the smart environment to work for any environment as the end user can define what a zone is, how many zones they have and how many areas each zone has. This system also supports as many walkers, doors and sensor microbits the only requirement for rooms is that there is atleast one basecamp in any room you have a sensor for. However if you don't need to have a sensor in the room then a room can exist without any basecamps thus a room can exist without any microbits. This is all then managable from the front end so you can delete,add and change rooms, areas and zones. In addition you can also assign,unassign and change the role (so sensor to door to walker etc) of all microbits.


## Microbits
The code for the microbits is written in C++ and is written using the [codal library](https://github.com/lancaster-university/codal-microbit-v2) and this works by first writing the code in C++ then compiling that code to hex through codal and then flashing that hex onto the microbits 

So our setup is similar to last time however due to a better knowledge of the microbit radio we no longer had to use atomic multicast like we did on the previous project which in turn decreased the amount of processing and lost packets of the microbits. The walker microbit is used for opening doors and as basically a location/room current capacity tracker and it itself does not need to communicate with the basecamp. When the walker wants to open the door the door will contact the base camp and the base camp will write over serial to the base station the packet and the base sation will turn it into API request which will return the results of if it can open the door and that packet will be sent do the door. The walker then also send a message to the nearest sensor saying that it is still alive every 10 seconds so that the sensor can then forward on the information and keep live tracking of its location. In addition the sensor will also send the temperature, noise and light reading every 3 seconds. 

The microbits when turned on use a simple dhcp which is that they will send a request to the basecamp for its ID and role, this is then written over serial and a request to the API is made which looks up a MySQL table that has all the serial ids of microbits along with a user friendly ID (starts at 1 and increments after each new microbit) and the role, this is then returned over serial and a packet is sent. The microbit will then flash If the microbit has no role then it will send a keep alive request every 30 seconds and when that request comes in it will check if a new role has been given. You then know what microbit is giving what data as they send along a little bit of meta data saying the size of the packet, the message id, the packet id, the microbit id then the data. The message id would then be used for making sure messages are only proccessed once by as they are a randomly generated 32 uint number.

## Base Station
The Base Station is effectively responsible for the turning bytes sent from a microbit serially into the correct data and then calling the relevant API routes to ensure that the data is handled correctly and stored if needed. The code itself is written in java and is event driven following the [observer design pattern](https://refactoring.guru/design-patterns/observer) which is shown in the class diagram below where the serial manager class will read packets and report them to the packet manager and then this packet manager will loop through all listners and report to them if they are subscribed to that packet type that one has appeared, the listeners on creation need to register themselves as a listener by calling the method in the packet manger class. 



Below is a class diagram of the base station, note doted line means it iteracts/uses that classes methods or properties, the blue line dictates classs inheritance, the green line is when a class is implementing a intergace and a white solid line is when a class must contain a specific amount of instances of another class:

![](https://michael-perdue.github.io/assets/ToolKitClass.png)

## API

## Database

# The systems features

## Demo of overall system

Here is a video of the website working from a phone:

[![Mobile Demo](https://img.youtube.com/vi/edvk2hz0MMY/0.jpg)](https://www.youtube.com/watch?v=edvk2hz0MMY&t "Mobile Demo")

Here is how the system looks from the hardware sides of things:

![](https://michael-perdue.github.io/assets/AllToolkit.gif)

In the demo above you can see the system in practive where each of the clusters are a room and its microbits asocciated with that room (note in practice and it has been tested in much bigger environments but for the sake of recording that is not possible to show). The computer then which has a base camp microbit attached to it and then this bace camp continually flashes B to let you know it has just processed some messages. Then on the actual computer proccessing the messages you can see it has a console window which is prints the amount of messages proccessed a second, the microbits that are connecting and disconnecting to this basecamp and any errors. Finally the computer shows the of the homepage dashboard of the website with live data.

## Realtime environmental data

Each microbit sensors records the temperature, light and noise level through the built in hardware of a microbit. The temperature value is measured in celcius, however both light and noise are measured from an arbiturary value so light is just on a scale of 0 to 255 and noise is just a reading in the 9000s so we coverted these to be thresholds meaning if its below x then display that its 'quiet' or for lights its 'dark'. Then each of these are displayed on the website home page under the zone, area and room the sensor is located in.

Finally the other evnrionmental data that we took the capacity of each zone, and this was effectively just the amount of people with microbits in each zone. This worked off each walker sending out pings to other sensors to find the closest microbit then this location would be reported back every 5 seconds and this is then be stored and the api only returns the capacity from records in the last 5 mins to ensure non-active microbits which could have left the zone and are no longer near any zones aren't still being recorded. No walker is ever counted more then once as it goes of its id and finds the last record and only the last one.

## Light actuation

Here is a video of the a microbit being made a sensor then turning that sensors light on:

![](https://michael-perdue.github.io/assets/LightToolkit.gif)

## Door unlocking and locking

Here is a video of the a walker trying to open a door they dont have permission to, then giving them permission and them opening the door:

![](https://michael-perdue.github.io/assets/DoorToolkit.gif)

## Graphs of live and historic data

## Fully managable system from the front end

### System structure management

### User management

### Microbit management


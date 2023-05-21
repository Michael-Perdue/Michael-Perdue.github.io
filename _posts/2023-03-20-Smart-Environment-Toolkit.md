---
title: Smart Environment Toolkit
date: 2023-03-20
categories: [University Projects, 3rd year]
tags: [java, c++, python, flask, grafana, influx, sql, microbits]  # TAG names should always be lowercase
---

Note this project was completed in a group of 3 people with a rotating scrum master between each 3 of us and my role was everything except front end (I made the graphing page for the front end but that was just embedding grafana through an iFrame) and I didn't really work on the microbits embedded side of things this time unlike the [previous project (Smart Lab)](https://michael-perdue.github.io/posts/Smart-Lab/) that I did work on the microbits on extensively and this projects extends that previous project. So I was focused on the Base Station, API, InfluxDB, MySQL DB and grafana for this project.

# The structure of the backend of the system

Here is a diagram of our backend structure:

![](https://michael-perdue.github.io/assets/ToolkitDiagram.jpg)

After the first project the [Smart Lab](https://michael-perdue.github.io/posts/Smart-Lab/) we already had a extensive idea on the limitation of a microbit and how to best utilise the radio of the microbits. So the microbits communicate with each other over radio on a specified band and radio group then the all the sensor microbits will find the closest basecamp microbit and this basecamp then sends this data over serail to the base station. The base station then processes this data, filters our junk messages and sends the data to the API through the appropriate routes. The API will then process this data and if its specific data points it will send it to influxDB (which is what influx was built to store) through flux statements and if it is anything relating to roles of the microbits then it will be stored into the MySQL DB. Grafana also uses influxDB and using flux statements combines the MySQL data which was a much nicer way of dealing with both DB's then using Grafana's built in functions which don't particularly work.

## Microbits
So our setup is similar to last time however due to a better knowledge of the microbit radio we no longer had to use atomic multicast like we did on the previous project which in turn decreased the amount of processing and lost packets of the microbits. The walker microbit is used for opening doors and as basically a location/room current capacity tracker and it itself does not need to communicate with the basecamp .

## Base Station

## API

## Database

# The systems features

## Demo of overall system

## Realtime environmental data

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


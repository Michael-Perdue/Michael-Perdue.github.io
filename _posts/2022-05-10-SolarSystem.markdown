---
title: SolarSystem
date: 2021-11-3
categories: [University Projects, 2nd year]
tags: [java]     # TAG names should always be lowercase
---

Using Java I have created a solar system model where you can modify the system to remove planets, moons and astroid belts. You can also add new planets and moons with their own user defined distance from the sun, angle, size, speed, colour and if they orbit any planets

**The base solar system**

The base solar system model is not 1 for 1 a replica of solar system but is roughly similar given that the difference between the biggest and smallest planet is 43x so for the sake of the model not being massive ive made the differences much smaller. The model includes all 8 planets and their major moons(no more then 5 moons per planet) it also includes saturns rings and the astroid belt that is between mars and jupitor that is randomly generated each launch so it never looks completely the same. The way that the system works is that it has all the space objects (planets, moons etc) in a arrayList that it continually loops through and calls move on each space object, upon move being called it will get its new location based of the speed and the angle of the object, it will then place/draw the object in reference to the object that it orbits as all space objects orbit something other that sun which just orbits 0,0. This is then drawn on the screen using Graphics2D (the univeristy of Lancaster provided this part of the code).

The solar system can also be sped up or slowed down through the intial command line arguement (must be greater than 0) and you can also zoom into the solar system (must be greater than or equal to 1) through the initial command line arguement

`java Driver "speed" "zoom" `

Below is the program running with the command `java Driver 1 1`

![](https://michael-perdue.github.io/assets/SolarSystem.gif)


**Adding and removing planets, moons and Asteriods**

When it comes to removing and adding space object it is to as when the program is launched another window pops up with 7 text boxes where you just need to enter the name of the planet,moon or asteriod that you want to remove and click remove. It is important to note that upon removing a planet or any space object that has another object orbiting it, the objects that orbit the removed planet will also be removed. For adding planets or moons you need to enter the name, distance (from the object it orbits), size, speed, colour and the object that it orbits. These newly added planets and moons can be removed by entering the name and clicking remove.

Below is a demo of the adding and removing of planets and moons

![](https://michael-perdue.github.io/assets/SolarSystem2.gif)

---
title: SolarSystem
date: 2021-11-3
categories: [University Projects, 2nd year]
tags: [java]     # TAG names should always be lowercase
---

**Project Overview:**

I have developed a solar system model using Java that offers you the flexibility to manipulate the system by removing planets, moons, and asteroid belts. Moreover, you can introduce new planets and moons, each customizable with their user-defined attributes such as distance from the sun, angle, size, speed, color, and orbital relationships.

### The Base Solar System

The base solar system model, while not a precise 1-to-1 replica of our solar system, closely resembles it. To maintain a manageable model size, I've scaled down the differences between the largest and smallest planets by a factor of 43. The model includes all eight planets along with their major moons (with a maximum of five moons per planet). Additionally, it incorporates Saturn's rings and the asteroid belt located between Mars and Jupiter. Remarkably, the asteroid belt is randomly generated with each launch, ensuring a unique appearance every time.

### System Mechanics

The simulation operates by managing all space objects, including planets and moons, within an ArrayList. It continuously iterates through these objects, invoking the "move" function on each. When "move" is called, a space object calculates its new location based on its speed and angle. Subsequently, it positions itself in reference to the object it orbits, as all space objects orbit something other than the sun, which orbits [0,0]. The rendering of this simulation is achieved using Graphics2D (provided by the University of Lancaster).

You have the option to control the passage of time within the simulation. You can accelerate or decelerate the simulation via initial command-line arguments (speed > 0). Furthermore, you can zoom in on the solar system (zoom >= 1) using these command-line arguments:

```bash
java Driver "speed" "zoom"
```

### Visual Demonstration

Below is a visual demonstration of the solar system simulation in action, using the command `java Driver 1 1`:

![Solar System Simulation](https://michael-perdue.github.io/assets/SolarSystem.gif)

## Customization

The simulation allows for further customization by adding or removing celestial bodies.

### Adding and Removing Celestial Bodies

> Removing a space object removes all orbiting objects
{: .prompt-info }

To remove space objects, simply launch the program. Another window will appear with seven text boxes. You need to enter the name of the planet, moon, or asteroid you wish to remove and then click "remove." It's important to note that when removing a planet or any space object with other objects orbiting it, those orbiting objects will also be removed.

To add planets or moons, provide details such as the name, distance from the object it orbits, size, speed, color, and the object it orbits. These newly added celestial bodies can be removed by entering their names and clicking "remove."

### Visual Demonstration

Here's a demonstration illustrating how you can add and remove planets and moons within the simulation:

![Adding and Removing Celestial Bodies](https://michael-perdue.github.io/assets/SolarSystem2.gif)

**Closing Thoughts:**

This project reflects my early Java programming skills which has only been built upon and my ability to create interactive simulations.
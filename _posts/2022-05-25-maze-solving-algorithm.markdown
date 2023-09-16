---
title: Maze Solving Algorithm
date: 2021-11-7
categories: [University Projects, 2nd year]
tags: [java, swing]     # TAG names should always be lowercase
---

**Project Overview:** 

I developed a **Java** Maze Solving Algorithm displayed visually in **Swing**. For this project I was provided with how to generate mazes through Lancaster University and then I was tasked with creating an depth-first search algorithm to find the shortest path from a starting point to an exit, highlighting the solution and letting the user continually select different routes.

## How the maze solving program works

### Program Structure
This program is built using Swing and generates a 20x20 grid of buttons, each containing a random number of walls (around 500 by default). You can increase the number of walls by adding an optional argument `java Driver <number>`, where `number` specifies the additional walls you want to add.

### Usage
1. **Setting Start and End Points for initial path**: You can choose the starting point by left-clicking on a tile and set the exit point by right-clicking. Once both starting and ending points are set, the shortest path will be generated. The path will be highlighted, and its length will be displayed in the console.

2. **Creating a New Path**: To find a new path, simply set the start and end points again, and the previous highlighted path will be removed.

## The Recursive Algorithm

### Algorithm Overview
The algorithm employed is a depth-first pathing algorithm with elements inspired by *[Dijkstra's algorithm](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)*, where it stores the previous quickest way to reach a node. It begins as a depth-first search from the starting point, checking each possible direction (up and right) until it cannot proceed further. If it reaches a dead end, it backtracks to the previous call made on the previous tile.

### Optimizing Performance
Each time a tile is reached, the program stores the length taken to reach that tile. When attempting to move in a direction, it checks if the current path length is shorter than the tile's smallest path, significantly enhancing performance. This logic also prevents the algorithm from getting stuck in loops.

### Handling Unsolvable Mazes
If no path is possible, the program will print to the console that it is impossible to create a path.

### Visual Demonstration
Below is a visual demonstration of the maze-solving program in action:

![](https://michael-perdue.github.io/assets/maze.gif)

**Closing Thoughts:** 

Developing the Maze Solving Algorithm was an engaging and rewarding project completed during my second year at university, under a tight one-week deadline. It provided an opportunity to apply my Java programming skills to create a practical tool for solving mazes.

While this algorithm effectively finds the shortest path, I later expanded my pathfinding knowledge with the development of the A* pathing algorithm for the Campus Crawling Game, which you can explore [here](https://michael-perdue.github.io/posts/campus-crawl-game-and-engine/#enemy-movement-and-a-pathing).
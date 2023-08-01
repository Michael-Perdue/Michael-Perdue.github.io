---
title: Maze Solving Algorithm
date: 2021-11-7
categories: [University Projects, 2nd year]
tags: [java]     # TAG names should always be lowercase
---

## How the maze solving program works
This program is made with swing Ui and generates a 20x20 grid of buttons each which have a random amount of walls around 500 by default. You can increase the amount of walls by adding an optional argument `java Driver <number>` where number refers to the amount of walls you want to add. You can then choose the starting point by left clicking a tile and then set the exit point by right clicking, upon a starting and end point being both set the shortest path will be generated and the path will be highlighted + the length of the path will be printed into the console. Then as soon as you want to make a new to find a new path just set the start and end point again and the previous highlighted path will be removed.

## The recursive algorithm for solving the mazes quickest path
The way in which the algorithm worked is a depth first search starting from the starting point it checks if it can go up then keeps going up until it cant so it checks right and repeats this process until it cant move at which point it will return to the previous call which was on the previous tile. Each time a tile is reached the length taken to get to that tile is stored so that when trying to go in a direction it will check if the current path length is smaller then the tiles smallest path which massively increases performance. This then deals with any looping as if the path tried to go to a tile it's been to then it will have a greater path then that tiles path so it wont be able to loop and get stuck. If no path is possible then it will print to the console that it is impossible to create a path. To see a more efficient path finding algorithm take a look at the A* pathing alorithm that I created for the Campus crawling game [click here](https://michael-perdue.github.io/posts/campus-crawl-game-and-engine/#enemy-movement-and-a-pathing)

Below is demo of the program working

![](https://michael-perdue.github.io/assets/maze.gif)
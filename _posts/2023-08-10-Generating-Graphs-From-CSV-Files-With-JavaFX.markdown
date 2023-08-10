---
title: Generating Graphs From CSV Files With JavaFX
date: 2023-08-10 
categories: [Additional Projects, Generating Graphs From CSV Files With JavaFX]
tags: [java]     # TAG names should always be lowercase
---

# TLDR of the project

This project was made to help get me familiar with JavaFX. It gets the names of all csv files in the data folder of the project and lets you select which ones you want to use for the data of a graph. There are 6 different types of graphs - line chart, bar chart, stacked bar chart, scatter chart, area chart and stacked area chart. Each of these graphs has their own tab and remebers your graph/settings when you click onto another tab so you don't have to worry about remaking a graph if you quickly switch graphs. For each of these graphs you can select the csv files you want to the x and y axis names and the title of the graph. Each data point has a tool tip which means you can hover over the data point to see its value. Finally you can save all of the graphs to pngs and it will save the graph with the name of the title to your downloads folder. For more indepth break down of the system you can look at the sections below. [The code for the project is located at the github repo here](https://github.com/Michael-Perdue/Graphing-javafx).

Below is a gif of the whole system working:

![](https://michael-perdue.github.io/assets/graphing-demo.gif)
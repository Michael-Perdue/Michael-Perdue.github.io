---
title: Generating Graphs From CSV Files With JavaFX
date: 2023-08-10 
categories: [Additional Projects, Generating Graphs From CSV Files With JavaFX]
tags: [java]     # TAG names should always be lowercase
---

# TLDR of the project

This project was made to help get me familiar with JavaFX by using its XYCharts. It gets the names of all CSV files in the data folder of the project and lets you select which ones you want to use for the data of a graph. There are 6 different types of graphs - line chart, bar chart, stacked bar chart, scatter chart, area chart and stacked area chart. Each of these graphs has its own tab and remembers your graph/settings when you click onto another tab so you don't have to worry about remaking a graph if you quickly switch graphs. For each of these graphs, you can select the CSV files you want to the x and y-axis names and the title of the graph. Each data point has a tooltip which means you can hover over the data point to see its value. Finally, you can save all of the graphs to PNG's and it will save the graph with the name of the title to your downloads folder. For a more in-depth breakdown of the system, you can look at the sections below. [The code for the project is located at the github repo here](https://github.com/Michael-Perdue/Graphing-javafx).

Below is a gif of the whole system working:

![](https://michael-perdue.github.io/assets/graphing-demo.gif)

# System Features

## Overall system structure

Below is a class diagram of the classes in the program:

![](https://michael-perdue.github.io/assets/graphing-classes.PNG)

## Handling CSV files and extracting data

### Detecting the files

The program works and populates graphs through data in CSV files. There is a data folder under the resources folder, this is where the program reads the CSV files from and the data in the CSV file should be just 2 values on each line with a comma separating them (see the next section for how errors are dealt with). Each of the file names is added to a tickbox list and when the user ticks a box the data is added to the chart, then on unticking a box the data is removed from the graph. 

### Handling errors from poorly formatted files

A file must be a CSV file and formatted as in the example below: 
```
datapoint-y-1,datapoint-x-1
datapoint-y-2,datapoint-x-2
```


### Extracting the data 

When the program is reading the data it also calculates the min and max values of the x-axis to ensure that the graphs look nice. The reason why the x-axis specifically is taken is due to the step normally being not a round number whereas the y-axis will typically be a number that is round and goes up by a set amount. So the min and max of the x are taken to work out the step for the x-axis which is the max taken away from the min, but it also is rounded to the nearest nicely formatted number be it a single decimal point, a whole number or to the nearest 5. This is then given to the XYChart that javaFX provides. The code for calculating the step is as below:
```python
double xstep = Math.ceil((xMaxMin[1] - xMaxMin[0]) /30) > 5 ? Math.ceil(((((xMaxMin[1] - xMaxMin[0]) / 30) + 5) /10)*10) : Math.ceil((xMaxMin[1] - xMaxMin[0]) /30);
NumberAxis xAxis = new NumberAxis(Math.ceil(xMaxMin[0]),Math.floor(xMaxMin[1]),xstep);
```
The second line changes depending on the chart as its specifying what the lowest and highest x value on the chart can go to, so for line charts it's just the max and lowest number as the flooring and ceiling of the values provide a little bit of space which aesthetically makes sense for line charts. However, for scatter charts, the min has the step taken away from it and the step added to the max as aesthetically it makes more sense for there to be a bigger gap. This is one of the reasons why loadchart method in the charts class is abstract as each graph has at least a slight difference in configuration.

## Adding tooltips to all the data points

## Examples of each 6 types of graphs

## Saving graphs to png


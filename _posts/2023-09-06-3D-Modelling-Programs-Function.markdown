---
title: 3D Modelling Program Functionality
date: 2023-09-06 
categories: [Additional Projects, 3D Modelling Program with JavaFX]
tags: [java, javafx, csv, modelling]     # TAG names should always be lowercase
pin: true
---

# TLDR of the project

This project was made to further my knowledge of JavaFX after the [graph generating program](https://michael-perdue.github.io/posts/Generating-Graphs-From-CSV-Files-With-JavaFX/) and this page covers the end-user functionality side of it. This program only uses JavaFX and no other external libraries. It is a 3D modelling program that lets you add cuboids and spheres to a blank screen and then you can translate, rotate, duplicate, set the material and more. The program works by having a bar of buttons that offer various functions from rotation, duplication, configuration and deletion of selected objects, creation of new 3D objects, setting the lighting and more. In addition, you can change the view of the model/objects by dragging with the mouse to rotate or pan the screen, changing zoom with the scroll wheel and you can move about, create and delete lightboxes to change the lighting. This is done by using [JavaFX's Shape3D](https://docs.oracle.com/javase/8/javafx/api/javafx/scene/shape/Shape3D.html) which is pretty primitive for the purposes I wanted to use it for and mainly only provides a rendered 3D object, for more on how I adapted it and coded the program [click HERE](). If you want to see the source code then [the code for the project is located at the github repo here](https://github.com/Michael-Perdue/3D-Modelling).

Below is a video of the whole system working:

{% include embed/youtube.html id='GmSJv23EQBg' %}

# System Features


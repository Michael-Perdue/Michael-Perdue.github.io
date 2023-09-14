---
title: 3D Modelling Program Functionality
date: 2023-09-06 
categories: [Additional Projects, 3D Modelling Program with JavaFX]
tags: [java, javafx, csv, modelling]     # TAG names should always be lowercase
pin: true
---

# TLDR of the project

This project was made to further my knowledge of JavaFX after the [graph generating program](https://michael-perdue.github.io/posts/Generating-Graphs-From-CSV-Files-With-JavaFX/) and this page covers the end-user functionality side of it. This program only uses JavaFX and no other external libraries. It is a 3D modelling program that lets you add cuboids and spheres to a blank screen and then you can translate, rotate, duplicate, set the material and more. The program works by having a bar of buttons that offer various functions from rotation, duplication, configuration and deletion of selected objects, creation of new 3D objects, setting the lighting and more. In addition, you can change the view of the model/objects by dragging with the mouse to rotate or pan the screen, changing zoom with the scroll wheel and you can move about, create and delete lightboxes to change the lighting. This is done by using [JavaFX's Shape3D](https://docs.oracle.com/javase/8/javafx/api/javafx/scene/shape/Shape3D.html) which is pretty primitive for the purposes I wanted to use it for and mainly only provides a rendered 3D object, for more on how I adapted it and coded the program [click HERE](). If you want to see the source code then [the code for the project is located at the github repo here](https://github.com/Michael-Perdue/3D-Modelling).

Here is a video of me making a dice from scratch in the program. The creation of it is sped up by 7x making the total time to make the dice from scratch just under 8 mins with my program:

{% include embed/youtube.html id='qFn9ltRbty8' %}


Below is a video of the whole system working and systematically showing each feature :

{% include embed/youtube.html id='GmSJv23EQBg' %}

# Program features

## Editing shapes

### Selecting shapes  

> You can select an object by left clicking it with the mouse.    
You can deselect an object by left clicking it with the mouse again.   
You can select multiple obejects at once.
{: .prompt-tip }

To select an object you can left-click it with the mouse and this will then cause a transparent blue outline to appear around the object. You can select multiple objects at once. This object will then be affected when you click some of the buttons ranging from configuration to rotation and all the selected objects will then be affected. To unselect an object you just have to left-click the object again with the mouse and it will be deselected making the blue outline disappear.

![](/assets/3D-Selecting.gif)

### Rotating shapes

> To rotate on the x axis press ***s*** or ***down arrow*** to decrease x axis and press ***w*** or ***up arrow*** to increase x axis rotation.       
To rotate on the y axis press ***a*** or ***left arrow*** to decrease y axis and press ***d*** or ***right arrow*** to increase y axis rotation.       
To rotate on the z axis press ***q*** to decrease z axis and press ***e*** to increase z axis rotation.   
{: .prompt-tip }

You must first click the button labeled *'Rotate'* and then to rotate an object you first need to select it then you can press the corresponding key mentioned above to rotate it. Multiple objects can be rotated at a time and any rotated object will stay at the angle it is rotated to until you change it. You can stop rotating by deselecting the object, clicking the *'Rotate'* button or just stop pressing the key. 

![](/assets/3D-Rotate.gif)

### Translating/Moving shapes

> To translate on the x axis press ***a*** or ***left arrow*** to decrease x axis position and press ***d*** or ***right arrow*** to increase x axis position.           
To translate on the y axis press ***s*** or ***down arrow*** to decrease y axis position and press ***w*** or ***up arrow*** to increase y axis position. 
To translate on the z axis press ***q*** to decrease z axis position and press ***e*** to increase z axis position.   
{: .prompt-tip }

You must first click the button labeled *'move'* and then to move an object you first need to select it then you can press the corresponding key mentioned above to move it. Multiple objects can be moved at a time and any moved object will stay at the position it is moved to until you change it. You can stop moving by deselecting the object, clicking the *'Move'* button or just stop pressing the key. 

![](/assets/3D-Translating.gif)

### Configuring shapes

![](/assets/3D-Configure.gif)

### Duplicating shapes

![](/assets/3D-Duplicating.gif)

### Deleting shapes

![](/assets/3D-Deleting.gif)

## Creating shapes 

### Creating Cubiods

To create the cuboid you just need to click the button labeled *'Add Square'* and then fill out a configuration box with the details of the cuboid you want to create. You can create cuboids of any dimension (depth width or height), at any position and rotation. In addition, you can select which material you want from wood, cobble, none (plain white) and a material called lightbox which is a slightly transparent black. Finally, you can make the cuboid a lightbox which means it gives off a light. 

Here is the configuration box that appears when the *'Add Square'* button is clicked.
![](/assets/3D-Square.png)

### Creating Spheres

To create the sphere you just need to click the button labeled *'Add Sphere'* and then fill out a configuration box with the details of the cuboid you want to create. You can create spheres of any radius, at any position and rotation. In addition, you can select which material you want from wood, cobble, none (plain white) and a material called lightbox which is a slightly transparent black. Finally, you can make the sphere a lightbox which means it gives off a light. 

Here is the configuration box that appears when the *'Add Sphere'* button is clicked.

![](/assets/3D-Sphere.png)

## Camera

### Zooming in with the camera

> You can zoom in by scrolling forward with the mouse and zoom out by scroll back with the mouse
{: .prompt-tip }

You can zoom in to see more detail at any point by scrolling forward with the mouse and vice versa to scroll out. If you zoom in too close and you clip into the item then the item will no longer be visible, the same is also true if you zoom out too far then the objects will at some point no longer be visible. 

![](/assets/3D-Zoom.gif)

### Rotating camera

> By default the camera is in rotate mode
{: .prompt-info }    
> You must be rotate mode and then just hold right click and move your mouse to rotate the screen
{: .prompt-tip }

To be able to see the model at different angles you can rotate the camera in any direction effectively as if the camera was on the surface of a sphere with the center being the 0,0 by default but you can drag the camera to change this. By default, you are in the rotate mode so you just have to hold right-click and move the mouse about to rotate the screen. Once you no longer want to rotate just stop holding the right click and the angle will stay at whatever position you stop at.

![](/assets/3D-ScrollRotate.gif) 

### Dragging camera

> You must be drag mode and then just hold right click and move your mouse to drag the screen
{: .prompt-tip }

While rotating the camera is great to see the model at different angles you might want to move the camera up, down or sideways in case the model looks off-center or you just want to pan around the model. So to enable this I made a drag camera mode, by default when loading the program the camera is in rotate mode but clicking the button labeled *'drag mode'* will switch mode. Then you just have to hold right click like you would with rotate and just move the mouse about to drag the screen. Once you no longer want to drag just stop holding the right click and the position will stay at whatever position you stop at. To leave the mode and go back to rotating you just have to click the same button which will now be titled *'rotate mode'* and it will switch back to rotation mode.

![](/assets/3D-DragReset.gif)

### Resetting camera

As my program does not have different colored lines for each axis that you can translate the shape along or rotate the shape along you need a point of reference. Therefore I made a button labeled *'reset camera'*, what this does is reset the camera orientation to remove any rotation applied by the user, as sometimes you can be moving the shape via the x-axis but you actually want to move it via the y-axis, its just the orientation of the camera is confusing. Ideally, the program in a future update should have a blue, green and red line for each axis so that you know what direction what axis is, however currently I have not implemented the multi-color lines as references.

## Lighting

### Adding lightboxes

### Removing lightboxes

### Hiding all lightboxes

### Disabling all lightboxes

---
title: 3D Modelling Program Structure and code
date: 2023-09-06 
categories: [Additional Projects, 3D Modelling Program with JavaFX]
tags: [java, javafx, modelling, design patterns]     # TAG names should always be lowercase
---

**Project Overview**:

After my graphing project I really wanted to push the capabilities of JavaFX in the realm of 3D modeling. Although JavaFX is primarily known for its 2D graphics and user interfaces, I sought to leverage its features creatively to build a 3D modeling application. This page covers how I leveraged it rather then listing and showing the functionality.

For more on the functionality of the 3D modelling program and demo's go to [this page](https://michael-perdue.github.io/posts/3D-Modelling-Programs-Structure/)

For the source code please visit the [GitHub repository](https://github.com/Michael-Perdue/3D-Modelling).

## Code Structure

Each classes specific responsibilities:

- **DrawingGUI:** This class manages the JavaFX scene, user interactions, and the 3D environment. It also effects camera control, object selection, rendering, and UI component creation.

- **LightManager:** Responsible for managing lighting within the 3D scene, this class offers features like adding and removing point lights, controlling their visibility, and enabling/disabling lighting effects.

- **Materials:** Materials play a significant role in rendering 3D objects. This class manages various materials and textures, simplifying the process of applying them to objects.

- **RenderableObject:** An abstract class serving as the foundation for all 3D objects in the scene. It encapsulates methods for transformations, outline generation, and point light handling.

- **Sphere3D:** An implementation of a 3D sphere object, extending RenderableObject. I included methods for creating sphere outlines and managing dimensions.

- **Box3D:** Similar to Sphere3D but tailored for box-shaped objects, Box3D extends RenderableObject. It provides methods for generating box outlines and handling box-specific transformations.

- **Main:** The main entry point of the application. It sets up the JavaFX scene, handles key events for object manipulation, and manages the program's lifecycle.

- **ConfigBox:** This class contributes to the user interface by creating pop-up boxes for configuring object properties, such as dimensions, materials, and transformations.



# My journey into making 3D Modeling Application in a framework know for 2D



## Leveraging JavaFX for 3D

JavaFX, a framework primarily designed for 2D graphics, in the end allowed me to create this great 3D modeling application that provides basic 3D modeling functionality. However, it's important to note that JavaFX's 3D capabilities are relatively minimal compared to specialized 3D libraries or engines. The fundamental building block for 3D graphics in JavaFX is the [`Shape3D` class](https://docs.oracle.com/javase/8/javafx/api/javafx/scene/shape/Shape3D.html), which represents 3D geometric shapes.

### Limitations of `Shape3D`

While `Shape3D` provides a foundation for rendering 3D objects, it is somewhat bare-bones in terms of functionality. It offers basic features but lacks more advanced capabilities commonly found in dedicated 3D frameworks.


1. **Limited Geometry**: `Shape3D` primarily supports primitive geometric shapes like cubes, spheres, and cylinders. More complex shapes or custom meshes are challenging to create directly with this class.

2. **Basic Transformations**: Although `Shape3D` allows for translation and rotation, it is hard to implement especially if you want to keep track of the current rotation as it offers no functionality for that, it lacks more advanced transformation options often required for intricate 3D modeling tasks like changing object dimensions.

3. **No Mesh Editing**: Unlike professional 3D modeling software, JavaFX's `Shape3D` doesn't provide tools for directly editing or manipulating mesh vertices, edges, or faces. This limitation makes precise modeling or intricate modifications challenging and for obvious reasons led me not to implement any functionality like that.

4. **Material Constraints**: The materials applied to `Shape3D` objects are relatively straightforward. While basic textures and colors can be applied, achieving complex materials with shaders or custom textures is not supported and finding a material with a normal, bump and specular map that works nicely. In addition alot of materials readily availible use height maps over normal maps.

5. **Interactivity Challenges**: Implementing interactive features, such as object picking, object manipulation handles, camera control, can be complex and may require extensive custom code.

6. **Camera Control**: Camera's exist in JavaFX for mainly staying in place, it expects objects to stay in place and is built for 2D scenes. So it meant every single camera control from zooming to dragging I had to implement.


### Custom Implementations: Box and Sphere

To create various 3D objects within the application, I had to develop custom implementations of common shapes like boxes and spheres. These implementations were necessary because the built-in `Box` and `Sphere` classes in JavaFX are also relatively basic and don't provide certain features commonly expected in 3D modeling software.

For example, adding a translucent blue outline to selected objects for user feedback required led to the storing of another box/sphere slightly bigger and transparent as an outline and every change to the original object happened to the outline. Similarly, the deep copy functionality, which seamlessly duplicates objects with their transformations and materials, had to be implemented as its such an extential for modeling. In addition just being able to easily increase or get the rotation had to be written as it has no underlying way to store it.

Despite these limitations, JavaFX's 3D shape implementation being simple allowed me to adapt it effectively for this 3D modeling project. It showcases my ability to work within constraints and create engaging 3D applications using accessible tools. This led to a RenderableObject class to adapt to all these issues:


## User-Centric 3D Interaction

I placed a strong emphasis on user-driven 3D interactions to create an engaging and immersive experience. Leveraging JavaFX's event handling capabilities, I implemented features such as camera control, allowing users to intuitively navigate the 3D space as without that a 3D modelling program just ends up being static.


### User-Controlled Camera Interaction

One of the standout features of the 3D modeling application is the ability for users to explore the 3D space through intuitive camera controls. The JavaFX framework's event handling system made it possible to implement these interactive camera features.

### Camera Rotation

Users can effortlessly manipulate the camera's perspective by right-clicking and dragging the mouse. When the right mouse button is pressed, the camera's rotation in both the X and Y axes becomes the focal point. As users move the mouse, the camera angle adjusts dynamically, making the program feel live and not static.

### Resetting the Camera

For added user convenience, I included a "Reset Camera" button that allows users to instantly reset the camera angle to its default position. This feature is especially good for when you just get lost at what way your facing when in a 3D scenes.

### Switching Between Camera Modes

To cater to different user preferences, I integrated camera modes - "Rotate" and "Move." These modes dictate how user interactions affect the scene. In "Rotate" mode, mouse movements change the camera's orientation, while in "Move" mode, they control translation.

These camera interaction features combine to create a dynamic and user-friendly 3D modeling experience and make you forget even that JavaFX is a mainly 2D framework.

The code to achieve this is as below:
```java
// Taken from DrawingGUI class
private void setupCameraControl() {
    Rotate xAxisRotate = new Rotate(0, Rotate.X_AXIS);
    Rotate yAxisRotate = new Rotate(0, Rotate.Y_AXIS);
    xAxisRotate.angleProperty().bind(angleX);
    yAxisRotate.angleProperty().bind(angleY);
    group.getTransforms().addAll(
            xAxisRotate,
            yAxisRotate
    );
    scene.setOnMousePressed(clicked -> {
        //Checks if the users last mouse click was the right one
        if(clicked.getButton() == MouseButton.SECONDARY) {
            //stores the starting point of drag
            startX = clicked.getSceneX();
            startY = clicked.getSceneY();
            //stores the angle of the camera before drag
            startAngleX = angleX.get();
            startAngleY = angleY.get();
            //sets it true that the user last mouse click was the right one
            rightClick = true;
            initialCameraX = camera.getTranslateX();
            initialCameraY = camera.getTranslateY();
        }else {
            rightClick = false;
        }
    });
    scene.setOnMouseDragged(dragged -> {
        //Checks if the users last mouse click was the right one
        if(rightClick && !drag) {
            //Sets the cameras new angle
            angleX.set(startAngleX - (startY - dragged.getSceneY()));
            angleY.set(startAngleY + startX - dragged.getSceneX());
        }else if(rightClick){
            camera.setTranslateX(initialCameraX - (dragged.getSceneX() - startX) * 0.1);
            camera.setTranslateY(initialCameraY - (dragged.getSceneY() - startY) * 0.1);

        }
    });
}
```

## Notable implementations

### Deep Copy of 3D Renderable Objects

A standout feature is the ability to create deep copies of 3D objects while retaining their properties. This feature employs the Prototype Design Pattern. I implemented the `deepCopyObject` method in the RenderableObject class, allowing users to duplicate objects, including shape, material, point light, and transformation. This pattern ensures efficient object creation and customization.

The code to implement deep copying which is specifically called when you want to change the size of an object as you have no way to change the object size without making a new object:

```java

// Taken from Box3D class
public void setSize(double depth, double height, double width){
    Box3D newBox = DrawingGUI.getInstance().createBox(depth,height,width,shape.getTranslateX(),shape.getTranslateY(),shape.getTranslateZ());
    deepCopyObject(newBox,true);
}

// Taken from RenderableObject class
protected void deepCopyObject(RenderableObject object, boolean deleteOriginal){
    object.applyTransform(currentTransfrom);
    object.getShape3D().setMaterial(shape.getMaterial());
    if(pointLight != null) {
        object.setPointLight(pointLight);
        DrawingGUI.getInstance().addLight(object);
        if(deleteOriginal) {
            DrawingGUI.getInstance().removeLight(this);
        }
    }
    if(deleteOriginal) {
        DrawingGUI.getInstance().removeObject(this);
        DrawingGUI.getInstance().renderableObjectClicked(object);
    }
}
```

### Blue Outline When Selecting Objects

To enhance user interaction and provide visual feedback, I implemented a translucent blue outline for selected objects. This is achieved by copying and increasing the dimensions of the selected shape by one and making that then translucent blue, when the shape is transformed so is the outline shape. The Decorator Design Pattern is employed here, where the outline shape decorates the original object with additional visual properties.

### Rotations and Transformations

The program offers support for rotating and translating 3D objects. Users can rotate objects around the X, Y, and Z axes and reposition them within the 3D space. These transformations are applied using the Composite Design Pattern, where various transformation operations are composed to achieve complex transformations. The ability to accumulate transformations provides precise control over object placement. This means the current transformation must be stored and it also makes it so when performing the deep copy you can also copy over the current rotation.

```java
//  Taken from RenderableObject class
private void rotation(Rotate rotate){
    currentTransfrom = currentTransfrom.createConcatenation(rotate);
    shape.getTransforms().clear();
    shape.getTransforms().add(currentTransfrom);
    if(outline!=null){
        outline.getTransforms().clear();
        outline.getTransforms().add(currentTransfrom);
    }
    if (pointLight != null) {
        pointLight.getTransforms().clear();
        pointLight.getTransforms().add(currentTransfrom);
    }
}

public void setRotationX(double angle){
    Rotate rotate = new Rotate(angle, Rotate.X_AXIS);
    rotation(rotate);
}

public void setRotationY(double angle){
    Rotate rotate = new Rotate(angle, Rotate.Y_AXIS);
    rotation(rotate);
}

public void setRotationZ(double angle){
    Rotate rotate = new Rotate(angle, Rotate.Z_AXIS);
    rotation(rotate);
}
```

**Closing thoughts:**

The 3D modeling program I made ended up being very user-friendly and really pushes JavaFX to its limits. At the same time it forced me to think beyond just using premade functions/classes and think about how I could extend the existing system.
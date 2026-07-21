# BEV_Tutorial

![Bird's Eye View Example](https://www.cyient.com/hs-fs/hubfs/2025/blog/multi.png?width=800&height=800&name=multi.png)

## What is BEV (Bird's Eye View)?

**Bird's Eye View (BEV)** is a way of representing the environment **from above**, as if a drone were flying over the robot and looking straight down.

Instead of seeing the world from the robot's front camera, BEV converts sensor data into a **2D top-down map**. This makes it much easier for the robot to understand where obstacles, roads, and free space are located.

The creation of a BEV usually involves processing sensor data, such as LIDAR, camera images or combination of multiple sensors, to generate a top-down representation aligned with the ground plane. By transforming the sensor data into a different coordinate system, BEV enables a comprehensive view that captures a wider field of view and rich geometric information about the scene.

![](https://multicorewareinc.com/wp-content/uploads/2023/06/car_-graphic.png)

## BEV is widely used in:

- Autonomous Cars.
- UGVs (Unmanned Ground Vehicles)
- Mobile Robots
- Warehouse Robots
- Planetary Rovers

## Perception View (PV) to Bird’s Eye View (BEV):

Perception View (PV) : what the robot's camera sees.

Bird’s Eye View (BEV) : 2D View from above.

Imagine Rover Sees A box using the front camera, it dont know where it is it only knows the pixles, But i need to know where it is to know, distance betweeen me and the obstacle.

Notice that:

Nearby objects look bigger.
Distant objects look smaller.
You only see what is in front of you.
Everything is represented as pixels.

So we use bev to see it from above as we see a map ,

Notice something important:

The Rover didn't move.

The Obstacle didn't move.

Only the viewpoint changed.

![](https://multicorewareinc.com/wp-content/uploads/2023/06/BEV-gif.gif)

------------------------------------------------------------------------------------------------------------------------------------

##  Steps in Implementing Bird's Eye View : 

1- Calibration and Setup :


    Begin by calibrating the camera to obtain its intrinsic and extrinsic parameters. Typically, this is achieved using a chessboard calibration technique. The known geometry of the chessboard provides a reliable reference, allowing the system to compute the necessary camera parameters accurately.

   - **Intrinsic parameters** : describe the internal characteristics of a camera, defining the relationship between the camera's image plane and pixel coordinates. These parameters include: 
    
     - Focal Length: Defines how much the camera zooms in or out, controlling the scale of objects in the image. The focal length is typically represented in terms of the x and y axes: fx and fy.
     
      - Principal Point: The pixel coordinates of the optical center of the camera, where the optical axis intersects the image plane. It is represented as (Cx, Cy).
      
      - Skew Coefficient: The angle between the x and y axes, usually close to 90°. In most consumer cameras, this value is negligible, but for certain specialized lenses, this could affect the image.
      
      - Distortion Coefficients: Most camera lenses cause some level of distortion, especially wide-angle lenses. This distortion can be of two types:
      
        - Radial Distortion: Causes straight lines to appear curved (barrel or pincushion distortion).
        
        - Tangential Distortion: Occurs when the lens and the image plane are not perfectly aligned.
        
      These intrinsic parameters can be represented in a matrix, often referred to as the intrinsic matrix K:

      **Extrinsic parameters** : describe the camera's position and orientation relative to the world. They consist of:
      
        - Rotation Matrix (R): Describes the camera's orientation in the world.
        
        - Translation Vector (T): Describes the camera's position in the world. The translation vector has three components representing the camera's displacement along the x, y, and z axes.

2- Detecting Key Points for Homography

3- Warping the Image Using Homography

4- Post-Processing and Refinement

--------------------------------------------------------------------------------------------------------------------------------


In Lidar we convert , In camera , In Radar 

![](https://www.thinkautonomous.ai/blog/content/images/2023/12/image-12--1-.jpg)


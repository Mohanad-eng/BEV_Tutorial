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

   - Begin by calibrating the camera to obtain its intrinsic and extrinsic parameters. Typically, this is achieved using a chessboard calibration technique.

   - The known geometry of the chessboard provides a reliable reference, allowing the system to compute the necessary camera parameters accurately.

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

![after making ipm](https://www.mdpi.com/ai/ai-06-00082/article_deploy/html/images/ai-06-00082-g002.png)

3- Warping the Image Using Homography

4- Post-Processing and Refinement


In plain English, what the script does, no code:

# Inverse Perspective Mapping (IPM) / Bird's Eye View (BEV) Guide

## Overview

Inverse Perspective Mapping (IPM) is a computer vision technique that transforms a camera image into a **Bird's Eye View (BEV)**. Instead of viewing the road from the camera's perspective, the scene is projected onto the ground plane, making distances and lane boundaries easier to analyze.

This technique is widely used in:

* Autonomous vehicles
* Mobile robots
* Lane detection
* Path planning
* Autonomous navigation
* Parking assistance

---

# Why Do We Need IPM?

A forward-facing camera suffers from **perspective distortion**.

Objects farther away appear:

* Smaller
* Narrower
* Closer together

For example, two parallel lane lines appear to converge toward the horizon.

Camera View:

```text
        Horizon
           ▲

      |        |
       \      /
        \    /
         \  /
          \/
```

After applying IPM:

```text
Bird's Eye View

|          |
|          |
|          |
|          |
|          |
```

The lane boundaries become nearly parallel, making geometric measurements much easier.

---

# Applications

IPM is commonly used for:

* Lane detection
* Road segmentation
* Obstacle mapping
* Occupancy grids
* Autonomous driving
* Mobile robot navigation
* Agricultural robots
* Warehouse robots

---

# Two Ways to Perform IPM

There are two common approaches.

---

## Method 1 — Four-Point Homography

This method is mainly used for:

* Learning homography
* Single images
* Document scanning
* Simple BEV demonstrations

### Step 1

Choose four points in the original image.

```python
src_pts = np.float32([
    [128,252],
    [350,270],
    [311,231],
    [191,222]
])
```

These are pixel coordinates:

```text
[x, y]
```

where

* x = horizontal position
* y = vertical position

---

### Step 2

Choose where those points should appear in the output.

```python
dst_pts = np.float32([
    [70,400],
    [230,400],
    [230,0],
    [70,0]
])
```

This rectangle represents the desired Bird's Eye View.

---

### Step 3

Compute the Homography.

```python
H = cv2.getPerspectiveTransform(src_pts, dst_pts)
```

The result is a 3×3 transformation matrix.

---

### Step 4

Warp the image.

```python
warped = cv2.warpPerspective(
    image,
    H,
    (300,400)
)
```

OpenCV uses the homography matrix to transform every pixel into the new top-down image.

---

### Advantages

* Very easy to understand
* No camera calibration required
* Good for experiments

### Disadvantages

* Four points must be selected manually (or detected automatically)
* Camera movement usually requires recalculating the points
* Not ideal for autonomous robots

---

# Method 2 — Camera Calibration (Intrinsics + Extrinsics)

This is the method used in robotics and autonomous vehicles.

Instead of selecting four points manually, the homography is computed from the camera model.

Required information:

## Camera Intrinsics

Intrinsic parameters describe the camera itself.

```text
K =
[ fx  0  cx ]
[ 0  fy  cy ]
[ 0   0   1 ]
```

Where:

* fx = focal length in x
* fy = focal length in y
* cx = principal point x
* cy = principal point y

These values come from camera calibration or camera specifications.

---

## Camera Extrinsics

Extrinsic parameters describe where the camera is mounted.

They include:

* Camera height
* Camera position
* Camera rotation
* Camera pitch
* Camera yaw

Example:

```text
Camera Position

Forward = 0.40 m

Height = 0.15 m

Yaw = 0°
```

---

## Ground Plane Assumption

IPM assumes that everything being projected lies on

```text
Z = 0
```

which represents the ground plane.

Objects above the ground (cars, people, trees) cannot be perfectly projected.

---

## Homography Computation

The calibration pipeline is:

```text
Camera Intrinsics (K)

        +

Camera Rotation (R)

        +

Camera Translation (t)

        │

        ▼

Ground Homography (H)

        │

        ▼

Bird's Eye View
```

The homography is computed mathematically without selecting any image points.

---

### Advantages

* Fully automatic
* Accurate
* Works on every image
* Suitable for robotics
* Suitable for autonomous vehicles

### Disadvantages

* Requires camera calibration
* Requires camera pose
* More mathematical

---

# Comparison

| Feature                     | Four Points | Camera Calibration |
| --------------------------- | ----------- | ------------------ |
| Manual point selection      | Yes         | No                 |
| Camera calibration required | No          | Yes                |
| Robot friendly              | No          | Yes                |
| Automatic                   | No          | Yes                |
| Used in robotics            | Rarely      | Yes                |
| Used for learning           | Yes         | Sometimes          |

---

# How Are the Four Points Chosen?

When using the four-point method, the selected points should:

* Lie on the ground plane.
* Form a rectangle in the real world.
* Be visible in the camera image.
* Be selected in a consistent order.

Correct order:

```text
Camera View

4 -------- 3
 \        /
  \      /
   \    /
    \  /
1 -------- 2
```

Where:

1. Near-left
2. Near-right
3. Far-right
4. Far-left

Incorrect ordering produces a distorted or mirrored Bird's Eye View.

---

# Homography Matrix

Both approaches ultimately compute the same object:

```text
H (3 × 3)
```

The difference is how the matrix is obtained.

Four-point method:

```text
4 Source Points
       +
4 Destination Points
        │
        ▼
getPerspectiveTransform()
        │
        ▼
H
```

Calibration method:

```text
Intrinsic Matrix

        +

Extrinsic Matrix

        │
        ▼
Mathematics
        │
        ▼
H
```

Both homography matrices are then used by OpenCV.

```python
warped = cv2.warpPerspective(
    image,
    H,
    output_size
)
```

---

# IPM in ROS2

In a robotics pipeline, the workflow is typically:

```text
Camera

        │

        ▼

Image Segmentation

        │

        ▼

Binary Mask

        │

        ▼

Inverse Perspective Mapping

        │

        ▼

Bird's Eye View

        │

        ▼

Lane Detection

        │

        ▼

Centerline Extraction

        │

        ▼

Path Planning

        │

        ▼

Robot Controller
```

---

# Which Method Should You Use?

Use the **four-point homography method** if you are:

* Learning perspective transforms.
* Working with a single image.
* Building a quick demonstration.
* Scanning documents.

Use the **camera calibration method** if you are:

* Developing autonomous robots.
* Using ROS2.
* Working with mobile robots or autonomous vehicles.
* Building a real-time BEV system.
* Performing lane detection or path planning.

---

# Conclusion

Inverse Perspective Mapping transforms a perspective camera image into a top-down Bird's Eye View by applying a homography matrix. This homography can be obtained either by manually defining four corresponding points or by computing it from the camera's intrinsic and extrinsic parameters. While the four-point approach is excellent for understanding the concept and experimenting with individual images, the calibration-based approach is the standard solution for robotics because it is automatic, accurate, and scalable to real-time autonomous navigation.


You look at your photo and pick 4 points that you know form a rectangle on the real ground — usually two points close to the car on the left/right edges of a lane, and two points further away on those same edges.
You tell the computer "these 4 points, even though they look like a trapezoid in the photo, are actually corners of a rectangle."
The computer works out the one mathematical warp that would turn your clicked trapezoid into a perfect rectangle.
It applies that exact same warp to every pixel in the photo — not just your 4 points — which is what turns the whole road into a top-down view.

--------------------------------------------------------------------------------------------------------------------------------


In Lidar we convert , In camera , In Radar 

![](https://www.thinkautonomous.ai/blog/content/images/2023/12/image-12--1-.jpg)


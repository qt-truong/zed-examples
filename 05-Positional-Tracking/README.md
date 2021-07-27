# ZED Positional Tracking

Positional tracking is the ability of a device to estimate its position relative to the world around it. Also called motion tracking or odometry, this is used to track the movement of a camera or user in 3D space with six degrees of freedom (6DoF).

## Overview

* [How It Works](#how-it-works)
* [Getting Pose](#getting-pose)
* [Positional Tracking Configuration](#positional-tracking-configuration)
* [Code Examples](#code-examples)
* [Integrations](#integrations)
* [Docs](#documentation)


## How It Works

The ZED uses visual-inertial stereo tracking of its surroundings to understand the movement of its user or system. As the camera moves in the real-world, the ZED SDK reports its position and orientation for each new frame. This information is called the camera 6DoF pose. Pose information is output at the frame rate of the camera, up to 60fps in HD720 and 100fps in WVGA mode.


## Getting Pose
Position gets updated with every new frame. To retrieve pose data, after opening the camera, enable positional tracking using `enablePositionalTracking()`, then use `getPosition()` after grabbing a frame.

The following pose data is returned:

- **Position**: the location of the camera in space is available as a vector [X, Y, Z]. Its norm represents the total distance traveled between the current camera position and the reference coordinate frame.

- **Orientation**: the orientation of the camera in space is available as a quaternion [X, Y, Z, W]. A quaternion can be directly manipulated to reflect yaw, pitch and roll in different coordinate frames.

The `POSE` class also contains timestamp, confidence value and a rotation matrix that describes the rotation of the camera with respect to the World Frame. The pose is relative to a reference [coordinate frame](https://www.stereolabs.com/docs/positional-tracking/coordinate-frames/), usually the World Frame which is fixed in space.


**C++**
```cpp
sl::Pose zed_pose;
if (zed.grab() == SUCCESS) {
        // Get the pose of the camera relative to the world frame
        POSITIONAL_TRACKING_STATE state = zed.getPosition(zed_pose, REFERENCE_FRAME::WORLD);
        // Display translation and timestamp
        printf("Translation: tx: %.3f, ty:  %.3f, tz:  %.3f, timestamp: %llu\r",
        zed_pose.getTranslation().tx, zed_pose.getTranslation().ty, zed_pose.getTranslation().tz, zed_pose.timestamp);
        // Display orientation quaternion
        printf("Orientation: ox: %.3f, oy:  %.3f, oz:  %.3f, ow: %.3f\r",
        zed_pose.getOrientation().ox, zed_pose.getOrientation().oy, zed_pose.getOrientation().oz, zed_pose.getOrientation().ow);
    }
}
```

**Python**
```python
zed_pose = sl.Pose()
if zed.grab(runtime_parameters) == SUCCESS :
        # Get the pose of the camera relative to the world frame
        state = zed.get_position(zed_pose, sl.REFERENCE_FRAME.FRAME_WORLD)
        # Display translation and timestamp
        py_translation = sl.Translation()
        tx = round(zed_pose.get_translation(py_translation).get()[0], 3)
        ty = round(zed_pose.get_translation(py_translation).get()[1], 3)
        tz = round(zed_pose.get_translation(py_translation).get()[2], 3)
        print("Translation: tx: {0}, ty:  {1}, tz:  {2}, timestamp: {3}\n".format(tx, ty, tz, zed_pose.timestamp))
        #Display orientation quaternion
        py_orientation = sl.Orientation()
        ox = round(zed_pose.get_orientation(py_orientation).get()[0], 3)
        oy = round(zed_pose.get_orientation(py_orientation).get()[1], 3)
        oz = round(zed_pose.get_orientation(py_orientation).get()[2], 3)
        ow = round(zed_pose.get_orientation(py_orientation).get()[3], 3)
        print("Orientation: ox: {0}, oy:  {1}, oz: {2}, ow: {3}\n".format(ox, oy, oz, ow))
```

**C#**
```csharp
Pose zed_pose = new Pose();
RuntimeParameters runtimeParameters = new RuntimesParameters();
if (zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS) {
        // Get the pose of the camera relative to the world frame
        POSITIONAL_TRACKING_STATE state = zed.GetPosition(ref zed_pose, REFERENCE_FRAME.WORLD);
        // Display translation and timestamp
        Console.WriteLine("Translation: tx: " + zed_pose.translation.X + "ty: " + zed_pose.translation.Y + "tz: " + zed_pose.translation.Z + "Timestamp: " + zed_pose.timestamp);
        Console.WriteLine("Rotation: ox: " + zed_pose.rotation.X + "oy: " + zed_pose.rotation.Y + "oz: " + zed_pose.rotation.Z + "ow: " + zed_pose.rotation.w);
}
```

*Note:* Positional tracking uses image and depth information to estimate the position of the camera in 3D space. To improve tracking results, use high FPS video modes such as HD720 and WVGA.

## Positional Tracking Configuration
To configure positional tracking, use `InitParameters` at initialization and `RuntimeParameters` to change specific parameters during use.

To read more about tracking configuration, see [using the API](https://www.stereolabs.com/docs/positional-tracking/using-tracking/) docs.


## Code Examples
For code examples, check out the [Tutorial](../09-Tutorials/tutorial%204%20-%20positional%20tracking) and [Sample](../10-Samples/positional%20tracking) on GitHub.


## Integrations
Check the [Integrations](../11-Integrations#overview) list to use positional tracking with your favorite suite of tools and libraries.


## Documentation
For more information, read the [Documentation](https://www.stereolabs.com/docs/positional-tracking) or these specific sections:

* [Positional Tracking Overview](https://www.stereolabs.com/docs/positional-tracking/)
* [Getting Pose](https://www.stereolabs.com/docs/positional-tracking/using-tracking/#getting-pose)
* [Tracking States](https://www.stereolabs.com/docs/positional-tracking/using-tracking/#tracking-states)
* [Area Memory](https://www.stereolabs.com/docs/positional-tracking/area-memory/)
* [Coordinate Frames](https://www.stereolabs.com/docs/positional-tracking/coordinate-frames/)
* [Using the API](https://www.stereolabs.com/docs/positional-tracking/using-tracking/)
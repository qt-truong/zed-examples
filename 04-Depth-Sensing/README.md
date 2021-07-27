# ZED Depth Sensing

The ZED stereo camera reproduces the way human binocular vision works. Human eyes are horizontally separated by about 65 mm on average. Thus, each eye has a slightly different view of the world around. By comparing these two views, our brain can infer not only depth but also 3D motion in space.

Likewise, Stereolabs stereo cameras have two eyes separated by 6 to 12 cm which allow to capture high-resolution 3D video of the scene and estimate depth and motion by comparing the displacement of pixels between the left and right images.

## Overview

* [How It Works](#how-it-works)
* [Getting Depth Data](#getting-depth-data)
* [Depth Sensing Configuration](#depth-sensing-configuration)
* [Code Examples](#code-examples)
* [Integrations](#integrations)
* [Documentation](#documentation)

## How it works

### Depth Map

Depth maps captured by the ZED store a distance value (Z) for each pixel (X, Y) in the image. The distance is expressed in metric units (meters for example) and calculated from the back of the left eye of the camera to the scene object.

Depth maps cannot be displayed directly as they are encoded on 32 bits. To display the depth map, a monochrome (grayscale) 8-bit representation is necessary with values between [0, 255], where 255 represents the closest possible depth value and 0 the most distant possible depth value.

### 3D Point Cloud

Another common way of representing depth information is by a 3-D point cloud. A point cloud can be seen as a depth map in three dimensions. While a depth map only contains the distance or Z information for each pixel, a point cloud is a collection of 3D points (X,Y,Z) that represent the external surface of the scene and can contain color information.

## Getting Depth Data

To extract the depth map of a scene, use `grab()` to grab a new image and `retrieveMeasure()` to retrieve the depth aligned on the left image. `retrieveMeasure()` can be used to retrieve a depth map, a confidence map, a normal map, or a point cloud.


**C++**
```cpp
sl::Mat image;
sl::Mat depth_map;
if (zed.grab() == SUCCESS) {
  // A new image and depth is available if grab() returns SUCCESS
  zed.retrieveImage(image, VIEW::LEFT);           // Retrieve left image
  zed.retrieveMeasure(depth_map, MEASURE::DEPTH); // Retrieve depth
}
```

**Python**
```python
image = sl.Mat()
depth_map = sl.Mat()
runtime_parameters = sl.RuntimeParameters()
if zed.grab(runtime_parameters) ==  SUCCESS :
  # A new image and depth is available if grab() returns SUCCESS
  zed.retrieve_image(image, sl.VIEW.LEFT)           # Retrieve left image
  zed.retrieve_measure(depth_map, sl.MEASURE.DEPTH) # Retrieve depth
```

**C#**
```csharp
sl.Mat image = new sl.Mat();
sl.Mat depth_map = new sl.Mat();
sl.RuntimeParameters runtimesParameters = new sl.RuntimeParameters();
if (zed.Grab(ref runtimeParameters) == sl.ERROR_CODE.SUCCESS) {
  // A new image and depth is available if Grab() returns SUCCESS
  zed.RetrieveImage(image, VIEW.LEFT);           // Retrieve left image
  zed.RetrieveMeasure(depth_map, MEASURE.DEPTH); // Retrieve depth
}
```

## Depth Sensing Configuration

To configure positional tracking, use `InitParameters` at initialization and `RuntimeParameters` to change specific parameters during use.

To read more about depth sensing configuration, see [using the API](https://www.stereolabs.com/docs/depth-sensing/using-depth/) docs.


## Code Examples

For code examples, check out the [Tutorial](../09-Tutorials/tutorial%203%20-%20depth%20sensing) and [Sample](../10-Samples/depth%20sensing).


## Integrations
Check the [Integrations](../11-Integrations#overview) list to use depth sensing with your favorite suite of tools and libraries.


## Documentation
For more information, read the [Documentation](https://www.stereolabs.com/docs/depth-sensing/) or these specific sections:

* [Depth Sensing Overview](https://www.stereolabs.com/docs/depth-sensing/)
* [Depth Settings](https://www.stereolabs.com/docs/depth-sensing/depth-settings/)
* [Confidence Filtering](https://www.stereolabs.com/docs/depth-sensing/confidence-filtering/)
* [Using the API](https://www.stereolabs.com/docs/depth-sensing/using-depth/)

You can also check out our [API Reference](https://www.stereolabs.com/docs/api/) for more details about the available functions provided by the SDK.
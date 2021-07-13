# ZED Depth Sensing API

The ZED stereo camera reproduces the way human binocular vision works. Human eyes are horizontally separated by about 65 mm on average. Thus, each eye has a slightly different view of the world around. By comparing these two views, our brain can infer not only depth but also 3D motion in space.

Likewise, Stereolabs stereo cameras have two eyes separated by 6 to 12 cm which allow to capture high-resolution 3D video of the scene and estimate depth and motion by comparing the displacement of pixels between the left and right images.

**Overview**

* [Depth Sensing Configuration](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#depth-sensing-configuration)
* [Getting Depth Data](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#getting-depth-data)
* [Displaying Depth Image](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#displaying-depth-image)
* [Getting Point Cloud Data](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#getting-point-cloud-data)
* [Getting Normal Map](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#getting-normal-map)
* [Adjusting Depth Resolution](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#adjusting-depth-resolution)
* [Code Example](https://github.com/qt-truong/zed-examples/tree/master/04-DepthSensing#code-example)


## Depth Sensing Configuration
To configure depth sensing, use `InitParameters` at initialization and `RuntimeParameters` to change specific parameters during use.


**C++**
```cpp
// Set configuration parameters
InitParameters init_params;
init_params.depth_mode = DEPTH_MODE::ULTRA; // Use ULTRA depth mode
init_params.coordinate_units = UNIT::MILLIMETER; // Use millimeter units (for depth measurements)
```

**Python**
```python
# Set configuration parameters
init_params = sl.InitParameters()
init_params.depth_mode = sl.DEPTH_MODE.ULTRA # Use ULTRA depth mode
init_params.coordinate_units = sl.UNIT.MILLIMETER # Use millimeter units (for depth measurements)
```

**C#**
```csharp
// Set depth mode in ULTRA
InitParameters init_parameters = new InitParameters();
init_parameters.depthMode = DEPTH_MODE.ULTRA; // Use ULTRA depth mode
init_parameters.coordinateUnits = UNIT.MILLIMETER; // Use millimeter units (for depth measurements)
```

## Getting Depth Data
To extract the depth map of a scene, use `grab()` to grab a new image and `retrieveMeasure()` to retrieve the depth aligned on the left image. `retrieveMeasure()` can be used to retrieve a depth map, a confidence map or a point cloud.

**C++**
```cpp
sl::Mat image;
sl::Mat depth_map;
if (zed.grab() == SUCCESS) {
  // A new image and depth is available if grab() returns SUCCESS
  zed.retrieveImage(image, VIEW::LEFT); // Retrieve left image
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
  zed.retrieve_image(image, sl.VIEW.LEFT) # Retrieve left image
  zed.retrieve_measure(depth_map, sl.MEASURE.DEPTH) # Retrieve depth
```

**C#**
```csharp
sl.Mat image = new sl.Mat();
sl.Mat depth_map = new sl.Mat();
sl.RuntimeParameters runtimesParameters = new sl.RuntimeParameters();
if (zed.Grab(ref runtimeParameters) == sl.ERROR_CODE.SUCCESS) {
  // A new image and depth is available if Grab() returns SUCCESS
  zed.RetrieveImage(image, VIEW.LEFT); // Retrieve left image
  zed.RetrieveMeasure(depth_map, MEASURE.DEPTH); // Retrieve depth
}
```

### Accessing Depth Values
The depth matrix stores 32-bit floating-point values which represent depth (Z) for each (X,Y) pixel. To access these values, use `getValue()`.

**C++**
```cpp
float depth_value=0;
depth_map.getValue(x,y,&depth_value);
```

**Python**
```python
depth_value = depth_map.get_value(x,y)
```

**C#**
```csharp
depth_value.GetValue(1, 2, out float depth_value);
```

By default, depth values are expressed in millimeters. Units can be changed using `InitParameters::coordinate_units`. Advanced users can retrieve images, depth and points clouds either in CPU memory (default) or in GPU memory using `retrieveMeasure(*, *, MEM_GPU)`.

## Displaying Depth Image
The 32-bit depth map can be displayed as a grayscale 8-bit image. To display the depth map, we scale its values to [0, 255], where 255 (white) represents the closest possible depth value and 0 (black) represents the most distant possible depth value. We call this process depth normalization. To retrieve a depth image, use `retrieveImage(depth,VIEW_DEPTH)`. Do no use the 8-bit depth image in your application for other purpose than displaying depth.


**C++**
```cpp
sl::Mat depth_for_display;
zed.retrieveImage(depth_for_display,VIEW::DEPTH);
```

**Python**
```python
depth_for_display = sl.Mat()
zed.retrieve_image(depth_for_display, sl.VIEW.DEPTH)
```

**C#**
```csharp
sl.Mat depth_for_display = new sl.Mat();
zed.RetrieveImage(depth_for_display,VIEW.DEPTH);
```

## Getting Point Cloud Data
A 3D point cloud with (X,Y,Z) coordinates and RGBA color can be retrieved using `retrieveMeasure()`.

**C++**
```cpp
sl::Mat point_cloud;
zed.retrieveMeasure(point_cloud, MEASURE::XYZRGBA);
```

**Python**
```python
point_cloud = sl.Mat()
zed.retrieve_measure(point_cloud, sl.MEASURE.XYZRGBA)
```

**C#**
```csharp
sl.Mat point_cloud = new sl.Mat();
zed.RetrieveMeasure(point_cloud, MEASURE.XYZRGBA);
```

To access a specific pixel value, use `getValue()`.

**C++**
```cpp
float4 point3D;
// Get the 3D point cloud values for pixel (i,j)
point_cloud.getValue(i,j,&point3D);
float x = point3D.x;
float y = point3D.y;
float z = point3D.z;
float color = point3D.w;
```

**Python**
```python
# Get the 3D point cloud values for pixel (i,j)
point3D = point_cloud.get_value(i,j)
x = point3D[0]
y = point3D[1]
z = point3D[2]
color = point3D[3]
```

**C#**
```csharp
float4 point3D = new float4();
// Get the 3D point cloud values for pixel (i,j)
point_cloud.GetValue(i,j, out point3D);
float x = point3D.x;
float y = point3D.y;
float z = point3D.z;
float color = point3D.w;
```

The point cloud stores its data on 4 channels using 32-bit float for each channel. The last float is used to store color information, where R, G, B, and alpha channels (4 x 8-bit) are concatenated into a single 32-bit float. You can choose between different color formats using `XYZ<COLOR>`. For example, BGRA color is available using `retrieveMeasure(point_cloud, MEASURE::XYZBGRA)`.

### Measuring distance in point cloud
When measuring distances, use the 3D point cloud instead of the depth map. The Euclidean distance formula allows to calculate the distance of an object relative to the left eye of the camera.

**C++**
```cpp
float4 point3D;
// Measure the distance of a point in the scene represented by pixel (i,j)
point_cloud.getValue(i,j,&point3D);
float distance = sqrt(point3D.x*point3D.x + point3D.y*point3D.y + point3D.z*point3D.z);
```

**Python**
```python
# Measure the distance of a point in the scene represented by pixel (i,j)
point3D = point_cloud.get_value(i,j)
distance = math.sqrt(point3D[0]*point3D[0] + point3D[1]*point3D[1] + point3D[2]*point3D[2])
```

**C#**
```csharp
float4 point3D = new float4();
// Measure the distance of a point in the scene represented by pixel (i,j)
point_cloud.GetValue(i,j,out point3D);
float distance = (float)Math.Sqrt(point3D.x*point3D.x + point3D.y*point3D.y + point3D.z*point3D.z);
```

## Getting Normal Map
Surface normals can be retrieved using `retrieveMeasure(normal_map, MEASURE_NORMALS)`. Normal maps are useful for traversability estimation and realtime lighting. The output is a 4 channels 32-bit matrix (X,Y,Z,empty), where X,Y,Z values encode the direction of the normal vectors.


## Adjusting Depth Resolution
To improve the performance of your application and speed up data acquisition, you can retrieve a lower resolution measure by specifying the width and height parameters in `retrieveMeasure()`. You can also specify where you would like to have the data available, in CPU (RAM) or GPU memory.

**C++**
```cpp
sl::Mat point_cloud;
// Retrieve a resized point cloud
// width and height specify the total number of columns and rows for the point cloud dataset
width = zed.getResolution().width / 2;
height = zed.getResolution().height / 2;
zed.retrieveMeasure(point_cloud, MEASURE::XYZRGBA, MEM::GPU, width, height);
```

**Python**
```python
point_cloud = sl.Mat()
# Retrieve a resized point cloud
# width and height specify the total number of columns and rows for the point cloud dataset
width = zed.get_resolution().width / 2;
height = zed.get_resolution().height / 2;
zed.retrieve_measure(point_cloud, sl.MEASURE.XYZRGBA, sl.MEM.GPU, width, height)
```

**C#**
```csharp
sl.Mat point_cloud = new sl.Mat();
// Retrieve a resized point cloud
// width and height specify the total number of columns and rows for the point cloud dataset
width = zed.ImageWidth / 2;
height = zed.ImageHeight / 2;
zed.RetrieveMeasure(point_cloud, MEASURE.XYZRGBA, MEM.GPU, new Resolution(width, height));
```

## Code Example
For code examples, check out the [Tutorial](https://github.com/qt-truong/zed-examples/tree/master/09-Tutorials/tutorial%203%20-%20depth%20sensing) and [Sample](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/depth%20sensing).

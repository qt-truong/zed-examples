# Object Detection

Object detection is the ability to identify objects present in an image. Thanks to depth sensing and 3D information, the ZED camera is able to provide the 2D and 3D position of the objects in the scene.

**Overview**

* [How It Works](#how-it-works)
* [Getting Object Data](#getting-object-data)
* [Object Detection Configuration](#object-detection-configuration)
* [Code Examples](#code-examples)
* [Integrations](#integrations)
* [Docs](#documentation)


## How It Works

The ZED SDK uses AI and neural networks to determine which objects are present in both the left and right images. The SDK then computes the 3D position of each object, as well as their bounding box, using data from the depth module. The objects can also be tracked within the environment over time, even if the camera is in motion, thanks to data from the positional tracking module.

## Getting Object Data

The ZED SDK allows to detect objects at every new frame. After opening the camera, enable the Object Detection module using `enableObjectDetection()`.
Then, get an new image with `grab(...)` and extract the detected objects with `retrieveObjects()`. The objects'  2D positions are relative to the left image, while the 3D positions are either in the `CAMERA` or `WORLD` reference frame depending on `RuntimeParameters.measure3D_reference_frame` (given to the `grab()` function).

**C++**
```cpp
sl::Objects objects; // Structure containing all the detected objects
if (zed.grab() == ERROR_CODE::SUCCESS) {
  zed.retrieveObjects(objects, detection_parameters_rt); // Retrieve the detected objects
}
```

**Python**
```python
objects = sl.Objects() # Structure containing all the detected objects
if zed.grab() == sl.ERROR_CODE.SUCCESS :
  zed.retrieve_objects(objects, obj_runtime_param) # Retrieve the detected objects
```

**C#**
```csharp
sl.Objects objects = new sl.Objects(); // Structure containing all the detected objects
RuntimeParameters runtimeParameters = new RuntimeParamters();
if (zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS) {
  zed.RetrieveObjects(ref objects, ref obj_runtime_param); // Retrieve the detected objects
}
```

The `sl::Objects` class stores all the information regarding the different objects present in the scene in it `object_list` attribute. Each individual object is stored as a `sl::ObjectData` with all information about it, such as bounding box, position, mask, etc. All objects from a given frame are stored in a vector within `sl::Objects`. `sl::Objects` also contains the timestamp of the detection, which can help connect the objects to the images.

You can iterate through the objects as follows:

**C++**
```cpp
for(auto object : objects.object_list)
  std::cout << object.id << " " << object.position << std::endl;
```

**Python**
```python
for object in objects.object_list:
  print("{} {}".format(object.id, object.position))
```

**C#**
```csharp
for (int idx = 0; idx < objects.numObject; idx++)
  Console.WriteLine(objects.objectData[idx].id + " " + objects.objectData[idx].position);
```

Each detected object can be accessed by using its ID as follows:

**C++**
```cpp
sl::ObjectData object;
objects.getObjectDataFromId(object, 0); // Get the object with ID = O
```

**Python**
```python
object = sl.ObjectData()
objects.get_object_data_from_id(object, 0); # Get the object with ID = O
```

**C#**
```csharp
sl.Objects object = new sl.Objects();
for (int idx = 0; idx < objects.numObject; idx++)
  if (objects.objectData[idx].id == 0)
    object = objects.objectData[idx];
```

## Object Detection Configuration
To configure object detection, use `ObjectDetectionParameters` at initialization and `ObjectDetectionRuntimeParameters` to change specific parameters during use.

To read more about object detection configuration, check this [dedicated section](https://www.stereolabs.com/docs/object-detection/using-object-detection/#object-detection-configuration)


## Code Examples
For code examples, check out the 3D Object Detetection [Tutorial](../09-Tutorials/tutorial%206%20-%20object%20detection) and [Sample](../10-Samples/object%20detection) in this repository.

## Integrations
Check the [Integrations](../11-Integrations#overview) list to use object detection with your favorite suite of tools and libraries.


## Documentation
For more information, read the [Documentation](https://www.stereolabs.com/docs/object-detection/) or these specific sections:

* [Object Detection Overview](https://www.stereolabs.com/docs/object-detection/)
* [Detection outputs](https://www.stereolabs.com/docs/object-detection/#detection-outputs)
* [Object Detection Configuration](https://www.stereolabs.com/docs/object-detection/using-object-detection/#object-detection-configuration)
# Release Notes
<BR>

## 3.5.0

ZED SDK 3.5 introduces a major improvement in the Body Tracking module which is now up to 2.5 faster and 30% more accurate and a new medium mode.

Besides, it also introduces a new way to retrieve detected objects in batches, powered by deep learning Re-Identification and appearance matching. This allows to keep the same ID for a detected object even if it has been occluded, hidden or out of the scene for some time. 

Finally, the ZED SDK 3.5 adds the support of the upcoming ZED 2i.


### SDK

### New Features / Improvements

#### API

- Introduced new Body Tracking model with better performances and accuracy : 

  * FAST mode is now x5(jetson)/x1.5(desktop) faster compared to FAST v3.4. 
  * MEDIUM mode is x2(jetson) faster compared to FAST v3.4. 
  * MEDIUM mode is 28% more precise compared to FAST v3.4
  * ACCURATE mode is now x2 faster compared to ACCURATE v3.4
  * ACCURATE mode is now 10% more precise compared to ACCURATE v3.4
  * This new model can also detect bodies in a much further range than previous version.


- Introduced new batching system for retrieving Objects in the Object Detection module. 

  * The objects can now be retrieved in batches of objects data defined by the new `sl::ObjectsBatch` structure.
  This adds a latency in the objects output flow, which allows internally a Re-Identification of the objects and check if an existing ID can be re-assigned. 
  Therefore, an oject that has been occluded/hidden or just moved out of the scene can now kept its original ID if the match is successfull.
  
  * Activate the batching system with `sl::ObjectDetectionParameters::BatchParameters`. See the API documentation for more information.
  * Call `sl::Camera::retrieveObjects()` to ingest the live objects into the batching system.
  * Retrieve batched objects with `sl::Camera::getObjectsBatch(std::vector<sl::ObjectsBatch>&)`
  * A `sl::ObjectsBatch` is bound to a specific ID. it contains the list of timestamps, positions, bbox,.... Each list have the exact same size.

- Updated Object detection models (`MULTI_CLASS` models) which slighty improve runtime (5%) and accuracy (2.5%).
- Added `MULTI_CLASS_BOX_MEDIUM` mode, x2 faster compared to ACCURATE mode while being 20% more precise compared to FAST.
- Added support of upcoming ZED2i new camera (USB : 2b03:f880/f881).
- Improved `TRACKING_STATE::SEARCHING`, in Object Detection module, leading to better recovery and avoid flying boxes.

#### Samples

- Updated object detection/birds eye viewer sample with a flag to activate/de-activate batch to show how to use the new batching system.
- Added a new Body Tracking Tutorial

#### Tools
- Added [documentation](https://www.stereolabs.com/docs/sensors/magnetometer/#magnetometer-calibration) about Magnetometer Calibration using Sensor Viewer

### Bug Fixes

#### API
- Fixed a memory leak impacting the Object Detection module when using multi class box models.
- Fixed issue with magnetometer calibration parameters not correctly saved in camera internal memory. Note that this requires to perform a new magnetometer calibration (with ZED Sensor Viewer tool) as previous are invalid in the new system.
- Fixed non valid IMU data on some SVOs last frame (skipped)
- Fixed the depth confidence value which could be higher than 100

#### Tools
- Fixed ZEDfu crash on Jetson platform.
- Fixed the "Edges" rendering mode on ZEDfu for Jetson.
- Fixed Depth Viewer with multi screen and improve Settings Panel GUI

### Notes
- The new batching system for object detection is only available with TensorRT7.1. Since TRT7.1 is not supported for all CUDA version, the module is only supported on the following platforms : 
  * Windows with CUDA 11.0.
  * Linux with CUDA 10.2 or cuda 11.0.
  * Jetsons with JP44 and JP45.

- On Linux, the static version of the SDK doesn't include the sl_ai library anymore for performance and support reasons. `libsl_ai.so` is now needed to use the AI module even when using the static sl_zed library. On Jetson the static library is now available for all Jetpack version. The installers are now unified again and include everything while being smaller in size.


### Integrations

#### Python

- Dropping python 3.5 support (not maintained since September 2020)
- Added support of ZED SDK 3.5
- Added support of upcoming ZED2i camera
- Added support of the new batching system
- Updated the object detection/bird view sample with a flag to activate/de-activate batch to show how to use the new batching system.
- Improved the body tracking sample with a new display and better performance
- Added a new Body Tracking Tutorial
- Fixed `sl.Mat().set_value` and `sl.Mat().set_to` functions
- Fixed `sl.Mat().get_data` function for `sl.Mat` of type `sl.MAT_TYPE.U16_C1`

#### C-sharp
- Added support of ZED SDK 3.5
- Added support of upcoming ZED2i camera
- Added support of the new batching system
- Updated the object detection/bird view sample with a flag to activate/de-activate batch to show how to use the new batching system.
- Updated the body tracking sample with a new display
- Added a new Body Tracking Tutorial

#### [GStreamer](https://github.com/stereolabs/zed-gstreamer)

- Depth mode has now a default value of NONE.
  To disable the depth elaboration it is also necessary to disable the positional tracking and the object detection, 
  so the values of the relative activation options have been set to false as default.
  The zedsrc element now starts without performing any depth elaboration with the default settings. 
- Added missing Positional Tracking parameters
- Added missing Runtime parameters (texture confidence, confidence, sensing mode and measure reference frame).
- Added missing Object Detection parameters (for body fitting and max range)
- Change parameters names in `zedsrc` to match the names in the ZED SDK

See [GStreamer plugin changelog](https://github.com/stereolabs/zed-gstreamer/blob/master/latest_changes.md) for details.

#### [OpenNI2 plugin](https://github.com/stereolabs/zed-openni)

- Added online [documentation](https://www.stereolabs.com/docs/openni2/)

#### ROS

- Added support for new DEPTH16_MM data type for depth (OPENNI MODE)
- Fixed issue #660: detected objects topic not published if depth computation not active
- Improved support for ZED Object Detection
- Added Skeleton Tracking support
- New Rviz plugin for Object Detection in `zed-ros-examples`
- New parameters and name changing to fit the new OD features, also the `start_object_detection` service has been modified to match the new features:
  - new `model` parameter to choose the AI model
  - new `max_range` parameter to limit the detection range
  - new `sk_body_fitting` parameter to enable Skeleton fitting for skeleton AI models
  - `people` -> `mc_people` to indicate that it is related to multiclass AI models
  - `vehicles`-> `mc_vehicles` to indicate that it is related to multiclass AI models
  - new `mc_bag` parameter to enable bags detection with multiclass AI models
  - new `mc_animal` parameter to enable animals detection with multiclass AI models
  - new `mc_electronics` parameter to enable electronic devices detection with multiclass AI models
  - new `mc_fruit_vegetable` parameter to enable fruits and vegetables detection with multiclass AI models

### [Open Capture driver](https://github.com/stereolabs/zed-open-capture)

- Fixed issue with framerate
- Added AEC AGC ROI support



## 3.4.2

### General Improvements
- Improved local streaming on receiver side by optimizing the frame packet reception rate.
This reduce a lot the number of frames dropped on the receiver.

### Bug Fixes
- Fixed a random crash that would occur when using 'sl::ObjectDetectionParameters::enable_mask_output = true'.
- Fixed a random crash that would occur when changing 'sl::ObjectDetectionRuntimeParameters::object_class_detection_confidence_threshold'
- Improved API Documentation of 'sl::FLIP_MODE'
- Fix Camera::getStreamingDeviceList() when using multiple subnetwork.


## 3.4.1

### General Improvements
- Improved local streaming on server side that reduce packet drop. 

### Bug Fixes

- Fixed a bug when using `SENSING_MODE::FILL` on jetson, causing a black depth image in fill mode. 
- Added missing texture_confidence_threshold in `Runtimeparameters::save/load` function. This parameter can now correctly be saved and loaded.
- Fixed sl::Mat::write() function when using U16_C1 mat format (provided by `MEASURE::DEPTH_U16_MM` and `MEASURE::DEPTH_U16_MM_RIGHT`).
- Fixed an issue in SVO real time mode where grab() returns an ERROR_CODE::CAMERA_NOT_INITIALIZED when next image in SVO was not available in the next 2 seconds.
  This is fixed by waiting for the next image without considering a timeout of 2 seconds.
  This only affect SVO real time mode.
- Fixed memory address sanitizer issue in Object Detection module.
- Fixed a bug with InitParameter::load/save function when using streaming input type.
  The ip/port was not properly saved in the yml file and load was therefore returning false. It is now correctly saved.
- Fixed a bug with IMU online bias correction that leads to corrupted stored bias values. Maximum correction has been decreased to avoid wrong orientation for IMU.  
  It only affects ZED-M and ZED2.
- Fixed missing `SensorsData::imu::pose_covariance` and `CameraInformation::sensors_configuration::camera_imu_transform` when using Streaming or SVO input mode. 
- Fixed a bug in sl::FusedPointCloud::save() when trying to save a fused point cloud as an OBJ file.


## 3.4.0
The new ZED SDK 3.4 introduces several updates to the API, a new two-pass offline mode in ZEDfu and many more features.
It also brings major improvements in positional Tracking when using area relocalization.


### SDK

### New Features

#### API

- Added depth map format (`MEASURE::DEPTH_U16_MM` and `MEASURE::DEPTH_U16_MM_RIGHT`) as unsigned short (`MAT_TYPE::U16_C1`) to store it in millimeters.
  This is useful for some integrations that requires a 16bits format for the depth map.

- Added depth max range for Detection module using `ObjectDetectionParameters::max_range`
  You can now set a different range for depth estimation and object detection.

- Added skeletons keypoints confidence (`ObjectData::keypoint_confidence`).
  Now each skeleton keypoints have their own confidence.

- Added a way to import OpenCV calibration using `InitParameters::optional_opencv_calibration_file`
  This allows to use a xml/yaml/json file provided by OpenCV Stereo Calibration module as an initial calibration.

- Added `Camera::updateSelfCalibration()` function to update the camera self calibration at runtime.
  It can be called anytime after `Camera::open()` has been successfully called.
  This is similar to previous resetSelfCalibration() that was removed in 3.X.

- Added `sl::Resolution getResolution(RESOLUTION resolution)` function to get the actual size (width and height) of a specific `RESOLUTION`


#### Tools
- Added an offline two-pass mode for SVOs in ZEDfu to better handle positional tracking loop closures.
  You can select the "Offline" mode in the main interface when using SVOs file in ZEDfu.
  This will first pre-process the SVO file by creating a complete relocalization database as a first step, then
  re-run the SVO file while using this database to find and close loops as a second step.
  This results into a more precise and aligned mesh compared to live method. This also reduce a lot the LOST tracking state while running the spatial mapping.

- ZEDCalibration Tool can now be used with a ZED2.

- Added a calibration procedure for magnetometer in ZED Sensors Viewer.
  You can start the calibration procedure by selecting "Magnetometer", then "calibrate magnetometer", and follow the guidelines.


### General Improvements

- Positional tracking with area memory activated performs faster and more accurate relocalization, leading to better global accuracy.
  Previously, the relocalization was sensitive to the position of the camera (requires to be close to the position saved in the database).
  Now it is less sensitive and will work even if the camera is not as close to the saved position in database as before.

### Bug Fixes
- Fixed the magnetometer data coordinate frame.
  Previously it was given in the coordinate frame of the magnetometer.
  It is now given in the coordinate frame of the IMU, so that Gyroscope, Accelerometer and Magnetometer share the same coordinate frame.

- Fixed an issue with Tegra encoder limiting the number of calls to the recording module.
  Previously, it was limited to around 900/1000 enableRecording()/disableRecording() calls due to a bug in the Tegra encoder.
  It is now unlimited.

- Fixed Sensors Data timestamp and fused IMU pose when using Streaming input mod, as reported here :
  https://github.com/stereolabs/zed-examples/issues/337.
  Now `Camera::getSensorsData()` will provide the same values if using LIVE or STREAMING mode.

- Increased image timestamp adjustment speed when the system clock changes (when using NTP/PTP synchronization).
  Previously, the maximum allowed correction was 500us for a single frame. Now it's at a maximum of 4ms per frame.
  This means that the correction should be much faster when a suddent jump happens in system clock .




### Integrations

#### Python

##### Fixes:
- In classes Matrix3f and Matrix4f: fixed transpose_mat, inverse_mat, zeros static methods
- Fixed ObjectData attribute setters
- Fixed method zeros() to create a sl.Orientation filled with zeros
- Fixed PointCloudChunk, FusedPointCloud vertices: they contained only 3D positional info and have now color information
- Fixed method to retrieve the chunks in a FusedPointCloud
- Fixed methods to get the current RuntimeParameters, StreamingParameters, RecordingParameters, RecordingStatus and StreamingProperties: some attributes could not be retrieved properly

##### Updates (from C++ SDK):
- Added method sleep_us to tell a program to sleep for a given period of time in microseconds
- Added methods to save (resp. load) SpatialMappingParameters into (resp. from) a file
- Added an ‘effective_rate’ attribute to BarometerData to retrieve the realtime data acquisition rate
- Added an attribute to sl.Camera class to check whether or not the Positional Tracking is enabled
- Added FLIP_MODE enum
- Added object_class_filter attribute to ObjectDetectionParameters to select which object to track and detect
- From 3.4: Added OD max_range and Skeleton keypoint confidence, getResolution() function


#### GStreamer
- Add compatibility fix for ZED SDK v3.4
- Add support for UINT16 Depth map
- Change parameters names in zedsrc to match the names in the ZED SDK


#### OpenNI (NEW!)
- New driver to allow to use a ZED camera with OpenNI2
- RGB, Depth and Gray streams
- Camera configuration by YAML files
- NiTE2 compatibility




## 3.3.3

### Bug Fixes

- Fixed a bug in the python script installer for Jetson JP43
- Fixed a bug in depth estimation while using VGA resolution and ULTRA mode on Jetson. The image pitch was wrong according to image size. This is now fixed and provides a correct depth Mat.

## 3.3.2

### General Improvements

- Improved C++ installer to better detect CUDA, NVIDIA driver and dependencies version. CUDA can now be automatically installed if needed on Linux
- Improved Python installer reliability, better handling of errors and unexpected host configurations
- Improved Object Tracking runtime for crowded scenes
- Improved floor plane segmentation
- Improved object 2D bounding boxes stability
- Improved Object Localization in case of static camera

### Bug Fixes

- Fixed Detection Module high CPU usage
- Fixed a bug in SVO Recording module on Jetson that created two duplicated keyframe on start


## 3.3.1
The ZED SDK patch 3.3.1 fixed bugs reported by users to make the ZED SDK even more useful. Some modules have been reworked to provide a better experience. We also improved our documentation to make it more dense and easier to understand. Wrappers and their sample have been updated to better reflect SDK capabilities.

### Bug Fixes
### SDK
- Improved Auto-Exposure behavior to fixe random overflow leading to purple images.
- Updated Depth Performance filtering to reduce occlusions, results are closer to SDK 2.8.
- Updated Depth Quality filtering to remove the quantification effect in some cases.
- Fixed an issue in Spatial Mapping module which was leading to aborting the process.
- Improved object tracking process runtime.
- Reduced object detection flickering.
- SDK installation now follow Windows rules about program drive.

### Tools
- ZEDfu no longer stop in a random way.
- Fixed ZED Diagnostic behavior with cuda 11.1.

### Integrations

#### Matlab
- Added full sensors data.
- Added missing Object Data attribute (velocity, confidence)
- Updated Samples
- Added sensors data sample

#### CSharp
- Updated C# API to match C++/python naming.
- Added NuGet compatibility for C# API.
- Added new spatial mapping sample for C# API.

#### Python
- Improved Documentation.
- Fixed issues in API leading to disjointed data into Matrix3f or Matrix4f when setting/getting values from Rotation or Orientation.
- Fixed Spatial Mapping Triangles index which was leading to false ordering.
- Updated Object Detection API.
- Samples now match C++ ones with new opengl viewers.

#### ROS
- Fixed rgb/depth asyn issue
- Improved IMU and other sensor sync
- Fixed issue with sensors data from SVO

#### ROS2
- Support for ROS2 Eloquent Elusor
- Support for SDK v3.3
- Support for ZED2
- Support for new ZED2 sensors
- Support for Object Detection with ZED2
- Switched from lifecycle node to "normal" node to improve ROS2 compatibility
- New [zed-ros2-examples](https://github.com/stereolabs/zed-ros2-examples) Github repository
- New Rviz2 plugin to visualize Object Detection results (bounding boxes and skeletons)
- New improved tutorials
- Improved Python launch files
- Updated [online documentation](https://www.stereolabs.com/docs/ros2/)

## 3.3.0

The new ZED SDK 3.3 introduces a major update of Object Detection module, bringing up to 2x improvements in accuracy and speed on desktop and Jetson platforms, along with new object classes. Body Tracking is also improved thank to a new body fitting technique. A new lightweight SDK installer is now available on Linux, along with many fixes and improvements.

### SDK

### New Platforms
- Introduced CUDA 11.1 and RTX 30x support.

### New Features

#### Object Detection

- Improved Object Detection accuracy and speed while reducing memory usage, up to 40% faster inference speed on Jetson Nano JP44 and 40% more accuracy compared to previous versions.
- Add a new Detection Model `MULTI_CLASS_BOX_ACCURATE`, 3x times more accurate than the standard 3.2 model (while being only 30% slower).
- New objects classes can now be detected, such as BAG, ANIMAL, ELECTRONICS, FRUIT_VEGETABLE.
- Detected Objects now have a sub-class, which is more descriptive.
- Detected Objects are now sorted by depth, from the closest to the farthest.

#### Body Tracking

- Add body fitting mode, resulting in smooth and realistic skeletons movements. Can be enabled using `ObjectDetectionParameters::enable_body_fitting`.
- Improved speed of skeleton detection by +40% on all platforms.
- Reduced CPU usage when using skeleton tracking.

### General Improvements

- Camera capture and grab is now more stable during long-term usage and can recover from USB connection drops.

- Camera capture timestamps (from `sl::zed::getTimestamp()`) are now continuously adjusted to host clock changes (for example when using NTP or PTP synchronization).
This allows for better synchronization between multiple ZED cameras connected to different hosts sharing the same sync source (NTP or PTP).

- `Camera::saveAreaMap` function now check if the ".area" extension is used and adds it if necessary.

- On Linux, the SDK installer has been split into a standard and full version. The installer is up to 50% lighter on desktop using the standard version. The major difference between the two version is that the full SDK installer contains the static ZED library.

### Bug Fixes

- Resolve symbols conflicts when linking with OpenCV using the static ZED library.
- Improve CUDA version detection in the Python installer script.
- Windows installers are now signed again.
- Improve ZED2 connection reliability (`open()`) when LOW_USB_BANDWIDTH was triggered as output. It can now automatically recover by restarting the camera module.
- Fix missing linear acceleration metadata in SVO files ( for ZED2) recorded with ZED Explorer.
- Fix `RecordingStatus` linking error that could occurs in some cases like dynamically loading the zed library
- Fix download location when using custom settings filepath (`InitParameters::optional_settings_path`), the previous behavior incorrectly downloaded the file into the standard path.
- getCurrentFPS function now reports correctly the application FPS (computed from the time between two consecutive grab calls), even when using SVO or Streaming inputs.
- Fix and improve Plane Detection with Depth Mode set in QUALITY.
- Fix Object Detection sample crash when using 2D mask that could occurs when the mask was empty.
- Fix Spatial mapping chunk usage with fused point clouds. The `chunk::has_been_updated` flag was not correctly set, leading to all the chunks marked as updated. Now only the chunks that have been updated during the last loop are marked as true.
- Spatial Mapping can now use all the available memory and is not limited to a power of 2 amount.
- Spatial Mapping from a SVO now processes each frame, unless it is set as real time mode. It was previously in a non synchronized thread and could skip frames when using in offline mode.
- Fix usage of an initial transform matrix in positional tracking initialization in the Python wrapper (it was previously partially ignored).

### Integrations

- ROS2 Eloquent is now supported in the latest ZED ROS 2 wrapper update.
- Updated and fixed various ROS wrapper issues.
- Improved Unity plugin support of Object detection and Skeleton tracking.
- Updated C++, Python and C# wrappers and samples. Tutorials codes are now available in the installer package.


### Known Issues

- ZED Static library is not available with JetPack 4.4.

### Deprecation

- JetPack 4.3 support is now deprecated and will be removed in the next release. In 3.3, JP 43 SDK uses the previous version of Object detection module (MULTI CLASS BOX model).
- CUDA 10.0 support is now deprecated. Users are encouraged to move to CUDA 11.0 or newer.
- Ubuntu 16.04 support is deprecated and will be removed in the next release.
- CUDA 9.0 and JetPack 4.2 support have been removed.


## 3.2.2

### Bug Fixes
- Fixed a bug on H26x SVO playback on desktop when trying to read the last frame and that last frame was a keyframe.
- Fixed a bug on H26x SVO playback when last frame was not all the time readable.
- Fixed depth occlusion detection issue that leads to misaligned depth on image movement. This also impacted the plane detection that was not consistent as in 2.7/8 version.
- Fixed a bug in Object detection mask that were not properly estimated and rendered.
- Fixed camera detection ID on Windows when other non-ZED cameras were connected.

### tools
- Fixed detection issues in ZED Sensor Viewer on Windows.
- Fixed crash when record was called on ZED Sensor Viewer on Windows when no camera are connected.


<BR>
_____
<BR>
<BR>

## 3.2.1

### New Platforms
- Introduced Ubuntu 20.04 and CUDA 11 support.
- Added support for NVIDIA JetPack 4.4 official release.

### Improvements
- Improved object detection accuracy and stability, increasing AP by +15%. Object boxes are now more stable both in detection and tracking modes.
- Improved skeleton tracking: 3D localization of skeletons has been improved, along with their bounding boxes and head position. Skeletons with unrealistic sizes have also been removed. Body tracking is still in Beta, and further improvements will be released in future updates.
- Improved playback of SVO recorded in Flip mode. They will be automatically flipped when necessary.

### Bug Fixes
- Fixed a camera freeze issue on Jetson caused by the internal USB hub of the board. Now the camera will reconnect automatically if the hub disconnects the device.
- Fixed a bug in confidence of detected objects. When 'detection_confidence_threshold' was changed, an object that was previously tracked could still be output even if the confidence was below the new threshold value. They are now removed.
- Fixed a H264/5 decoding issue when playing back SVO on Jetson platforms. Setting a frame position could previously lead to an SVO freeze.
- Fixed a positional tracking bug when using Local Streaming as input. An invalid tracking state was generated that would lead to wrong camera pose in Streaming mode.
- Fixed 'setSVOPosition()'' on Jetson platforms that was generating a wrong image sequence when grab() was called.
- Fixed a bug on sl::Mat initialisation by reference if that reference was empty. Now the sl::Mat created will be seen as uninitialized.
- Fixed an incorrect CMake output into stderr for a status message, reported by @spiderkeys.
- Fixed the performance optimizer script for Jetson NX where an incorrect power profile was selected.
- Improved AI model optimization process on Jetson platforms.

### Samples
- Added new sample "Point Cloud Mapping" to show how to map an area in point cloud format with the spatial mapping module.
- Fixed an OpenGL scaling issue in Body Tracking / Object detection samples.
- Fixed various bugs in samples.

<BR>
_____
<BR>
<BR>

## 3.2.0

The new ZED SDK 3.2 introduces a new Body Tracking module and brings major performance improvement for Object Detection.
The release also adds support for Jetson Xavier NX and JetPack 4.4, along with a new C# wrapper and an open source capture library for the ZED camera family.

### SDK

### New Features

#### Body Tracking
- Added a new AI-powered detection model that detects and tracks body skeletons in 2D and 3D. Skeleton tracking can be used on both desktop and embedded platforms such as NVIDIA Jetson.
- Body tracking is part of the Object Detection module and can be called using `DETECTION_MODEL::HUMAN_BODY_FAST` or `DETECTION_MODEL::HUMAN_BODY_ACCURATE` for a more accurate skeleton tracking at a lower FPS.
- Added new objects in the ObjectData class that contain skeleton and head data:
   * keypoint_2d for 2D human keypoints/joints (`ObjectData::keypoint_2d;`)
   * keypoint for 3D human keypoints/joints (`ObjectData::keypoint;`)
   * head_bounding_box_2d for head 2D bounding box (`ObjectData::head_bounding_box_2d;`)
   * head_bounding_box for head 3D bounding box (`ObjectData::head_bounding_box;`)
   * head_position for head 3D location (`ObjectData::head_position;`)
- Added the 3D uncertainty (covariance) of a body root position in `ObjectData::position_covariance[6]`.

#### Object Detection
- Added the 3D uncertainty (covariance) of an object position in `ObjectData::position_covariance[6]`.
- Improved Object Detection FPS on NVIDIA Jetson (Jetpack 4.4). Detection is now 2x times faster on Jetson Nano compared to 3.1.

#### Video Streaming & Recording
- Added direct stream-to-record feature to allow recording from a streaming input without re-encoding the video. This allows to dramatically reduce hardware resources when ingesting and recording multiple streams on a single PC. Use `RecordingParameters::transcode_streaming_input = false`.
- Added framerate parameter for video recording and streaming. It is now possible to capture at 60FPS for example and record or stream at 15FPS. See `RecordingParameters::target_framerate` and `StreamingParameters::target_framerate`.
- Added bitrate parameter for SVO recording with specific bitrate values (for H264/H265 SVO only). See `RecordingParameters::bitrate`.
- Added new Auto Flip mode for ZED 2 and ZED Mini (using IMU). Using `InitParameter::camera_image_flip = FLIP_MODE::AUTO` will automatically set the correct flip mode for the camera, so that you don't need to set it manually either in Live or SVO mode. This parameter will also works for SVO recorded with 3.X SDK version, since the flip mode was already recorded in the metadata. See `InitParameter::camera_image_flip = FLIP_MODE::AUTO`.


### General Improvements
- Improved floor plane detection for ZED-M and ZED 2 (using IMU).
- Improved barometer readings with sensor fusion to reduce drift during camera movements. It requires a ZED 2 sensor firmware update to v777.
- Updated `toVerbose()` function. Added CUDA error code/string in verbose message when `ERROR_CODE::CUDA_ERROR` is returned.


### Bug Fixes
- Fixed IMU data that was not flipped correctly when flip was enabled with `camera_image_flip `.
- Fixed bug in IMU orientation covariance, leading to NaN occurences.
- Fixed grab() error code that could generate an `ERROR_CODE::CAMERA_NOT_INITIALIZED` at the end of an SVO file read in real time mode . It now generates a correct `ERROR_CODE::END_OF_SVO_FILE`.


### Plugins & Interfaces
- Introduced a new C# wrapper for creating C# applications with the ZED SDK (Windows only). The [C# API](https://github.com/stereolabs/zed-csharp-api/tree/master) is now available on GitHub.
- Introduced a new open source capture library for the ZED camera family that doesn't require CUDA or NVIDIA hardware.
It provides low level access to the camera video and sensors, as well as camera controls such as exposure/gain, which were not accessible with standard capture libraries (OpenCV for example).
The project is available on GitHub as [zed-open-capture](https://github.com/stereolabs/zed-open-capture).


### Platforms
- Introduced Jetson Xavier NX and JetPack 4.4 support.


### Known Issues
- Static libraries are not available on JetPack 4.4 due to a linking issue with TENSORRT static library in JetPack 4.4. This should be fixed in the next patch.
- Updating ZED 2 sensor firmware on Windows can sometimes be unreliable. We recommend updating your camera firmware on Linux.


<BR>
_____
<BR>
<BR>


## 3.1.2

### SDK

- Improved streaming and recording FPS on Nvidia Jetson. Frame drops have been reduced from 15% to 1% when using HD720@60fps with streaming or recording module enabled on Jetson Nano.
- Fixed a bug in H264/H265 SVO playback: The first frame was not correctly read and replaced by the second frame. This is now fixed and consistent with previous SDK versions.
- Added a security in object detection maximum perception distance. Depth maximum distance is now clamped at 100 meters to avoid memory overflow when using object detection.
- Added security and warnings when input parameters (InitParameters, StreamingParameters,...) were out of range.


### Plugins

- Improved and fixed zed-docker ROS images. See here :
https://github.com/stereolabs/zed-docker/pull/17


<BR>
_____
<BR>
<BR>


## 3.1.1

### SDK

- Fixed random jumps in ZED Mini IMU data. This was due to invalid timestamps that were sometimes generated.
- Fixed depth and normals maps in `SENSING_MODE::FILL` mode with confidence at 100. NaN or Inf values have been removed.
- Fixed a coordinate system issue in the object detection module. In certain coordinate systems, the distance between the left and right camera eyes was not set correctly, leading to invalid objects and position.
- Added a warning message when requested FPS is out of range (HD2K with fps=30 for example).
- Fixed a random camera opening issue that could return a wrong serial number and an `ERROR_CODE::INVALID_CALIBRATION_FILE` error in `open()`.
- Fixed required CUDA version declaration in the CMake file.
- Fixed Windows installer splash screen that could be displayed incorrectly.

### Tools

- Fixed IMU firmware update in ZED Explorer which was broken in 3.1.0. It now works correctly.

### Plugins

- Updated Matlab plugin with 3.1 features.
- Updated Unreal plugin with 3.1 features.
- Add beta support of URP pipeline in the Unity plugin (https://github.com/stereolabs/zed-unity/tree/URP_Compatibility_beta).
- Add beta support of HDRP pipeline in the Unity plugin (https://github.com/stereolabs/zed-unity/tree/HDRP_Compatibility_beta).
- Fix dependencies issues in some ZED Docker images (Ubuntu 18 was missing libgomp).

<BR>
_____
<BR>
<BR>

## 3.1.0

### SDK

### New Features

- Added new `VIDEO_SETTINGS::GAMMA` control to adjust gamma level of the camera. Can be very helpful in low light environments.
- Added new `VIDEO_SETTINGS::AEC_AGC_ROI` control to select a region of interest for auto exposure/gain adjustment. Exposure and Gain levels will be automatically optimized for the ROI and not for the full image. This is useful in high dynamic range scenes, for example when indoors and outdoors areas are visible at the same time.
See `setCameraSettings(sl::VIDEO_SETTINGS,sl::Rect,sl::SIDE)` function for more information.
- Updated Camera Control sample to show how to use new Gamma and ROI features.
- Updated `CameraInformation` structure to improve availability of Camera, Sensors configuration and calibration information
     * Added `SensorParameter` structure to access accelerometer, gyroscope, magnetometer, barometer configuration (resolution, sampling rate, noise density...).
     * Added stereo_transform (extrinsics stereo parameters) in user `COORDINATE_SYSTEM` and `UNITS`.
     * Updated camera_imu_transform to user `COORDINATE_SYSTEM` and `UNITS`.

- Added compression level option in Mat::Write() function to adjust quality and size of PNG and JPG files.
- Added new Sensor Tutorial for reading sensor data and configuration.
<>

### Improvements

- Improved temporal stability and denoising of depth map.
- Improved accuracy of `QUALITY` depth mode.
- Improved performance when using the Object Detection module with the masks enabled.
- Jetson installation process has been improved. The installer now detects if it's running within Docker to skip any unnecessary steps. Object detection optimized AI model is now downloaded instead of being optimized on the host, making setup much faster.
- Improved cuDNN version handling.
- Added `Debug` compilation mode detection to warn about compatibility issues when mixing with Release libraries.

### Bug Fixes

- Fixed resetPositionalTracking() that was not taking into account user-defined values for the yaw angle.
- Fixed Object detection AI model optimization that could lead to a corrupted model file. Additional checks have been added to handle file corruption.
- Fixed `getCameraSettings()` behavior when using Streaming input type. Now settings are available even if setCameraSettings() has not been called before.
- Fixed `getDeviceList()` on Windows. When multiple camera were connected, DeviceProperties of one of the camera were wrong.
- Fixed multiple SVO recording in a single session. If enableRecording() was called again after disableRecording(), the SVO file was not correctly recorded.  
- Fixed `setSVOPosition()` for H264/H265 SVO files that could return an image at an incorrect position.
- Fixed 3D Object Detection AI model optimization on Jetson Nano by increasing swap file size during the installation process.
- Improved robustness and reliability of the Object Detection AI model optimization process on all platforms.
- Improved compatibility with Darknet framework by adding `undef GPU` to solve the conflict with the macro and the SDK enumerate values.

### Python API

- New Video features (Gamma, ROI selection), Camera and Sensors information added to the Python API.
- To facilitate the Python API installation, it is now compiled for each platform. The installer includes a Python script that automatically downloads the right version for your PC. See the new Installation doc for more information.
- Fixed issue with get_sdk_version() that returned an empty string.
- Fixed Export_SVO sample which didn't set the SVO input correctly.
- Fixed several getters functions that required input-output arguments instead of returning it (does not break the API).

### Tools

- ZED Explorer: Improved SVO playback. Slider can now be used anytime during video playback. Yes, this should have been the case from the start.
- ZED Explorer: Added Exposure/Gain ROI selection to the main UI window. Draw a rectangle with the mouse on the left image to adjust Exposure/Gain for this specific ROI.
- ZED Depth Viewer: Add toggle buttons to set Depth Confidence at 50 and the new Texture Confidence at 90. Depth and Texture confidence are still accessible through the settings window.

## Plugins

### Docker

- Added Python API to all Docker desktop images.
- Added Jetson `runtime` optimized images and `ros-devel` images (previously only `devel` was available).
- Added Jetson Python API specific image variants including, `py-devel`, `py-runtime` and `tools-devel` that are optimized to run with limited storage space (e.g. Jetson Nano).
- Added `NVIDIA_DRIVER_CAPABILITES` declaration in each image. It should now better control which driver features (e.g. compute, graphics) are exposed to the container.
- Reduced `devel` images size by removing unnecessary tools and graphics libraries (moved to `gl-devel` images).
- Improved [Docker Docs](https://www.stereolabs.com/docs/docker/) and [Troubleshooting](https://github.com/stereolabs/zed-docker#troubleshooting) section.

See [Stereolabs DockerHub](https://hub.docker.com/repository/docker/stereolabs/zed) repository for all available images.

### ROS

- Added new package zed_interfaces with isolated declarations of custom messages, services and actions.
- Removed `world_frame` parameter.
- Removed `publish_pose_covariance` parameter, now covariance for pose and odometry is always published.
- Removed _m from parameters mapping/resolution_m and mapping/max_mapping_range_m.
- Renamed the parameter depth_resample_factor to `depth_downsample_factor`.
- Renamed the parameter img_resample_factor to `img_downsample_factor`.
- Renamed the parameter odometry_db to `area_memory_db_path`.
- Renamed the parameter frame_rate to `grab_frame_rate`.
- Added new dynamic parameter `pub_frame_rate` to reduce Video and Depth publishing frequency irrespective of Camera FPS.
- Added new dynamic parameter `gamma` for Gamma Control.
- Added new dynamic parameter `depth_texture_conf` to filter depth according to textureness information.
- Added new TF frames for all the sensors available on ZED 2.
- Added topics to publish grayscale images.
- Added topic to publish Camera to IMU transform: /<camera_name>/<node_name>/camera_imu_transform
- Default value for depth_confidence changed from 100 to 50.
- Added base_frame as launch parameter to propagate the value of the parameter in the Xacro description.

<BR>
_____
<BR>
<BR>
##  3.0.3
### SDK
- Fix recording issue with ZED Camera or with streaming input type that was crashing the encoder.
- Fix Ubuntu16 installer that was missing turbojpeg dependencies.
- Fix Qt5 Opengl/Svg dependencies in Ubuntu installers.
- Fix old firmware (1142) support for ZED Camera on Windows.

### Tools
- Fix minimum depth distance setting in ZED Depth Viewer when graphic card was less than 3Gb GPU memory.
- Fix unresponsive GUI in ZED Sensor Viewer when a ZED Camera was connected.
- Fix ZED Calibration tool with --cimu option that was crashing on Windows.


<BR>
_____
<BR>
<BR>

##  3.0.2
### SDK
- Improved Spatial mapping performance by up to 200% on desktop and Jetson embedded platforms. Depth fusion is now much faster and the resulting model now refreshes in real-time on most platforms.
- Improved `DEPTH_MODE::PERFORMANCE`. It now benefits from neural depth estimation with improved accuracy, edges and cleaner data.
- Increased `DEPTH_MODE::PERFORMANCE` speed with a performance boost of up to 15%.
- Fixed a bug in `resetTracking` function. The yaw angle defined by the Transform parameter was not taken into account.
- Removed libturbojpeg-0-dev dependency from Ubuntu16 installer since the package is only available in Ubuntu18.
- Eliminated flickering in `VIEW::DEPTH` image when the scene has very low depth content.
- Fixed cuDNN reported version on Jetpack 4.2. The model failed to run due to an incorrect reported version. It now works correctly.

### Tools
- Added new ZED Sensor Viewer tool that lets you display accelerometer, gyroscope, barometer, magnetometer and temperature sensors charts for ZED 2.
- Added new LiDAR mode view in ZED Depth Viewer. LiDAR mode will be available in the API in ZED SDK 3.1.
- Added new Confidence 50 view in ZED Depth Viewer. It displays sparser but more confident depth maps.
- Added new Texture Confidence slider in ZED Depth Viewer. It lets you remove untextured areas such as the sky or white walls from the depth map.
- Added minimum distance settings in the saved configuration for ZED Depth Viewer, so that it takes the saved value when the tool is launched again.

### Plugins
- Updated Unity plugin to support ZED 2 and SDK 3.0.
- Updated Unreal plugin to support ZED 2 and SDK 3.0 with custom engine based on 4.21.
- Updated Matlab plugin to support ZED 2 and SDK 3.0.
- Updated ROS plugin to support ZED 2 and SDK 3.0.
- Updated ROS wrapper wiki with up to date topics, parameters and services for ZED SDK 3.0.

### App
- Updated ZED World demo to support ZED 2 and ZED SDK 3.0.
<BR>
_____
<BR>
<BR>

##  3.0.1

### Python
- Added <a href="https://www.stereolabs.com/docs/api/python/">python API documentation</a>
- Fixed an issue preventing the compilation of the python wraper

### Tools
- Updated minimum depth range in Depth Viewer to automatically adapt to available GPU memory.

<BR>
_____
<BR>
<BR>

##  3.0.0

### New ZED 2 Camera

ZED SDK 3.0 is the first release that supports the brand new <a href="https://www.stereolabs.com/zed-2/">ZED 2 camera</a>, improved in every way with wider angle optics, more accurate factory calibration, built-in new generation environmental and motion sensors and industrial grade mounting system and enclosure.

The SDK 3.0 also introduces new neural depth sensing, improved positional tracking, new AI-based object detection API that detects and tracks objects in 3D, image quality enhancement, performance improvements and a lot more features.

To update to 3.0 from previous versions, read the <a href="https://www.stereolabs.com/developers/release/3.0/migration-guide/">migration guide</a>.


### New Features    

#### Object Detection
- Added a new module that detects and tracks objects in 2D and 3D, compatible with ZED 2.
- Added `enable / disableObjectDetection` and `retrieveObjects` functions.
- Added `ObjectData` class that contains detected object data, such as 2D and 3D bounding box, tracking information, dimensions and 2D mask.

#### Depth
- Introduced neural networks for depth estimation in `ULTRA` mode to improve stereo depth accuracy. Other modes will be updated in following SDK updates.
- Introduced new confidence filter that detects homogenous zones. Use `textureness_confidence_threshold` to remove noisy values in the depth map such as sky or untextured areas.

#### Video
- Added image enhancement setting to provide more vivid and accurate colors. Enabled by default in 3.0.
- Added image sharpness setting to improve sharpening of live and recorded videos.
- Updated SVO video format to store new sensors data.

#### Sensors
- Introduced new Sensor API to provide access to data from IMU, barometer, magnetometer and temperature sensors in the ZED 2.
- Updated documentation and added tutorial to show how to use the different sensors.                                    

#### Local Streaming
- Improved local network streaming of video data between sending and receiving host computers.  
- Video Streaming API is not compatible between 2.8 and 3.0.

#### Docker
- Improved Docker support for all ZED SDK versions.
- Introduced lightweight `runtime` Docker images for deployment of Docker applications using the ZED SDK.
- Updated <a href="https://hub.docker.com/r/stereolabs/zed/">Stereolabs DockerHub</a> repository with the new images.


#### Performance
- Improved speed of the Depth module by carefully optimizing critical functions on desktop and embedded platforms.
- Optimized image rectification pipeline.

#### Software Support
- Updated to C++14 (previously C++11)
- The new  Object Detection module uses TensorRT and cuDNN to run efficiently on embedded platforms. The module is dynamically loaded from a separate sl_ai library to avoid the explicit dependency when using sl_zed.

#### Platforms
- Added support CUDA 10.2 and JetPack 4.3.
- Dropped support for JetPack 3.3 and Windows 7. While ZED may still be used on Windows 7, it is not officially supported.

.space(data-MY="60px")     
### Major 3.0 API Changes
- Since there are a lot of changes compared to ZED SDK v2, a <a href="/developers/release/3.0/migration-guide/">migration guide</a> is available to help you switch easily from 2.8 to 3.0.

#### General
- Each enum is now an enum class (forced using `TYPE::VALUE`, instead of the current `TYPE::TYPE_VALUE` or even `TYPE_VALUE`).
- All deprecated functions / parameters / options have been removed.
- All libs are now merged into sl_zed for clarity and ease of deployment.
- All headers are now merged into a single sl/Camera.hpp.
- `Camera::grab` is now blocking (guaranteed to return a new frame).

#### Functions
- `SaveAs` functions have been replaced by `Mat::save()`.
- Added getter functions to retrieve any module parameter from Camera, such as `getPositionalTrackingParameters`.
- Added static function `Camera::reboot()` to perform a hardware reset of the camera.


#### Video
- Resolution and FPS information have been moved to `Camera::getCameraInformation`.
- The `CAMERA_SETTINGS` enum is renamed to `VIDEO_SETTINGS`.
- Added `InitParameters::image_enhancement` for better black level correction, AWB adjustment and improved gamma curve
- Added `VIDEO_SETTINGS::SHARPNESS`, a digital two-pass sharpening control that improves image quality.
- Video recording is now automatically handled by the grab function, without having to explicitly call the `record()` function (now removed).
- Added a new `pauseRecording` function.
- Added `RecordingParameters` structure.
- Deprecated `RAW` and `LOSSY` compression modes. They have been removed from SDK recording options.
- `AVCHD` and `HEVC` have been renamed into `H264` and `H265`.

#### Sensors
- Added `SensorsData` class to store the data of all the other sensors present in the new ZED 2 which includes `BarometerData`, `TemperatureData`, `MagnetometerData` and `IMUData`. It also includes a state; `CAMERA_MOTION_STATE` which indicates if the camera is static, moving or falling.
- `Camera::getIMUData()` has been replaced by `Camera::getSensorsData()` to reflect the class update. It provides access to all the sensor values.


#### Depth
- Depth range and confidence parameters have been moved into `InitParameters`.
- Improved `ULTRA` mode with better accuracy and contours.                                

#### Positional Tracking
- Positional Tracking module function have been renamed to include "Positional" to differentiate it from object tracking. For instance: `Camera::enableTracking` is now `Camera::enablePositionalTracking`.
- Spatial memory has been renamed to Area memory for consistency with Area files (`enable_area_memory`, saveAreaMap`)


#### Streaming
- Added `StreamingParameters::chunk_size` to select the maximum size of each transmitted packet and improve packet transmission.

#### Spatial Mapping
- Removed all deprecated Mesh functions. The overloaded SpatialMap functions that include support for both `Mesh` and `FusedPointCloud` should now be used.
- `extractWholeSpatialMap` is now smarter if a previously retrieved map is passed as an argument. It will retrieve only the difference between the initial and updated mesh.

#### Compatibility
- Streaming module is not compatible between 2.8 and 3.0.
- Due to a change in SVO metadata, SVOs recorded with 3.0 will only be compatible with SDK 2.8.4 and upwards.

<BR>
_____
<BR>
<BR>

##  2.8.4

### SDK

#### General

- Fixed a memory leak in Streaming module on Jetson that continuously increased memory until the camera was closed.
- Improved IMU timestamp accuracy. Timestamp is still based on epoch time and IMU clock drift is now compensated.
  Note that `getIMUData(TIME_REFERENCE_IMAGE).timestamp` and  `getTimestamp(TIME_REFERENCE_IMAGE)` can now be slightly different since they are not based on the same computation, however the difference will be less than the time of a frame.
- Fixed a wrong translation scale that was applied in `getCameraInformation().camera_imu_transform`. Translation was given in meters when the selected unit was in millimeters.

#### Tools
- Fixed ZED Explorer which was still creating a UI interface when used in command line. Command line options are now available even if no screen is connected to the host PC/Jetson.


#### Platforms
- Fixed Jetson clock script so that it correctly enables maximum performance (nvpmodel -m 0) with the new JetPack 4.2.


<BR>
_____
<BR>
<BR>

##  2.8.3

### SDK

#### General

- Fixed multiple camera detection on Ubuntu 18.
- Fixed getCameraSettingsValue() when using network streaming input. It was not fully supported on previous versions.
- Fixed random freeze on Windows when using network streaming input.
- Fixed retrieveImage() for Side by Side View mode that would lead to an empty image.
- Fixed error code that returned SUCCESS when retrieveImage() or retrieveMeasure() was used with a resolution higher than the camera resolution. Functions will now return ERROR_CODE_INVALID_RESOLUTION.

#### SVO
- Fixed a bug in LOSSLESS compression on Linux when using ZED Mini cameras. IMU metadata was not reported in the SVO file, leading to wrong tracking states.
- Fixed metadata synchronization issue where IMU and image data could be out of sync in H264 (AVCHD) and H265 (HEVC) SVO files recorded on Desktop platforms.

#### Samples
- Fixed support of Visual Studio 2019 for Samples that required GLEW library.
- Improved mouse control in Positional Tracking sample.

#### Tools
- Fixed SVO playback controls in ZED Explorer. Previously, the "+/-" button could jump one frame each time it was clicked.
- Fixed SVO conversion to H264 (AVCHD) and H265 (HEVC) in ZED SVO Editor tool.
- Improved zoom in/out control in Depth Viewer.

### Plugins

#### Unity
- Added support for building applications with Unity and ZED on Linux. See [zed-unity repository](https://github.com/stereolabs/zed-unity) for more information.
- Fixed issue when using multiple cameras that could lead to a crash if both cameras shared the same CAMERA_ID.
- Fixed minor issues in the plugin. See [zed-unity release notes](https://github.com/stereolabs/zed-unity/releases) for more details.

<BR>
_____
<BR>
<BR>

##  2.8.2

### SDK

#### General
- Added static sl_svo library on Linux. All ZED SDK libraries are now available as static libraries.
- Fixed SVO playback speed in ZED Explorer. SVO are now played at their capture speed.
- Applied multiple minor UI updates in ZED Explorer.
- Fixed an issue preventing the tools to be launched with a double click on Ubuntu 18.

#### Video
- Fixed unrectified image orientation in Flip mode.
- Fixed a potential issue leading to file corruption when multiple programs accessed the camera calibration file at the same time.

#### Positional Tracking
- Updated IMU output to remove gyroscope bias correction from raw data. This will improve SLAM algorithms that include online bias estimation and use the IMU raw data.
- Fixed enable_imu_fusion function. When set to false, it now effectively disables IMU use for visual odometry estimation.

#### Spatial Mapping
- Fixed a memory detection issue preventing the mapping to start when <2GB of RAM was available.

#### SVO
- Fixed a potential issue when reading the last frame of an SVO file.
- Added support for ~ character in SVO paths on Linux.

#### Samples
- Added ArUco Marker detection sample showing how to relocalize the camera during tracking.
- Updated OpenCV-related samples for compatibility with OpenCV 4.
- Fixed installation of the SVO recording sample.

<BR>
_____
<BR>
<BR>

##  2.8.1

### SDK

#### Positional Tracking
- Fixed IMU bias correction that could lead to slow movement not being correctly tracked.

#### SVO
- Fixed automatic low recording bitrate when target FPS is not reached.

#### Tools
- Fixed ZED Explorer image stall when taking a screenshot.
- Removed debug message in ZED Explorer console.

<BR>
_____
<BR>
<BR>

##  2.8.0

### New Features

- Streaming over IP network
- ZED SDK 2.8 introduces a brand new streaming feature. Users can now connect as many ZED cameras as needed directly to a network and conveniently access the video feeds simultaneously, on multiple devices and applications. [Get Started](https://www.stereolabs.com/blog/how-to-set-up-a-multi-camera-network/).
- Also, Ethernet cabling greatly extends the distance allowed between the ZED camera and the host device making installation and placement of ZED cameras more flexible and scalable.
Introducing a brand new [ROS 2 wrapper](https://www.stereolabs.com/blog/zed-wrapper-for-ros2/) for the ZED SDK, taking advantage of all the new features and optimization of the new ROS 2 version.

### SDK

#### General
- Added Streaming capabilities. The ZED SDK can now stream data from local cameras over the network, for multiple remote computers running the SDK to use as input.
- Added support for scanning into point clouds using Spatial Mapping.
- In collaboration with NVIDIA, solved an issue on Jetson preventing the ZED Mini from being detected after reboot. Fix is available in Jetpack 4.2 and will be applied to older versions.
- Fixed an issue on Jetson TX2 leading to incorrect floor plane detection with the ZED Mini.
- Fixed image timestamping issue that could lead to offsets when using `getTimestamp(TIME_REFERENCE_IMAGE)`.

#### Streaming
- Added input.setFromStream(...) function in InitParameters to open a streaming camera.
- Added enableStreaming(...) and disableStreaming() functions to start and stop a stream from a camera.
- Added StreamingParameters structure to setup custom bitrate, port and codec of a camera stream.
- Added isStreamingEnabled() function to get the streaming status of a camera.
- Added getStreamingDeviceList() function to list the camera streams available on the network.

#### Spatial Mapping
- Added map_type parameter in SpatialMappingParameters to select between mesh or point cloud-based mapping.
- Added SPATIAL_MAP_TYPE enumerate to list available mapping types (mesh vs. point cloud).
- Added extractWholeSpatialMap(...) function, deprecating extractWholeMesh(...).
- Added retrieveSpatialMapAsync(...) function, deprecating retrieveMeshAsync(...).
- Added requestSpatialMapAsync() function, deprecating requestMeshAsync().
- Added getSpatialMapRequestStatusAsync() function, deprecating getMeshRequestStatusAsync().
- Fixed an issue that could lead to a crash during mesh texturing in Unity.

#### API
- Added CAMERA_SETTINGS_LED_STATUS in CAMERA_SETTINGS to allow users to enable or disable the camera LED.
- Updated Jetson installers. There is now one installer per Jetpack version that covers all supported Jetson boards, instead of one installer per board and per Jetpack.

#### SVO
- Fixed memory leak in disableRecording() function.
- Introduced optional_settings_path to select a custom calibration file path.

#### Positional Tracking
- Added enable_imu_fusion parameter in TrackingParameters to disable camera-IMU odometry fusion when using a ZED Mini in specific environments such as high vibration platforms.
- Updated TrackingParameters.enable_pose_smoothing behavior to better track high-speed movements of the camera when disabled.
- Updated Pose.pose_covariance behavior when using spatial memory. The covariance is now accessible when TrackingParameters.enable_spatial_memory is activated.
- Deprecated Pose.pose_confidence attribute. Use Pose.pose_covariance instead.
- Introduced camera_disable_imu to disable the imu opening in live mode. This allows opening a ZED-M on USB 3.0 only extensions cord such as optical fiber.

#### Samples
- Introduced Streaming Sender and Streaming Receiver samples to help users get started with the new feature.

#### Tools
- Added a feature to ZEDfu to scan and export as fused point cloud.
- Fixed a graphics resizing issue in ZED Explorer on Ubuntu 18.
- Fixed an issue in SVO Editor that prevented cutting an SVO within the first few frames.


### Plugins

#### Python
- Updated API for compatibility with 2.8 release.
- Added 2.8 streaming capabilities.
- Added native OpenCV sample to open the camera without the ZED SDK on non-NVIDIA computers.

#### ROS 2
- Releasing the beta version of the new ROS 2 wrapper.

#### ROS
- Updated API for compatibility with 2.8 release.
- Moved parameters from launch files to YAML files. This makes them clearer and unifies parameters between different launch files.
- Added minimum depth parameter (min_depth).
- Added base_link to the TF tree to improve user experience when adding ZED cameras to a standard ROS robot configuration. Pose and Odometry are now referred to base_link instead of zed_camera_center.
- Added a separated launch file for ZED Mini (zedm.launch and display_zedm.launch).
- Added a service to set the ON/OFF status of the blue LED (firmware > 1523).
- Added a service to toggle the status of the blue led (firmware > 1523).
- Added a parameter to choose the default SVO compression mode for SVO recording.
- Added 2D mode for stable navigation on planar surfaces.
- Added option to set a fixed pose/odometry covariance or use the dynamic matrices calculated by the SDK.
- Added SVO recording services.
- Added zero-copy to Pointcloud publishing (thanks @RhysMcK).
- Added dynamic parameter to change the frequency of point cloud publishing. Now the point cloud can have a frequency different from the grab frequency (always less than or equal to the grab frequency).
- Added services to start/stop network streaming, which allows remote machines to acquire and process images using a ROS node or a "not ROS" application.
- Added stereo side-by-side image topic for raw and rectified images.
- Removed initial warning about TF not being available.
- Improved console logging for a better vision of the node configuration.
- Fixed additional minor issues.

#### Unity
- Updated API for compatibility with 2.8 release.
- Added support for multiple cameras on the same PC - complete refactoring of the plugin to support this.
- Added Multicam sample to demonstrate how to use more than one ZED camera at once.
- Added support for the SDK's new streaming features - direct USB connection no longer required.
- Added Pose Smoothing to positional tracking relocalization. Enabling spatial memory for tracking no longer creates "jumps" in position estimation.
- Added Estimate Initial Position feature to set the ZED's pitch and height based on automatic floor plane detection at start.
- Removed dependence on Unity layers for showing the correct real-world feed to the proper camera. Users can now adjust ZED rig culling masks like any Unity Camera.
- Moved Spatial Mapping and SVO recording/playback to ZEDManager, removing ZEDSpatialMappingManager and deprecating ZEDSVOManager.
- Simplified how detected planes are drawn in scene, giving more control and allowing them to be drawn at the same time as a spatial mapping mesh.
- Fixed ZED rig drift when tracked with ZEDControllerTracker. This was caused by how the latency compensation was handled.
- Fixed long pause when closing applications while a ZED was initializing.
- Updated controller scripts to work with SteamVR Unity plugin version 2.
- Fixed additional minor issues.

#### Unreal
- Updated engine and plugin to support 2.8 release.
- Added Pose Smoothing to positional tracking relocalization. Enabling spatial memory for tracking no longer creates "jumps" in position estimation.
- Unreal engine version updated to 4.21.


<BR>
_____
<BR>
<BR>

##  2.7.1

### SDK

#### General
- Fixed a minor range issue in the depth confidence.

#### SVO
- Fixed an issue affecting the playback on a PC of an H264 SVO recorded on Jetson.

<BR>
_____
<BR>
<BR>

##  2.7.0

The ZED SDK 2.7 release introduces CUDA 10, Ubuntu 18 and Jetson Xavier support, H264/H265 compression for SVO, new TensorFlow and YOLO samples and static compilation support on Linux.

### SDK

#### API
- Added support for `CUDA 10`.
- Added support for `Ubuntu 18.04`.
- Added support for the `Jetson Xavier` platform.
- Added static compilation support on Linux.
- Fixed random freeze when disconnecting a camera while in use.

#### SVO
- Added `H264` and `H265` hardware encoding and decoding with NVENC for SVO files. A single SVO is now up to 40 times smaller for the same image quality. Thanks to hardware encoding, CPU utilization is now minimal when recording SVO, and drop frames have been eliminated.

#### Positional Tracking
- Added `enable_imu_fusion` parameter in `TrackingParameters` to disable the camera-IMU odometry fusion when using a ZED Mini.

#### Samples
- Introduced a [TensorFlow sample](https://github.com/stereolabs/zed-tensorflow) for 3D object detection with the ZED and TF.
- Introduced a [YOLO sample](https://github.com/stereolabs/zed-yolo) for 3D object detection with the ZED and YOLO in C++ and Python.
- Introduced a [Docker example](https://github.com/stereolabs/zed-docker) showing how to run a ZED SDK image as a container. Images are available on [Docker Hub](https://hub.docker.com/r/stereolabs/zed/).

#### Tools
- Fixed hot plugging that could cause a freeze in ZED Explorer.
- Fixed issue preventing downgrading firmware in ZED Explorer.
- Fixed various minor issues with firmware update in ZED Explorer.
- Fixed various minor memory issues that could lead to stability issues in ZED Explorer.
- Fixed minor memory issue leading to empty mesh in ZEDfu.
- Fixed graphical issue preventing launching ZEDfu on Ubuntu.


### Plugins

#### ROS
- Added covariance data to the odometry message.
- Added pose_with_covariance topic.
- Added Odometry and Pose path messages.
- Added floor alignment feature in positional tracking to align the World Frame orientation with the foor plane.
- Removed OpenCV dependency.
- Added parameter to disable the publishing of the map frame.
- Added parameter to disable the publishing of the covariance in odometry and pose.
- Added three new tutorials about Video, Depth and Pose subscription in C++.
- Updated the plugins structure in RVIZ display.
- Updated documentation with new tutorials about Video, Depth and Positional Tracking modules.
- Moved "zed_nodelet_example" and "zed_rtabmap_example" in the new "examples" folder.
- Fixed reconnection issue. Thanks to @RhysMcK.
- Fixed Pointcloud structure issue. Thanks to @ChristofDubs.

#### Unity
- Added toggle box to reveal a camera rig used for final HMD output, normally hidden, for advanced users.
- Added toggle boxes for the fade-in effect when the ZED initializes, and setting the ZED rig Gameobject to "DontDestroyOnLoad" on start.
- Updated the previously hard-coded layers the ZED plugin used (8-11) to be adjusted in the Inspector, and set them to 28-31 by default.
- Updated greenscreen config file loading so that it will work in a build when placed in a Resources folder, and changed default save path accordingly.
- Updated error messages from playing SVOs in loop mode with tracking enabled.
- Fixed Rift/Vive controller drift when using ZED's tracking instead of the HMD's.


<BR>
_____
<BR>
<BR>

##  2.6.0

### SDK

#### General
- Added new entry in Init Parameters sl::InitParameters::optional_settings_path. This allows to set a new path for looking at the SN<xxx>.conf calibration files. See the API documentation for examples.
- Warning messages are now displayed only if InitParameters::sdk_verbose is enabled. This allows a clean console when verbose is not activated.
- Fixed incorrect string message for `sl::COORDINATE_SYSTEM::COORDINATE_SYSTEM_RIGHT_HANDED_Z_UP_X_FWD`.
- Fixed random freeze on `Camera::close()` on Ubuntu when camera was unplugged during frame capture.


#### Positional Tracking
- Improved overall speed of positionnal tracking computation.
   - 50% more efficient on Tegra.
   - 50% more efficient on linux desktop.
   - 15% more efficient on windows.
- Added covariance estimation of the Pose.  
- A new field has been added in `sl::Pose` called pose_covariance.
- For this version, the covariance is only calculated if `sl::TrackingParameters::enable_spatial_memory` is disabled.
- Added detection of initial orientation and position of the camera.
- Added `sl::TrackingParameter::set_floor_as_origin option` to automatically start the tracking at the detected initial position.
- The initial position estimation uses the floor plane detection to estimate the height and orientation of the camera. Make sure that the floor plane is visible enough to have a proper estimation.
- Tracking state will be in `TRACKING_SEARCH` state until the floor plane has been detected and the position of the camera estimated.

#### Tools
- Fixed low depth framerate in ZED Depth Viewer on Ubuntu Desktop.

### Firmware
- A new IMU firmware for the ZED-M has been introduced in ZED SDK 2.6*
- The IMU framerate is now running at 800Hz, compared to 500Hz in previous firmware.
- This improves the IMU fusion with a better accuracy and less drifts.
- The Synchronization between the camera stream and the IMU stream has been improved for a better visual-inertial tracking. (ZED-M only).

<BR>
_____
<BR>
<BR>


##  2.5.1

### SDK
#### General
- Fixed segmentation fault when using the ZED in USB2.0 on Ubuntu.
- Fixed memory leak on sl::Camera::getDeviceList().
- Fixed a bug that leads to a wrong camera detection in getDeviceList() when this function was called a lot of time.
- Fixed wrong timestamp in IMUData when getIMUData(sl:TIME_REFERENCE_CURRENT) was called. Now, you can use the timestamp to check if the values are new (timestamp will be different, otherwise they will be the same).


<BR>
_____
<BR>
<BR>

##  2.5.0

### SDK
#### General
- Added new coordinate system RIGHT_HANDED_Z_UP_X_FWD for better support on ROS.
- Moved GPU computation in a non-default stream for better GPU overlapping and performance improvements with CUDA-based third party libraries.
- Added IMU to camera transform in IMUData class (only for ZED-M). This can be used if using RAW IMU data to obtain a quaternion (in IMU frame) and express it in Left camera frame.

#### Depth
- Fixed Nan value in FILL mode when confidence is at 100%.

#### Planes
- Fixed scaling bug when UNIT parameters was not in METERS.

#### SVO
- Fixed timestamp to 0ULL (instead of -1) for legacy RAW compression mode.

### Plugins

#### Unity
- Added Drone Fight demo (similar to ZEDWorld).
- Added Movie Screen demo (similar to ZEDWorld).
- Added Object Placement with Planes demo.
- Multiples bug fixes and improvements (see Unity release notes in unity package or on Github).
- Updated plugin compatibility with ZED SDK 2.5.

#### Unreal
- Updated custom engine to UE4.19.1.
- Updated plugin compatibility with ZED SDK 2.5.
- Added option in BP_Initializer for selecting the Pawn tracking mode:
   - Full ZED tracking
   - Full HMD tracking
   - Mixte tracking (position from HMD and rotation from ZED)
- Added SRemap experimental rendering option which improves the pass-through.
- Moved BP_ZedCamera blueprint class to C++ only.
- Global performance improvement.
- Improved Vive support.
- Fixed a bug making the Editor crash randomly when closing the game preview.
- Fixed a bug saying "wrong reference frame" when starting game preview.

#### ROS
- Updated wrapper compatibility with ZED SDK 2.5.
- Github example code refactored in a more clear format
- Updated "package.xml" to "Format 2" (http://wiki.ros.org/catkin/package.xml). Thanks to rbonghi
- Odometry is now published when subscribing to point cloud topics. Thanks to ArkadiuszNiemiec
- Added launch example to run the ROS wrapper nodelet in a standalone nodelet manager. Thanks to Russ76
- Added a publisher for internal IMU data. Thanks to tonftybj
- Added a publisher for disparity image. Thanks to fujimo-t
- Fixed minor bugs


### App
- The complete source code of the multiplayer PingPong demo based on Unreal has been released on Github! The binary package is also available in the release section of Github.


### Platforms
- Updated to CUDA 9.2 and kept the version with CUDA 9.0

 <BR>
_____
<BR>
<BR>

##  2.4.1

### SDK
#### Video
- Improved asynchronous SVO pipeline with up to 30% more recording bandwidth.
- Reduced frame latency on Linux platforms. The image buffer is now filled asynchronously instead of waiting for a grab() call.
- Fixed a recording issue leading to IMU data not being saved in the SVO file.

#### Depth
- Improved depth estimation and stability on homogeneous surfaces in FILL sensing mode.
- Improved sharpness on depth edges in FILL sensing mode.

#### Tracking
- Improved robustness against near moving objects. (Like user hands in AR)
- Updated enable_spatial_memory parameter behavior. Disabling it now provides a pure Visual Odometry.
- Updated relocalization and loop closure rules to improve the tracking accuracy.

#### Samples
- Introduced a zed-opencv-native sample for stereo image capture without the SDK.

### Tools
- Added an SDK version management to keep you informed about new updates and features.

### Jetson
- Major performance improvements on Jetson boards thanks to internal SDK optimizations and updated CPU and GPU frequencies available during the SDK installation.


<BR>
_____
<BR>
<BR>

##  2.4.0

ZED SDK 2.4 introduces a major firmware update for ZED and ZED Mini, bringing multi-camera support to Windows and Linux and improving significantly USB connectivity on Windows 10.
We also introduce plane detection for a better understanding of the environment, as well as various fixes and improvements to the ZED API, tools, samples, and documentation.

### SDK
#### Depth
- Improved depth edges and details in PERFORMANCE and ULTRA modes.
- Improved depth reliabiliity by removing oversaturated areas detected in the images.
- Improved confidence maps for better usability.
- Updated confidence threshold to 97% in Depth Viewer.
- Improved stability of MEDIUM and QUALITY depth modes in STANDARD sensing.
- Fixed depth stabilization bug that could occur when reading an SVO.
- Fixed depth stabilization bug that could disable the stabilizer if tracking state switched to SEARCH.

#### Tracking
- Improved positional tracking accuracy.
- Fixed issue with IMU that would increase drift when using visual-inertial positional tracking.
- Fixed an issue where the initial tracking position would be incorrect when playing back an SVO taken with a ZED mini.
- Fixed an issue preventing tracking from being enabled with an SVO recorded with a ZED mini where self-calibration was disabled.
- Fixed a potential crash when restarting positional tracking.

#### API
- Added Camera::findFloorPlane function for floor detection and improved tracking initialization.
- Added Camera::findPlaneAtHit function that returns the best matching plane at a selected pixel position in the image.
- Added Plane object to store information about a detected plane, such as its center, normal, bounds, etc.
- Updated open() function to allow SDK, tools and plugins to open the device 0.5s faster.
- Reduced SVO compression ratio in Lossy mode to improve image quality.
- Deprecated function Camera::setFPS.
- Fixed an issue that could allow CameraInformation::camera_imu_transform to be empty.

#### Samples
- Introduced a multi-camera sample showing how to capture data from multiple connected devices on Windows and Linux.
- Introduced a new tutorial showing how to use the new findFloorPlane function.
- Introduced a plane detection sample to display planes detected by the new findPlaneAtHit function.
- Introduced an OpenGL example code on GitHub to show how to display camera feed with OpenGL.
- Updated positional tracking example code to show how to get IMU data from a ZED Mini.
- Updated Matlab plugin with getIMUData function, giving access to the ZED Mini IMU values.

#### Documentation
- Updated Mesh API reference documentation to describe how textures are linked to vertices.
- Updated Unity Spatial Mapping section to add missing steps in the tutorial.

### Tools
- ZED Explorer now opens a device up to 1.0 second faster.
- Updated ZED Diagnostic tool messages for clarity.
- Added a smudge detector in ZED Diagnostic to warn users when the camera lenses need to be cleaned.
- Minor ZED Calibration tool GUI update for readability.
- Updated ZED Depth Viewer settings to display ULTRA depth mode by default.
- Updated the settings GUI in ZED Explorer.

### Plugins
- ROS - Added dynamic adjustment of gain and exposure settings in ROS wrapper (Thanks to GitHub user @fujimo-t).
- Unity - Major update of the plugin. See changes here.
- Unity - Released plugin source code on GitHub. Contributions are encouraged!

### Platforms
- Updated to JetPack 3.2 for Jetson TX1 and TX2.
- Updated Jetson installer to enable MAX-N performance mode by default.
- Added Ubuntu installer compiled with CUDA 9 for better TensorFlow compatibility.



<BR>
_____
<BR>
<BR>

 ##  2.3.3

### SDK

#### Depth
- Fixed right disparity values being over filtered when using enable_right_side_measure.

#### Tools
- Updated automatic camera calibration download to avoid caching issues.

<BR>
_____
<BR>
<BR>

##  2.3.2

### SDK

#### Compatibility
- Removed a registry key generating side effects on Windows Mixed Reality Headsets.
- Fixed CUDA installation on Windows 7.
- Fixed Redistributables installation on Windows 10.

#### Stability
- Improved internal image grabbing process against camera reconnections.

#### Depth
- Improved confidence map precision in Performance and ULTRA depth mode.

#### SDK Tools
- Fixed several bugs in ZED Explorer leading to unexpected crashes of the application.
- Updated point cloud preview in the Depth Viewer.
- Updated Diagnostic tool to handle more situations.
- Updated firmware update mechanism in Windows 10 to authorize downgrading.

#### ZED World
- Embedded CUDA version to simplify the installation.
- Fixed latency correction and image projection in HMDs for a smoother experience.



<BR>
_____
<BR>
<BR>



##  2.3.1

### SDK

#### ULTRA Depth Mode
- Optimized memory usage, it now uses only 50% less GPU memory.
- Optimized depth density, ULTRA depth map are more dense by default.
- Optimized confidence map, confidence values are more linear and accurate.
- Fixed an issue when retriving normals in VGA resolution with the ULTRA mode.

#### Camera Settings
	- Fixed an issue leading to exposure and gain settings to be applied on the left image only.

#### Tracking / SVO Playback
	- Fixed tracking behavior during the playback of an SVO recorded with a ZED mini.


<BR>
_____
<BR>
<BR>


 ##  2.3.0

### New Features

#### Introducing stereo passthrough in Unity:
- Added support of the ZED mini camera.
- Added stereo passthrough feature with optimized rendering in VR headsets

#### SDK device detection:
- Connected ZED and ZED mini cameras can now be listed before opening them.

#### IMU-optimized tracking for ZED mini:
- The new ZED mini supports comes with an optimized tracking fusion that takes advantage its integrated IMU.
#### New ULTRA depth mode:
- Introducing a new ULTRA depth mode for an improved depth accuracy in reduced-real-time applications.

### SDK
#### ZED mini support
- Added sl::Camera::getDeviceList() static function to get the connected ZED and ZED mini in a vector of sl::DeviceProperties.
- Added sl::DeviceProperties structure to contain the information of each device.
- Added sl::MODEL enumerate to diferentiate the ZED mini from the ZED.
- Added camera_model information in the sl::CameraInformation structure.
- Added sl::Camera::getIMUData() function that give access to the fused and RAW IMU data.
- Added sl::IMUData to store the IMU values filled by getIMUData().
- Added sl::Camera::SetIMUPrior() function as an input for external IMU sensors.

#### Updated API
- Added sl::DEPTH_MODE::DEPTH_MODE_ULTRA for the new ULTRA depth mode.
- Added sl::Camera::getPlaneAtHit() function to get plane estimations from the depth information.
- Added sl::Plane object to store the plane information
- Added sl::Camera::getTimestamp() to replace the existing timestamp functions.
- Added sl::TIME_REFERENCE to switch between live data and frame synchronized ones in function cals.
- Changed timestamp reference form PC uptime to epoch.
- Deprecated sl::Camera::getCurrentTimestamp(), replaced by sl::Camera::getTimestamp(TIME_REFERENCE_CURRENT)
- Deprecated sl::Camera::getCameraTimeStamp(), replaced by sl::Camera::getTimestamp(TIME_REFERENCE_IMAGE)
- Deprecated SVO_COMPRESSION_MODE_RAW in SVO writing modes. Playback is still available.

#### Tools
- Introducing a graphical interface for the Diagnostic tool.
- Improving Diagnostic tool accuracy and messages.
- Adding the ZED mini 3D representation in Depth Viewer.

### Plugins
- Unity:
- Added ZED mini support.
- Introducing stereo passthrough for VR headsets.
- ROS:
- Added ZED mini support.
- Added ZED camera selection from serial number.
- Fixed occasional crash when closing the wrapper.

### Dependencies
- Introducing CUDA 9.1 support. (CUDA 8 for Jetson TX1 and TX2)

### Known Issues
- On certain configurations, VRCompositor in SteamVR can freeze when using HTC Vive and ZED. Disabling Async Reprojection in SteamVR can fix the issue.


<BR>
_____
<BR>
<BR>


 ##  2.2.0

### New Features

#### Performance improvements:
- Improved depth computation performance by 20%. Reduced memory usage.
- Improved positional tracking speed by 15%.
- Updated internal SDK architecture to reduce and stabilize overall grab() time. 2.3 will further improve grab() time, especially for applications using depth and positional tracking.

#### Major update of Unity plugin:
- The plugin has been completely refactored to simplify the creation of interactive AR applications.
- Added Spatial Mapping to enable physical interactions with the real-world environment.
- Updated VR Green Screen sample. Improved chroma key, VR controller support, general usability and fixed various bugs.    

#### SDK version detection:
- Applications that use the ZED SDK are now able to identify the installed SDK version before loading it.
- When loaded by an executable, the ZED SDK now detects the expected ZED SDK version and self-adapt to ensure runtime compatibility.

#### External CUDA context:
- SDK CUDA context can now be retrieved to allow context sharing within an application.

#### **Gravity orientation estimation**:
- Real-world gravity orientation can now be estimated after mapping an area.
- The dominant floor plane is extracted from the mesh and its normal is used to estimate the gravity vector.

#### CMake find_package:
- The ZED SDK can be linked to a project by using the find_package command on Cmake (previously only supported on linux).

### SDK
#### Updated Video API
- Improved real-time SVO playback mode. SVO will now always play at their capture frame-rate, not faster.
- Fixed Flip mode on Nvidia Jetson boards
- Fixed an issue affecting SVO recording on Linux.

#### Updated Depth API
- Reduced depth computation time by 20% and improved stability.
- Reduced GPU memory usage by x2 in PERFORMANCE mode.

#### Updated Positional Tracking API
- Updated positional tracking behaviour. Tracking now always returns a camera pose, even when tracking state is "Searching..." or saving an area file.
- Fixed tracking reset. Reset tracking no longer freezes the application when calling disable/enable in a row.
- Fixed tracking bug at high velocity. Tracking now correctly returns a pose when moving at high speed.
- Fixed bug in Spatial Memory that would sometimes make the pose jump to an incorrect location.
- Introduced Camera::saveCurrentArea() function to save an area file without having to disable tracking.

#### Updated Spatial Mapping API
- Improved Spatial Mapping API stability.
- Introduced Mesh::getGravityEstimate() to estimate gravity vector using mesh.
- Refactor MeshFilterParameters::MESH_FILTER, optimized for a better fitting of the initial Mesh.
- Renamed SpatialMappingParameters::RESOLUTION as MAPPING_RESOLUTION to avoid confusion with sl::RESOLUTION.
- Renamed SpatialMappingParameters::RANGE as MAPPING_RANGE.
- Renamed MeshFilterParameters::FILTER as MESH_FILTER.
- Renamed SpatialMappingParameters::keep_mesh_consistent as use_chunk_only. This parameter allows to disable mesh update and speed up applications which only use chunk lists.
- Renamed SpatialMappingParameters::invert_vertex_order as reverse_vertex_order. This parameter allows to retrieve mesh vertices in clockwise or counterclockwise order.
- Renamed SpatialMappingParameters::range_meter.second as SpatialMappingParameters::range_meter.
- Renamed SpatialMappingParameters::allowed_max as SpatialMappingParameters::allowed_range.
- Removed SpatialMappingParameters::range_meter.first.
- Removed SpatialMappingParameters::allowed_min.

#### General SDK changes
- Added Camera::getZEDSDKBuildVersion() and Camera::getZEDSDKRuntimeVersion() functions to allow SDK version detection at runtime. Applications on Windows are now able to check if a compatible version of the SDK is available before loading the SDK libraries.
- Added InitParameters::sdk_cuda_ctx input to allow context sharing within an application.
- Improved thread safety.
- Improved ZED USB device detection on certain Windows 10 configurations.
- Fixed "Corrupted SDK" error message that would trigger when opening the camera.
- Removed keep_occluded_point parameter in savePointCloudAs(). New behavior is set to keep_occluded_point = false.
- Deprecated independent string conversion functions *2str() in favor of a new universal toString() one.

### Plugins
- Unity:
- Refactored plugin organization and architecture.
- Added Spatial Mapping feature to allow physical interactions with the real-world environment.
- Improved chroma key in Green Screen sample. Matte edges are now much cleaner and sharper.
- Improved PBS lighting in forward rendering mode.
- Added AR post-processing shaders to enable realistic integration of virtual objects in ZED video.   
- Added OpenGL support.
- Added camera loading screen with error reporting.
- Added callbacks reporting the camera status.
- Fixed SVO reading speed when playing back recorded videos in Unity.
- Fixed multithreading issue leading to crashes on computers with low resources.
- ROS:
- Updated wrapper to ensure compatibility with ZED SDK 2.2.
- Removed X11 dependency to allow usage of remote ROS nodes without display.
- Matlab:
- Updated interface to ensure compatibility with ZED SDK 2.2.

### Samples and Tutorials
- SVO Export sample is now able to save 16-bit images of the depth from an SVO file.

### Tools
- ZED Depth Viewer: Fixed point cloud rendering and export issues leading to empty files.
- Updated Diagnostic tool.

### Dependencies
- Removed X11 on Linux to allow SDK access and control from a remote SSH connection.

### Known Issues
- On certain configurations, VRCompositor in SteamVR can freeze when using HTC Vive and ZED. Disabling Async Reprojection in SteamVR can fix the issue.


<BR>
_____
<BR>
<BR>

##  2.1.0/1

### Major New Features

#### Major update of Depth Sensing API
- New depth stabilization mode is now available. It brings significant improvement to depth sensing accuracy and noise reduction. It is enabled by default and requires the positional tracking (enabled automatically).
- Images/Depth/Measures resolutions can now be adjusted in retrieve*() functions, speeding up applications that need lower resolution Mat.
- Normal maps are now available for the left and right eye.
- Depth map and point cloud are now available for the right eye.

### Plugins
- Unity:
- Added Light script to enable light interactions between the real and virtual world.
- Added Vive tracker support.
- Added Oculus Rift support in Green Screen MR sample.
- Added a semi-automated calibration script to align the real and virtual world in Green Screen MR sample.


### Dependencies
- Removed OpenCV as an SDK dependency.


### SDK

#### Updated Depth API:
- Added sl::Resolution parameter in retrieve*() function to allow retrieval of smaller depth resolutions.
- Added RuntimeParameters::depth_stabilization to enable depth stabilization. Enabled by default in 2.1.
- Added MEASURE_NORMALS as a new MEASURE to extract the normal map of the scene. Output is an sl::Mat with 4 channels (x,y,z,empty) of 32bit float.
- Added InitParameters::enable_right_measure for all measure types to extract a measure MEASURE_XXX_RIGHT mapped on the right image.
- Fixed bug in the confidence map for PERFORMANCE mode.

#### Updated Spatial Mapping API (see details below):
- Added vector of sl::Chunk to allow access to smaller parts of a mesh ("chunks").
- Added Mesh::getVisibilityList() to get the list of chunks visible by the ZED.
- Added Mesh::getSurroundingList() to get the list of chunks located within a specific distance of the ZED.

#### Updated Positional Tracking API (see details below):
- Added getEulerAngles() to retrieve camera rotations in degrees or radians.
- Renamed getRotation() into getRotationMatrix().
- Updated Positional Tracking sample.


### Experimental feature

#### Spatial Mapping API update
- Mesh Chunks have been introduced. Spatial mapping now divides the world in fixed-size blocks called chunks. Instead of storing and accessing a single mesh with a million+ vertices, this allows to store and access multiple local submesh with a much lower number of vertices.
- Chunks within a certain distance of the camera can be accessed through Visibility and Surrounding lists. See sl::Mesh API documentation for more information.
- Additional optimization techniques based on chunks will be introduced in later updates.
- **Known Issue:** The normals can be duplicated with a slight orientation difference on the edge of the chunks on the mesh, this can cause lightning issues


<BR>
_____
<BR>
<BR>

  ##  2.0.1


  ### SDK
  - Bug fix on initial_world_transform units
  - Bug fix on Mesh::load() function

  ### Tools
  - Bug fix on ZEDfu installation for TX1

<BR>
_____
<BR>
<BR>
##  2.0.0

### Major New Features
#### Major SDK update
- The ZED SDK is now composed of different modules: Video, Depth, Positional Tracking and Spatial Mapping. Each module can be configured through dedicated parameters. Support functions have also been introduced and memory management has been simplified. The new API is much easier to use and integrate.
- ZED SDK 2.0 introduces breaking changes. To help you switch from 1.2 to 2.0, read the migration guide.

#### New Spatial Mapping module
- New Spatial Mapping module is introduced in 2.0.
- Spatial Mapping combines stereo depth sensing and motion tracking to capture a 3D map of the environment. Spatial mapping is useful for collision avoidance, motion planning and realistic blending of real and virtual worlds.

#### Major update of Positional Tracking module
- New Spatial Memory mode now enables the ZED to memorize its surroundings. It significantly improves positional tracking by correcting drift and ensures a consistent experience across uses. Spatial Memory is enabled by default and replaces the previous Area Learning mode.

#### New Unity plugin and samples
- Unity package now supports ZED SDK 2.0 and Unity 5.5.
- Added new VR, MR and Green Screen samples.


### SDK
#### Updated Video module
- Added control of camera White Balance.
- Reduced video capture latency on Linux: It is now possible to adjust the size of the buffer queue size for video capture using InitParams::reqBuffersCountLinux.
- Added real-time SVO mode: This mode reads SVO files and skips frames if necessary to simulate the behavior of the camera operating in live mode. Real-time mode is not available for SVO files recorded in RAW compression.
- Fixed bug with camera control functions that sometimes returned erroneous values.

#### Updated Depth module
- Improved depth quality for STANDARD mode and reduced aliasing.

#### Updated Positional Tracking module
- Introduced Spatial Memory mode that significantly reduces drift over time.
- Introduced new Pose, Translation, Orientation, Rotation and Transform classes that provide better and finer control over the API.
- Refactored tracking parameters into a TrackingParameters class. Use default parameters for optimal results.

#### Added Spatial Mapping module
- Introduced new sl::Mesh class to store polygonal meshes.
- enableSpatialMapping() starts Spatial Mapping.
- requestMeshAsync(), getMeshRequestStatusAsync(), retrieveMeshAsync(sl::Mesh) request and retrieve the mesh that is being created. These functions are asynchronous and designed for real-time mesh extraction.
- extractWholeMesh(sl::Mesh) extracts the current mesh synchronously. This function is designed to extract the whole mesh at the end of a mapping session.
- disableSpatialMapping() stops Spatial Mapping.

#### Added new Mat class
- The sl::Mat class simplifies CPU and GPU memory management.
- Memory can be owned or shared (reference) with another Mat.
- Reallocation is handled by the SDK. It is not necessary to pre-allocate memory.
- Transfer between CPU and GPU memory is simplified using Mat::updateCPUfromGPU() and Mat::updateGPUfromCPU().
- Direct conversion to OpenCV cv::Mat is now available using Mat::toCVMat().
- Direct data access is available using Mat::getPtr<T>()

#### General SDK changes
- Added default Camera constructor and open()/close() functions
- Unified namespace: all the class and function are now into the single unified namespace "sl".
- Changed filename input type from std::string to sl::String, based on const char* to allow Debug build under Windows.
- Renamed coordinate systems and added new COORDINATE_SYSTEM "LEFT_HANDED_Z_UP".
- Fixed bug with invalid depth values when out of range.
- Minor bug fixes and general improvements.


### Tools
- ZED Explorer: Updated GUI.
- ZED Depth Viewer: Updated GUI.
- ZEDfu: Updated tool to use the new Spatial Mapping API.
- ZED SVO Editor: Added compression options.
- ZED Calibration: Fixed a crash when adding or removing the ZED while the tool is running.
- Updated Diagnostic Tool.


### Plugins
####  Unity
- Refactored plugin to ensure compatibility with ZED SDK 2.0 and Unity 5.5.
- Improved overall stability and performance.
- Added Positional Tracking for VR sample.
- Added Green Screen VR Capture sample.
- Added Simple Mixed Reality sample.

#### ROS
- Updated wrapper to ensure compatibility with ZED SDK 2.0.

#### Matlab
- Updated interface to ensure compatibility with ZED SDK 2.0.


### Samples and Tutorials
#### Introduced new samples for each major API module:
- Camera Control: Shows how to open the ZED, control camera settings and display images.
- Depth Sensing: Shows how to get a depth map from the ZED and display a 3D point cloud using OpenGL.
- Motion Tracking: Shows how to use the ZED as a positional tracker and display the ZED in 3D space using OpenGL.
- Spatial Mapping: Shows how to map a scene using the Spatial Mapping API and display the projection of the mesh on the image in real-time using OpenGL.

#### Added new 'Interface' samples:
- OpenCV: Shows how to use the ZED with OpenCV.
- PCL: Shows how to use the ZED with PCL.

#### Added tutorials showing how to use the different modules of the SDK: Video, Depth, Tracking, Mapping.


### Dependencies
- Removed Eigen.
- Removed Boost from all samples.

### Platform Support
- New Jetson TX2 is now supported.


<BR>
_____
<BR>
<BR>

##  1.2.0

### New Features
#### Major update of Unity plugin
- Unity plugin now includes ZED images, depth and tracking, along with new simplified C# interface.

#### Major update of ROS wrapper
- ROS wrapper has been significantly improved. New features have been introduced and documentation has been updated.

#### New recording modes
- A new lossy compression mode is introduced in this release. It improves the compression ratio by 5 with minimal loss in image quality and is faster than previous compression modes.

#### Frame drop detection
- Dropped frames can now be detected by calling getFrameDroppedCount().

### SDK
#### Improved SVO recording:
- Added lossy compression mode.
- Improved lossless compression and limited frame drops during recording.
- Added recording_state structure for record() function output. The structure contains status, current and average compression time/ratio.

#### Bug fixes
- Fixed bug when destroying Camera object that could lead to a crash.
- Fixed bug (Jetson only) when calling retrieveMeasure_cpu() and writePointCloudAs() functions that stored a part of the image.
- Fixed bug when using setCameraSettingsValue() in SVO mode.

### Tools and Samples
- ZED Explorer: Added compression mode selection for recording.
- ZED Explorer: Fixed minor bugs with recording in command line.
- ZED Depth Viewer: Minor bug fixes.
- Code refactoring and documentation update of most samples on GitHub.

### App
- ZEDfu: Fixed bug when RAM memory limit is exceeded during mesh post-processing. Now *.ply files are saved even if post-processing doesn't run.

### Plugins
- Unity: New package which makes ZED stereo images, depth and tracking available in Unity.
- Unity: All major C++ ZED SDK functions are now accessible in Unity through C# interface.
- ROS wrapper: Improved performance and fixed minor bugs.
   - Converted wrapper to nodelet to improve performance. Thanks to GitHub contributor @furushchev.
   - Added support for using multiple ZED and GPU.
   - Added URDF model.
   - Updated topics names and added additional launch file examples.

### Compatibility notes:
- Jetson TK1: This is the last update for Jetson TK1. Next releases will support TX1 only.



<BR>
_____
<BR>
<BR>

##  1.1.1
### Platforms
- Added Ubuntu 16.04 compatibility .
- Added NVIDIA Pascal GPUs support and CUDA 8 compatibility on Windows and Linux.
- Added JetPack 2.3 compatibility on Jetson TX1.

### SDK
- grab() function now returns an appropriate error code ERRCODE when issues occur.
- grab() function now accepts either a structure as input or a list of variables.
- Minor bug fixes.

### Tools
- Fixed bug in ZED Calibration tool that could lead to app crash.

### App
- ZEDfu: Improved memory management and framerate.
- ZEDfu: Fixed bug when Live input is started and self-calibration fails.

<BR>
_____
<BR>
<BR>

##  1.1.0

### New Features
#### New recording pipeline
- SVO or AVI recording can now be started whith enableRecording() and stopped with stopRecording() without deleting the ZED camera object. You can also record videos whith depth and tracking enabled. Use the record() function after a grab() call to save incoming images in your video file.

#### New grayscale and raw image output
- Grayscale images and raw (unrectified) images can now be retrieved using retrieveImage functions.

### SDK
#### New positional tracking matrix output
- Besides the 4x4 Eigen matrix that contains the position and orientation of the camera, a new structure "MotionPoseData" that contains the rotation, translation and orientation (quaternion) of the camera can be retrieved. The quaternion can be used more easily in OpenGL and Unity applications.

#### New XYZ+Color output
- Added XYZBGRA, XYZARGB, XYZABGR support in retrieveMeasure*() function. This improves performance with ROS and other third-party libraries.

#### Bug fixes
- Fixed PNG error when SVO end is reached.
- Fixed some Valgrind errors when using ZED SDK.
- Fixed timestamp output on Windows (now in nanoseconds).

### Tools and Samples
#### New diagnostic tool
- ZED Diagnostic, located in the Tools folder, analyzes your hardware configuration and detects potential issues that may affect the performance of your system.

#### Minor fixes
- ZED Depth Viewer window can now be resized.
- ZED Depth Viewer and ZED Explorer now support ZED disconnections and reconnections.
- Added frame drop and effective framerate indicators in ZED Explorer for Live, Recording and SVO Playback mode.


### Compatibility notes
- Jetson TX1: Current version is for JetPack 2.1 in 32bits mode. New versions will only be released for JetPack 2.2 and above in 64bits mode.
- Windows: Current version supports CUDA 7.5. Next updates will support CUDA 8.0 only.
- Ubuntu: Current version supports Ubuntu 14.04 and CUDA 7.5. Next updates will support Ubuntu 16.04 with CUDA 8.0.


<BR>
_____
<BR>
<BR>

##  1.0.0

### Major New Features

#### Positional tracking API
- New tracking API is introduced in 1.0
- It calculates position and orientation of the camera for every frame using a new improved stereo SLAM technology. See Tracking samples to learn how to use the new API.
#### New ZEDfu large-scale 3D reconstruction application
- ZEDfu demonstrates how to combine stereo depth sensing and camera tracking to capture realistic environmental assets for VR, films and games.
- Now available as a standalone downloadable application for Windows platform. Also available in the �App� folder of the ZED SDK.

#### New high-speed Wide VGA mode
- Updated ZED firmware (1142) introduces Wide VGA @100 FPS, replacing previous VGA mode.
- WVGA resolution is increased to 672*376 per image with improved image quality and a much wider field of view.
- Firmware update is done automatically during installation of the ZED SDK (Win, Linux, Jetson).

#### SVO compression
- New SVO lossless compression now saves 66% in file size and processing time.

#### Unity support
- Positional tracking API is compatible with Unity.
- Unity plugin for ZED is available as a downloadable package on our Developer page.


### SDK
#### Added positional tracking API:
- New getPosition() function provides position and orientation of the camera for every frame. Use enableTracking() during initialization to use the Tracking API.
- enableAreaLearning parameter can be set to activate camera relocalization and loop closure detection during tracking.

#### Updated depth sensing mode with up to 2x faster computation:
- Updated MODE::PERFORMANCE to new faster than real-time stereo matching algorithm.
- Added MODE::MEDIUM with depth estimation quality and speed similar to the previous MODE:PERFORMANCE.

#### Added manual control for camera exposure time:
- Exposure time can now be configured manually by the user with setCameraSettingsValue() function.

#### Replaced parameters for Init() function with new "InitParams" structure:
- UNIT: Desired metric units can be specified in "InitParams" structure. All the provided data will be scaled according to the selected units: baseline, depth, XYZ, depthclamp, closestDephValue, tracking information... Therefore, "setBaselineRatio()" has been removed.
- COORDINATE_SYSTEM: Coordinate systems such as left or right-handed coordinates can now be selected in "InitParams" structure. The oriented data such as (XYZ) point cloud and tracking information will be expressed in the specified coordinate system.
- minimumDistance: the minimumDistance for depth computation can now be configured manually and set above 50cm. On certain configurations, this can dramatically improve frame-rate.

#### Invalid MEASURE values are now separated into three distinct values:
- OCCLUSION_VALUE: NaN (not a number), for occlusions.
- TOO_FAR: Inf (infinity), for values above DepthClampValue.
- TOO_CLOSE: -Inf, for values below ClosestDepthValue.
- isValidMeasure(): New macro function that returns false if the given value is one of the above, else returns true.

#### Added function to calculate ZED camera field of view:
- Left and right eye field of view can now be retrieved using getParameters() : LeftCam/RightCam.hFOV/dFOV/vFOV. See StereoParameters structure for more details.

#### SVO files now record the camera framerate as well as timestamp during recording:
- Use getCameraTimestamp() function to extract the timestamp from the current SVO image. This can be used to detect frame drops during recording.


### Tools and Samples
- New unified GUI for the tools
- Added two samples using the Positional Tracking API: cpu/Simple Tracking and cpu/Tracking Viewer.
- "Grabbing thread" sample has been renamed to "Optimized Grab".
- Updated all samples with ZED SDK 1.0 compatibility


### API Changes
- Camera::init(...): Replaced arguments with InitParams structure. See SDK Changes.
- SENSING_MODE: RAW and FULL modes have been renamed to STANDARD and FILL. This better reflects the use of the different depth sensing modes. Old names have been removed.


### Dependencies
- Updated OpenCV, from 2.4.9 to 3.1 on Window and Linux.
- Added Eigen 3.




<BR>
_____
<BR>
<BR>

 ##  0.9.4 Beta
  ### Platforms
  - Linux: Added compatibility for Linux Kernel 4.X. Previous ZED SDK version were incompatible with new Linux kernel, producing a "ZED_NOT_AVAILABLE" message and segfault.

  ### SDK
  - Fixed a bug in color correction.

  ### Tools
  - Introducing a new tool called "ZED Calibration" that can be used to easily recalibrate your ZED. Must be used in Live mode. Please follow the tutorial inside the tool to learn how to use this new tool.
  - Added automated check of ZED SDK version in ZED Explorer. You can also use ZED Explorer with -v command line option to check if your SDK is up to date.
  - Fixed bug in ZED Depth Viewer that could lead to degraded performance on specific CPU/GPU configurations on Windows.

<BR>
_____
<BR>
<BR>

  ##  0.9.3 Beta

  ### Platforms
  - Compatible with CUDA 7.5 for Windows and Linux.

  ### SDK
  - Improved grab() time.
  - Reduce Latency when using cpu functions for Jetson.
  - Changed Selfcalibration to a non-blocking function. Self-Calibration is done in background and the result can be checked with the status function.
  - Improved Init() time

  ### Tools
  - Merged ZED Explorer and ZED Settings App into one single tool.
  - Added command line options for ZED Explorer for check compatibility and recording

  ### Known Issues
  - On Ubuntu 14.04, the newer kernel 4.X is NOT supported.

<BR>
_____
<BR>
<BR>

  ##  0.9.2b Beta
  ### SDK

  Bug fixes. Patches version.
  <BR><BR><BR>

  ##  0.9.2 Beta

  ### Platforms
  - Added Window 10 compatibility.

  ### SDK
  - Improved Left/Right image quality with retrieveImage*().
  - Improved grab time on embedded platform (Jetson TK1).
  - Added possibility to disable self-calibration during init().
  - Added function that returns results of self-calibration.
  - More calibration parameters available.


  ### Tools
  - Bug fixes in Depth Viewer tool.
  - Added more calibration parameters in ZED Settings App


<BR>
_____
<BR>
<BR>

  ##  0.9.1 Beta


  ### SDK
  - Bug fixes for Auto-calibration that made samples crash on Windows 8
  - Bug fixes for ply write function that made incoherent color.
  - Add more support for slMat2cvMat function.

  ### Tools
  - Improved global framerate of Depth Viewer tool.

<BR>
_____
<BR>
<BR>

  ##  0.9.0 Beta


  ### SDK
  - Added triangulation function to extract XYZ or XYZRGBA point cloud.
  - Added Save functions to save point cloud in multiple formats and depth image in multiple format.
  - Added Camera and Current Timestamp extraction function to help synchronization with other devices.
  - Added FPS extraction function.
  - Bug fixes when using svo files.
  - Improved AutoCalibration.


  ### Samples
  - Complete re-factory of samples.
  - Added Cuda sample for background substraction and image disparity for right image.
  - Added ROS Sample to demonstrate how to interact with ROS.
  - Added Recording sample to demonstrate how to use recording functions.
  - Added SVO Playback sample to demonstrate how to simply use the svo file.
  - Simplify existing samples.

  ### Tools
  - Added ZED Depth Viewer sample to have a plug and play tool to demonstrate all the functions of the ZED SDK.
  - Added Cmd Line mode for ZED Explorer to record svo without Graphics.

<BR>
_____
<BR>
<BR>

  ##  0.8.2 Beta


  ### SDK
  - Added flip mode (to work with ZED Camera vertically flipped)
  - Added multiple input possibility under Linux system.


  ### Samples
  - Added Multiple input sample.

<BR>
_____
<BR>
<BR>

  ##  0.8.1 Beta


  ### SDK
  - Major improvement of Disparity estimation in untextured areas and repetitive patterns
  - Reduced grab() time by 10%
  - Reduced retrieveImage() time to <0.5ms when grab is done with disparity enabled
  - Added new function to adjust ZED camera parameters (set / getCameraSettingsValue), including exposure, white balance, brightness, contrast and hue
  - Added option to select a different framerate other than the default ones available in the ZED Camera constructor. For example, it is now possible to select VGA mode @ 30 fps
  - Added getSVOPosition() to retrieve current SVO position
  - Bug fix in "Live mode" on Windows x64
  - Added compatibility with NVIDIA GeForce 9 series of graphics cards


  ### Tools
  - Minor bug fix in ZED Explorer on Linux

<BR>
_____
<BR>
<BR>

  ##  0.8.0 Beta

  ### Platforms
  - Added Linux (Ubuntu 14.04 LTS only) compatibility.


  ### Tools
  - Added SVOEditor: command line tool to cut and concatenate SVO files. See -help for more details
  - Bug fix in ZED Settings App with "-sn" options
  - Minor bug fix in ZED Explorer




  ### SDK
  - Improved Disparity estimation
  - Bug fix in Disparity/Depth normalization
  - Bug fix in NONE grab mode (MODE::NONE)
  - Merged MODE::MEDIUM et MODE::QUALITY into a single QUALITY MODE
  - Add specific functions for SVO (set position and get number of frames)
  - Changed name of "DispReliability" to "ConfidenceThreshold"
  - Conformed set/get functions




  ### Known Issues
  - Tools or Samples may not launch when run twice on Win8. Reconnecting the ZED will solve this issue.


<BR>
_____
<BR>
<BR>

  ##  0.7.1a Alpha


  ### Notes
  - Initial release.

  ### Known Issues
  - Specific samples can crash at launch on Win 8. Relaunching the sample solves the problem.

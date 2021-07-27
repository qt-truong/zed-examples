
# ZED Video Capture

The ZED API provides low-level access to the camera hardware and video features, facilitating camera control and high-quality video recording and streaming.

**Overview**

* [How It Works](#how-it-works)
* [Camera Configuration](#camera-configuration)
* [Getting Images](#getting-images)
* [Code Examples](#code-examples)
* [Integrations](#integrations)
* [Docs](#documentation)

## How It Works

The left and right video frames of the ZED Camera are synchronized and streamed as a single uncompressed video frame, and you can choose your preferred output format among the following :

* Left or Right rectified 
* Side-by-side view
* Left or Right unrectified
* Left or Right grayscale

The ZED camera features an onboard **ISP (Image Signal Processor)** that performs various image processing algorithms on raw images captured by the dual image sensors.

## Camera Configuration

To configure the camera, create a Camera object and specify your `InitParameters`. Initial parameters let you adjust camera resolution, FPS, depth sensing parameters and more. These parameters can only be set before opening the camera and cannot be changed while the camera is in use. Note that `InitParameters` contains a default configuration.

To read more about how to configure your camera using the API, read our [dedicated section](http://localhost:1313/docs/video/using-video/#camera-configuration).

## Getting Images

### Live Image Capture

To capture images from the ZED, specify your `RuntimeParameters` and call `grab()` to grab a new frame and `retrieveImage()` to retrieve the grabbed frame. `retrieveImage()` lets you select between different views such as left, right, unrectified and grayscale images.

**C++**
```cpp
sl::Mat image;
if (zed.grab() == SUCCESS) {
  // A new image is available if grab() returns SUCCESS
  zed.retrieveImage(image,VIEW_LEFT); // Retrieve the left image
}
```

**Python**
```python
image = sl.Mat()
runtime_parameters = sl.RuntimeParameters()
if zed.grab(runtime_parameters) == sl.ERROR_CODE.SUCCESS:
  # A new image is available if grab() returns SUCCESS
  zed.retrieve_image(image, sl.VIEW.VIEW_LEFT) # Retrieve the left image
```

**C#**
```csharp
sl.Mat image = new sl.Mat();
RuntimeParameters runtimeParameters = new RuntimeParameters();
if (zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS) {
  // A new image is available if grab() returns SUCCESS
  zed.RetrieveImage(image,sl.VIEW.LEFT); // Retrieve the left image
}
```


### Video Recording
The ZED SDK uses Stereolabs SVO format to store videos along with additional metadata such as timestamp and sensor data.

To record SVO files, you need to enable the Recording module with `enableRecording()`. Specify an output file name (eg: output.svo) and `SVO_COMPRESSION_MODE`, then save each  grabbed frame using `record()`. SVO lets you record video and associated metadata (timestamp, IMU data and more if available).

**C++**
```cpp
// Create a ZED camera object
Camera zed;

// Enable recording with the filename specified in argument
String output_path(argv[1]);
err = zed.enableRecording(output_path, SVO_COMPRESSION_MODE::H264);

while (!exit_app) {
    if (zed.grab() == SUCCESS) {
        // Each new frame is added to the SVO file
        zed.record();
    }
}
// Disable recording
zed.disableRecording();
```

**Python**
```python
# Create a ZED camera object
zed = sl.Camera()

# Enable recording with the filename specified in argument
output_path = sys.argv[0]
err = zed.enable_recording(output_path, sl.SVO_COMPRESSION_MODE.H264)

while !exit_app :
    if zed.grab() == SUCCESS :
        # Each new frame is added to the SVO file
        zed.record()

# Disable recording
zed.disable_recording()
```

**C#**
```csharp
// Create a ZED camera object
sl.Camera zed = new sl.Camera(0);
sl.RuntimeParameters runtimeParameters = new sl.RuntimeParameters();
// Enable recording with the filename specified in argument
string output_path = args[0];
err = zed.EnableRecording(output_path, sl.SVO_COMPRESSION_MODE.H264_BASED);

while (!exit_app) {
    // Each new frame is added to the SVO file
    zed.Grab(ref runtimeParameters);
}
// Disable recording
zed.DisableRecording();
```

Once your SVO is properly recorded, you can [play it back](https://www.stereolabs.com/docs/video/using-video/#video-playback) or load it as an input to your ZED programs, enabling the ZED API to behave as if a ZED Camera was connected and live feed was available. Every module of the ZED API will be available: depth, tracking, spatial mapping and more.

## Code Examples
For code examples, check out our Video Capture [Tutorial](../09-Tutorials/tutorial%202%20-%20image%20capture), [Camera Control](../10-Samples/camera%20control), [SVO Recording](../10-Samples/svo%20recording/recording) and [SVO Playback](../10-Samples/svo%20recording/playback) samples in this repository.


## Integrations
Check the [Integrations](../11-Integrations#overview) list to use the ZED with your favorite suite of tools and libraries.

## Documentation
For more information, read the [Documentation](https://www.stereolabs.com/docs/video/) or these specific sections:

* [Camera Controls](https://www.stereolabs.com/docs/video/camera-controls/)
* [Camera Calibration](https://www.stereolabs.com/docs/video/camera-calibration/)
* [Video Recording](https://www.stereolabs.com/docs/video/recording/)
* [Multi Camera](https://www.stereolabs.com/docs/video/multi-camera/)
* [Using the API](https://www.stereolabs.com/docs/video/using-video/)

You can also check out our [API Reference](https://www.stereolabs.com/docs/api/) for more details about the available functions provided by the SDK.
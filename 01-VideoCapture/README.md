
# ZED Video Capture

The ZED API provides low-level access to the camera hardware and video features, facilitating camera control and high-quality video recording and streaming.

**Overview**

* [Camera Configuration](https://github.com/qt-truong/zed-examples/tree/master/01-VideoCapture#camera-configuration)
* [Image Capture](https://github.com/qt-truong/zed-examples/tree/master/01-VideoCapture#image-capture)
* [Video Recording](https://github.com/qt-truong/zed-examples/tree/master/01-VideoCapture#video-recording)
* [Video Playback](https://github.com/qt-truong/zed-examples/tree/master/01-VideoCapture#video-playback)
* [Code Examples](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/camera%20control)

## Camera Configuration
To configure the camera, create a Camera object and specify your `InitParameters`. Initial parameters let you adjust camera resolution, FPS, depth sensing parameters and more. These parameters can only be set before opening the camera and cannot be changed while the camera is in use. Note that `InitParameters` contains a default configuration.

**C++**
```cpp
// Create a ZED camera object
Camera zed;

// Set configuration parameters
InitParameters init_params;
init_params.camera_resolution = RESOLUTION_HD1080;
init_params.camera_fps = 30;

// Open the camera
err = zed.open(init_params);
if (err != SUCCESS)
    exit(-1);
```

**Python**
```python
# Create a ZED camera object
zed = sl.Camera()

# Set configuration parameters
init_params = sl.InitParameters()
init_params.camera_resolution = sl.RESOLUTION.RESOLUTION_HD1080
init_params.camera_fps = 30

# Open the camera
err = zed.open(init_params)
if err != sl.ERROR_CODE.SUCCESS:
    exit(-1)
```

**C#**
```csharp
// Create a ZED camera object
sl.Camera zed = new sl.Camera(0);

// Set configuration parameters
sl.InitParameters init_parameters = new sl.InitParameters();
init_parameters.resolution = sl.RESOLUTION.HD1080;
init_parameters.cameraFPS = 30;

// Open the camera
sl.ERROR_CODE err = zed.Open(ref init_params);
if (err != sl.ERROR_CODE.SUCCESS)
    Environment.Exit(-1);
```

Camera settings such as exposure, white balance and others can be manually set at runtime using `setCameraSettings()`.

**C++**
```cpp
// Set exposure in manual mode at 50% of camera framerate
zed.setCameraSettings(CAMERA_SETTINGS_EXPOSURE, 50, false);
// Set white balance to 4600K
zed.setCameraSettings(CAMERA_SETTINGS_WHITE_BALANCE, 4600, false);
// Reset to auto exposure
zed.setCameraSettings(CAMERA_SETTINGS_EXPOSURE, -1, true);
```

**Python**
```python
# Set exposure to 50% of camera framerate
zed.set_camera_settings(sl.CAMERA_SETTINGS.CAMERA_SETTINGS_EXPOSURE, 50, False)
# Set white balance to 4600K
zed.set_camera_settings(sl.CAMERA_SETTINGS.CAMERA_SETTINGS_WHITE_BALANCE, 4600, False)
# Reset to auto exposure
zed.set_camera_settings(sl.CAMERA_SETTINGS.CAMERA_SETTINGS_EXPOSURE, -1, True)
```

**C#**
```csharp
// Set exposure in manual mode at 50% of camera framerate
zed.SetCameraSettings(sl.VIDEO_SETTINGS.EXPOSURE, 50);
// Set white balance to 4600K
zed.SetCameraSettings(sl.VIDEO_SETTINGS.WHITEBALANCE, 4600);
//Reset to auto exposure
zed.SetCameraSettings(sl.VIDEO_SETTINGS.EXPOSURE, -1);
```

To see the list of available video modes and settings, read [Camera Controls doc](https://www.stereolabs.com/docs/video/camera-controls/).


## Image Capture
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


## Video Recording
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

## Video Playback
To play SVO files, simply add the file path as argument in `setFromSVOFile()`.  When loading SVO files, the ZED API will behave as if a ZED was connected and live feed was available. Every module of the ZED API will be available: depth, tracking, spatial mapping and more. When an SVO file is read entirely, `END_OF_SVOFILE_REACHED` error code is returned.

**C++**
```cpp
// Create a ZED camera object
Camera zed;

// Set SVO path for playback
String input_path(argv[1]);
InitParameters init_parameters;
init_parameters.input.setFromSVOFile(input_path);

// Open the ZED
err = zed.open(init_parameters);

sl::Mat svo_image;
while (!exit_app) {
    if (zed.grab(ref runtimeParameters) == SUCCESS) {
        // Read side by side frames stored in the SVO
        zed.retrieveImage(svo_image, VIEW::SIDE_BY_SIDE);
        // Get frame count      
        int svo_position = zed.getSVOPosition();
    }
    else if (zed.grab() == END_OF_SVOFILE_REACHED) {
        std::cout << "SVO end has been reached. Looping back to first frame" << std::endl;
        zed.setSVOPosition(0);
    }
}
```

**Python**
```python
# Create a ZED camera object
zed = sl.Camera()

# Set SVO path for playback
input_path = sys.argv[1]
init_parameters = sl.InitParameters()
init_parameters.set_from_svo_file(input_path)

# Open the ZED
zed = sl.Camera()
err = zed.open(init_parameters)

svo_image = sl.Mat()
while !exit_app:
  if zed.grab() == sl.ERROR_CODE.SUCCESS:
    # Read side by side frames stored in the SVO
    zed.retrieve_image(svo_image, sl.VIEW.SIDE_BY_SIDE)
    # Get frame count
    svo_position = zed.get_svo_position();
  elif zed.grab() == sl.ERROR_CODE.END_OF_SVOFILE_REACHED:
    print("SVO end has been reached. Looping back to first frame")
    zed.set_svo_position(0)
```

**C#**
```csharp
// Create a ZED camera object
sl.Camera zed = new sl.Camera(0);

sl.RuntimeParameters runtimeParameters = new sl.RuntimeParameters();

// Set SVO path for playback
string input_path = argv[0];
sl.InitParameters init_parameters = new sl.InitParameters();
init_parameters.inputType = sl.INPUT_TYPE.SVO;
init_parameters.pathSVO = input_path;

// Open the ZED
err = zed.Open(ref init_parameters);

sl.Mat svo_image = new sl.Mat();
while (!exit_app) {
    if (zed.Grab(ref runtimeParameters) == sl.ERROR_CODE.SUCCESS) {
        // Read side by side frames stored in the SVO
        zed.RetrieveImage(svoImage, sl.VIEW.SIDE_BY_SIDE, sl.MEM.CPU);
        // Get frame count      
        int svo_position = zed.GetSVOPosition();
    }
    if (zed.GetSVOPosition() >= zed.GetSVONumberOfFrames() - 1) {
        Console.WriteLine("SVO end has been reached. Looping back to first frame");
        zed.SetSVOPosition(0);
    }
}
```

## Code Examples
Get started with stereo video capture, recording and streaming using the following code samples on GitHub:

- [Camera Control](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/camera%20control)
- [SVO Recording](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/svo%20recording/recording), [SVO Playback](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/svo%20recording/playback) and [SVO Export](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/svo%20recording/export)

For more information, visit our [documentation](https://www.stereolabs.com/docs/video/) and our [API Reference](https://www.stereolabs.com/docs/api/).
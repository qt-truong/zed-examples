# ZED Local Streaming

Using the ZED SDK, you can stream the side by side video of a ZED camera over a local IP network (Ethernet or Wifi).


**Overview**

* [How It Works](#how-it-works)
* [Using the Streaming API](#using-the-streaming-api)
* [Code Example](#code-example)
* [Integrations](#integrations)
* [Docs](#documentation)

## How It Works

Devices with proper permissions can access the live feed from anywhere using the ZED SDK on the receiving end. When taking a stream as input, the ZED API will behave as if a camera is directly connected to the PC. Every module of the ZED API will be available: depth, tracking, spatial mapping and more.

To stream the video of a ZED camera, refer to the [Camera Streaming](../10-Samples/camera%20streaming) sample.

## Using the Streaming API

### Streaming Configuration

To stream the video content of a ZED camera, you need to enable the streaming module with `enableStreaming()`. The standard `grab()` function will grab a frame and send it over the local network.

Use `StreamingParameters` to specify settings like bitrate, port, etc. Then pass those parameters when you call `enableStreaming()`.

**C++**
```cpp
// Set the streaming parameters
sl::StreamingParameters stream_params;
stream_params.codec = sl::STREAMING_CODEC::H264; // Can be H264 or H265
stream_params.bitrate = 8000;
stream_params.port = 30000; // Port used for sending the stream
// Enable streaming with the streaming parameters
err = zed.enableStreaming(stream_params);

while (!exit_app) {
    zed.grab();
}
// Disable streaming
zed.disableStreaming();
```

**Python**
```python
# Set the streaming parameters
stream = sl.StreamingParameters()
stream.codec = sl.STREAMING_CODEC.H264 # Can be H264 or H265
stream.bitrate = 8000
stream.port = 30000 # Port used for sending the stream
# Enable streaming with the streaming parameters
err = zed.enable_streaming(stream)

while !exit_app :
    zed.grab()

# Disable streaming
zed.disable_streaming()
```

**C#**
```csharp
// Enable streaming with the streaming parameters
err =  zed.EnableStreaming(STREAMING_CODEC.H264_BASED, 8000, 30000);

RuntimeParameters runtimeParameters = new RuntimeParameters();
while (!exit_app) {
    zed.Grab(ref runtimeParameters);
}
// Disable streaming
zed.DisableStreaming();
```

### Using a Stream as SDK Input

Video content streamed from a ZED camera is accessible remotely and can be used as standard input for the ZED API. Therefore, every ZED API module will work as if the camera was directly connected to the device. To use a remote stream as input, specify the IP address and the port of the sender in `initParameters`. Then call `open()` to open the camera from the stream and `grab()` to grab a new frame and do the processing you want.


**C++**
```cpp
// Set the input from stream
InitParameters initParameters;
initParameters.input.setFromStream("127.0.0.1", 30000); // Specify the IP and port of the sender
// Open the camera
ERROR_CODE err = zed.open(initParameters);
if (err != ERROR_CODE::SUCCESS)
    exit(-1);

while (!exit_app) {
    if (zed.grab() == ERROR_CODE::SUCCESS) {
        // Any processing
    }
}
// Close the camera
zed.close();
```

**Python**
```python
# Set the input from stream
init = sl.InitParameters()
init.set_from_stream("127.0.0.1", 30000) # Specify the IP and port of the sender
# Open the camera
err = zed.open(init)
if err != sl.ERROR_CODE.SUCCESS:
	exit(1)

while !exit_app:
    if zed.grab() == sl.ERROR_CODE.SUCCESS):
        # Any processing

# Close the camera
zed.close()
```

**C#**
```csharp
// Set the input from stream
InitParameters initParameters = new InitParameters();
initParameters.inputType = INPUT_TYPE.STREAM; 
initParameters.ipStream = "127.0.0.1"; // Specify the IP of the sender
initParameters.portStream = "30000"; // Specify the port of the sender

RuntimeParameters runtimeParameters = new RuntimeParameters();
// Open the camera
ERROR_CODE err = zed.Open(ref initParameters);
if (err != ERROR_CODE.SUCCESS)
    Environment.Exit(-1);

while (!exit_app) {
    if (zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS) {
        // Any processing
    }
}
// Close the camera
zed.Close();
```

## Code Example

See the [Camera Streaming](../10-Samples/camera%20streaming) samples for more information on streaming a video and using a stream as input.

## Integrations

Check the [Integrations](../11-Integrations#overview) list to use positional tracking with your favorite suite of tools and libraries. 

## Documentation

For more information, read the [Documentation](https://www.stereolabs.com/docs/video/streaming/) or these specific sections:

* [Hardware Requirements](https://www.stereolabs.com/docs/video/streaming/#hardware-requirements)
* [Streaming Modes](https://www.stereolabs.com/docs/video/streaming/#streaming-modes)
* [Streaming Protocol](https://www.stereolabs.com/docs/video/streaming/#streaming-protocol)
* [Using the API](https://www.stereolabs.com/docs/video/streaming/#using-the-streaming-api)
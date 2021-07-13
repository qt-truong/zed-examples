# ZED Sensors API

The Sensors API lets you access sensors available on ZED depth cameras and perform a wide variety of sensor-related tasks explained below.

**Overview**

* [Getting Sensors Data](https://github.com/qt-truong/zed-examples/tree/master/03-Sensors#getting-sensors-data)
* [Retrieve New Sensor Data](https://github.com/qt-truong/zed-examples/tree/master/03-Sensors#retrieve-new-sensor-data)
* [Accessing Raw Sensor Data](https://github.com/qt-truong/zed-examples/tree/master/03-Sensors#accessing-raw-sensor-data)
* [Identifying Sensors Capabilities](https://github.com/qt-truong/zed-examples/tree/master/03-Sensors#identifying-sensors-capabilities)
* [Code Example](https://github.com/qt-truong/zed-examples/tree/master/03-Sensors#code-example)


## Getting Sensors Data

The data from the different sensors is accessible with the `SensorsData` class. The class contains raw and calibrated values of each sensor.

To retrieve sensor values synchronized with the image frames, you will need to:

- Open the camera and grab the current image.
- Get sensors data corresponding to this image using `TIME_REFERENCE::IMAGE`.
- Retrieve data from the different sensors.


**C++**
```cpp
// Create and open the camera
Camera zed;
zed.open();
SensorsData sensors_data;

// Grab new frames and retrieve sensors data
while(zed.grab() == SUCCESS){
    zed.getSensorsData(sensors_data, TIME_REFERENCE::IMAGE); // Get frame synchronized sensor data

    // Extract multi-sensor data
    imu_data = sensors_data.imu;
    barometer_data = sensors_data.barometer;
    magnetometer_data = sensors_data.magnetometer;

    // Retrieve linear acceleration and angular velocity
    float3 linear_acceleration = imu_data.linear_acceleration;
    float3 angular_velocity = imu_data.angular_velocity;

    // Retrieve pressure and relative altitude
    float pressure = barometer_data.pressure;
    float relative_altitude = barometer_data.relative_altitude;

    // Retrieve magnetic field
    float3 magnetic_field = magnetometer_data.magnetic_field_uncalibrated;
}
```

**Python**
```python
# Create and open the camera
zed = sl.Camera()
zed.open()
sensors_data = sl.SensorsData()

# Grab new frames and retrieve sensors data
while zed.grab() == sl.ERROR_CODE.SUCESS :
  zed.get_sensors_data(sensors_data, sl.TIME_REFERENCE.IMAGE) #  Get frame synchronized sensor data

  # Extract multi-sensor data
  imu_data = sensors_data.get_imu_data()
  barometer_data = sensors_data.get_barometer_data()
  magnetometer_data = sensors_data.get_magnetometer_data()

  # Retrieve linear acceleration and angular velocity
  linear_acceleration = imu_data.get_linear_acceleration()
  angular_velocity = imu_data.get_angular_velocity()

  # Retrieve pressure and relative altitude
  pressure = barometer_data.pressure
  relative_altitude = barometer_data.relative_altitude

  # Retrieve magnetic field
  magnetic_field = magnetometer_data.get_magnetic_field_uncalibrated()
```

**C#**
```csharp
// Create and open the camera
Camera zed = new Camera();
InitParameters init_parameters = new InitParameters();
zed.Open(ref init_parameters);
SensorsData sensors_data = new SensorsData();

RuntimeParameters runtimeParameters = new RuntimeParameters();
// Grab new frames and retrieve sensors data
while(zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS){
    zed.GetSensorsData(ref sensors_data, TIME_REFERENCE.IMAGE); // Get frame synchronized sensor data

    // Extract multi-sensor data
    imu_data = sensors_data.imu;
    barometer_data = sensors_data.barometer;
    magnetometer_data = sensors_data.magnetometer;

    // Retrieve linear acceleration and angular velocity
    float3 linear_acceleration = imu_data.linearAcceleration;
    float3 angular_velocity = imu_data.angularVelocity;

    // Retrieve pressure and relative altitude
    float pressure = barometer_data.pressure;
    float relative_altitude = barometer_data.relativeAltitude;

    // Retrieve magnetic field
    float3 magnetic_field = magnetometer_data.magneticFieldUncalibrated;
}
```


### Time Reference
The function `getSensorsData` can be called with a `TIME_REFERENCE`. For example you can use `TIME_REFERENCE::CURRENT` to get the sensor data corresponding the timestamp of the function call, or `TIME_REFERENCE::IMAGE` to get the data synchronized with the current camera image.


For more information on sensor time reference, read our documentation on [Sensors Time Synchronization](https://www.stereolabs.com/docs/sensors/time-synchronization/).


## Retrieve New Sensor Data
To retrieve updated sensor data, `getSensorsData` must be called at a frequency superior or equal to the sensor data rate.
Since sensors have different frequencies and their data is stored in the same `sensors_data` structure, some sensors data might not be updated between two calls to `getSensorsData`.

To know if a given sensor data has been updated, we compare their timestamps which are used as unique identifiers. If they are the same, then a sensor data has not been updated.

**C++**
```cpp
Timestamp last_imu_ts = 0;
while(zed.grab() == SUCCESS){
    zed.getSensorsData(sensors_data, TIME_REFERENCE::IMAGE);

    // Check if a new IMU sample is available
    if (sensors_data.imu.timestamp > last_imu_ts) {
        cout << "Linear Acceleration: " << sensors_data.imu.linear_acceleration;
        cout << "Angular Velocity: " << sensors_data.imu.angular_velocity;
        last_imu_ts = sensors_data.imu.timestamp;
    }
}
```

**Python**
```python
last_imu_ts = sl.Timestamp()
while(zed.grab() == sl.ERROR_CODE.SUCCESS):
    zed.get_sensors_data(sensors_data, sl.TIME_REFERENCE.IMAGE)

    # Check if a new IMU sample is available
    if sensors_data.get_imu_data().timestamp.get_seconds() > last_imu_ts.get_seconds():
        print("Linear Acceleration: {}".format(sensors_data.get_imu_data().get_linear_acceleration()))
        print("Angular Velocity : {}".format(sensors_data.get_imu_data().get_angular_velocity()))
        last_imu_ts = sensors_data.get_imu_data().timestamp
```

**C#**
```csharp
ulong last_imu_ts = 0;
RuntimeParameters runtimeParameters = new RuntimeParameters();

while(zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS){
    zed.GetSensorsData(sensors_data, TIME_REFERENCE.IMAGE);

    // Check if a new IMU sample is available
    if (sensors_data.imu.timestamp > last_imu_ts) {
        Console.WriteLine("Linear Acceleration: " + sensors_data.imu.linearAcceleration);
        Console.WriteLine("Angular Velocity: " + sensors_data.imu.angularVelocity);
        last_imu_ts = sensors_data.imu.timestamp;
    }
}
```

## Accessing Raw Sensor Data
The Sensors API lets you read raw data from the depth camera built-in motion and position sensors. To retrieve raw data, use the `uncalibrated` values present in `sensors_data` structure.

## Identifying Sensors Capabilities

Sensors factory parameters can be access through the API. They cannot be changed as they are fixed in the microcontroller firmware of the cameras.
You can access the following parameters:

- Sensor Type
- Sampling Rate
- Range
- Resolution
- Noise Density
- Random Walk (if applicable)
- Sensor Units

**C++**
```cpp
// Display camera information (model, serial number, firmware version)
auto info = zed.getCameraInformation();
cout << "Camera Model: " << info.camera_model;
cout << "Serial Number: " << info.serial_number;
cout << "Camera Firmware: " << info.camera_configuration.firmware_version;
cout << "Sensors Firmware: " << info.sensors_configuration.firmware_version;

// Display accelerometer sensor configuration
SensorParameters& sensor_parameters = info.sensors_configuration.accelerometer_parameters;
cout << "Sensor Type: " << sensor_parameters.type;
cout << "Sampling Rate: " << sensor_parameters.sampling_rate;
cout << "Range: "       << sensor_parameters.range;
cout << "Resolution: "  << sensor_parameters.resolution;
if (isfinite(sensor_parameters.noise_density)) cout << "Noise Density: " << sensor_parameters.noise_density;
if (isfinite(sensor_parameters.random_walk)) cout << "Random Walk: " << sensor_parameters.random_walk;
```

**Python**
```python
# Display camera information (model, serial number, firmware version)
info = zed.get_camera_information()
print("Camera model: {}".format(info.camera_model))
print("Serial Number: {}".format(info.serial_number))
print("Camera Firmware: {}".format(info.camera_configuration.firmware_version))
print("Sensors Firmware: {}".format(info.sensors_configuration.firmware_version))

# Display accelerometer sensor configuration
sensor_parameters = info.sensors_configuration.accelerometer_parameters
print("Sensor Type: {}".format(sensor_parameters.sensor_type))
print("Sampling Rate: {}".format(sensor_parameters.sampling_rate))
print("Range: {}".format(sensor_parameters.sensor_range))
print("Resolution: {}".format(sensor_parameters.resolution))

import math
if math.isfinite(sensor_parameters.noise_density):
    print("Noise Density: {}".format(sensor_parameters.noise_density))
if math.isfinite(sensor_parameters.random_walk):
    print("Random Walk: {}".format(sensor_parameters.random_walk))
```

**C#**
```csharp
// Display camera information (model, serial number, firmware version)
Console.WriteLine("Camera Model: " + zed.GetCameraModel());
Console.WriteLine("Serial Number: " + zed.GetZEDSerialNumber());
Console.WriteLine("Camera Firmware: " + zed.GetCameraFirmwareVersion());
Console.WriteLine("Sensors Firmware: " + zed.GetSensorsFirmwareVersion());

// Display accelerometer sensor configuration
SensorParameters sensorParams = new SensorParameters();
sensorParams = zed.GetSensorsConfiguration().accelerometer_parameters;
Console.WriteLine("Sensor type: " + sensorParams.type);
Console.WriteLine("Sampling Rate: " + sensorParams.sampling_rate);
Console.WriteLine("Range:" + sensorParams.range.x + " / " + sensorParams.range.y);
Console.WriteLine("Resolution: " + sensorParams.resolution);
if (!float.IsInfinity(sensorParams.noise_density)) Console.WriteLine("Noise Density: " + sensorParams.noise_density);
if (!float.IsInfinity(sensorParams.random_walk)) Console.WriteLine("Random Walk: " + sensorParams.random_walk);
```

## Code Example
For code example, check out the [Getting Sensor Data](https://github.com/qt-truong/zed-examples/tree/master/09-Tutorials/tutorial%207%20-%20sensor%20data) tutorial.

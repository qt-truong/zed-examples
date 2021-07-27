# ZED Sensors

The Sensors API lets you access sensors available on ZED depth cameras and perform a wide variety of sensor-related tasks explained below.

## Overview

* [How It Works](#how-it-works)
* [Getting Sensors Data](#getting-sensors-data)
* [Code Examples](#code-examples)
* [Integrations](#integrations)
* [Documentation](#documentation)

## How It Works

The ZED family of depth cameras is a multi-sensor platform. The cameras have built-in sensors to add position and motion-assisted capabilities to your app, from accelerometer and gyroscope sensors to temperature, barometer, magnetometer and more.

The sensors can be used to detect camera movements, compute the camera orientation according to the north magnetic pole, detect relative altitude variations, analyze external weather conditions, and much more.

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

## Code Examples
As an example, check out the [Getting Sensor Data](../09-Tutorials/tutorial%207%20-%20sensor%20data) tutorial.


## Integrations
Check the [Integrations](../11-Integrations#overview) list to use the ZED sensors capabilities with your favorite suite of tools and libraries.


## Documentation
For more information, read the [Documentation](https://www.stereolabs.com/docs/sensors/) or these specific sections:

* [IMU](https://www.stereolabs.com/docs/sensors/imu/)
* [Magnetometer](https://www.stereolabs.com/docs/sensors/magnetometer/)
* [Barometer](https://www.stereolabs.com/docs/sensors/barometer/)
* [Temperature Sensors](https://www.stereolabs.com/docs/sensors/temperature/)
* [Time Synchronization](https://www.stereolabs.com/docs/sensors/time-synchronization/)
* [Using the API](https://www.stereolabs.com/docs/sensors/using-sensors/)

You can also check out our [API Reference](https://www.stereolabs.com/docs/api/) for more details about the available functions provided by the SDK.
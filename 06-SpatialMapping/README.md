# ZED Spatial Mapping

Spatial mapping (also called 3D reconstruction) is the ability to create a 3D map of the environment. It allows a device to understand and interact with the real world. Spatial mapping is useful for collision avoidance, motion planning, and realistic blending of the real and virtual world.

**Overview**

* [Spatial Mapping Configuration](https://github.com/qt-truong/zed-examples/tree/master/06-SpatialMapping#spatial-mapping-configuration)
* [Enabling Spatial Mapping](https://github.com/qt-truong/zed-examples/tree/master/06-SpatialMapping#enabling-spatial-mapping)
* [Getting a 3D Map](https://github.com/qt-truong/zed-examples/tree/master/06-SpatialMapping#enabling-spatial-mapping)
* [Code Example](https://github.com/qt-truong/zed-examples/tree/master/06-SpatialMapping#code-example)


## Spatial Mapping Configuration
Spatial Mapping configuration is similar to previous modules. Use `InitParameters` to set the video mode, coordinate system and units.

**C++**
```cpp
// Set configuration parameters
InitParameters init_params;
init_params.camera_resolution = RESOLUTION::HD720; // Use HD720 video mode (default fps: 60)
init_params.coordinate_system = COORDINATE_SYSTEM::RIGHT_HANDED_Y_UP; // Use a right-handed Y-up coordinate system
init_params.coordinate_units = UNIT::METER; // Set units in meters
```

**Python**
```python
#Set configuration parameters
init_params = sl.InitParameters()
init_params.camera_resolution = sl.RESOLUTION.HD720 # Use HD720 video mode (default fps: 60)
init_params.coordinate_system = sl.COORDINATE_SYSTEM.RIGHT_HANDED_Y_UP # Use a right-handed Y-up coordinate system
init_params.coordinate_units = sl.UNIT.METER # Set units in meters
```

**C#**
```csharp
// Set configuration parameters
InitParameters init_params = new InitParameters();
init_params.resolution = RESOLUTION.HD720; // Use HD720 video mode (default fps: 60)
init_params.coordinateSystem = COORDINATE_SYSTEM.RIGHT_HANDED_Y_UP; // Use a right-handed Y-up coordinate system
init_params.coordinateUnits = UNIT.METER; // Set units in meters
```

The coordinate system determines the axes convention of the spatial map, and coordinate units specify the metrics.
Since spatial mapping uses positional tracking to create its map, we recommend using the HD720 video mode at 60fps for optimal results.


## Enabling Spatial Mapping
After opening the camera, enable positional tracking using `enablePositionalTracking()` and spatial mapping using `enableSpatialMapping()` with `SpatialMappingParameters` default parameters.

`SpatialMappingParameters` has two main settings to adjust: resolution and range.

### Adjusting Resolution
You can specify mapping resolution manually (in meters) or through the following presets:

* `MAPPING_RESOLUTION::HIGH`: Set resolution to 2cm. Use this setting to map small areas.
* `MAPPING_RESOLUTION::MEDIUM`: Set resolution to 5cm. Good balance between performance and level of detail.
* `MAPPING_RESOLUTION::LOW`: Set resolution of 8cm. Use this settings to map large areas or create a collision mesh.

Note that mapping on the HIGH resolution setting is resource-intensive, and slows down spatial map updates. Use lower resolutions to get faster updates.

**C++**
```cpp
SpatialMappingParameters mapping_parameters;
mapping_parameters.resolution_meter = 0.03 ;  // Set resolution to 3cm
mapping_parameters.resolution_meter = SpatialMappingParameters::get(MAPPING_RESOLUTION::LOW); // Or use preset
```

**Python**
```python
mapping_parameters = sl.SpatialMappingParameters()
mapping_parameters.resolution_meter = 0.03 # Set resolution to 3cm
mapping_parameters.resolution_meter = mapping_parameters.get_resolution_preset(sl.MAPPING_RESOLUTION.LOW) # Or use preset
```

**C#**
```csharp
SpatialMappingParameters mapping_parameters = new SpatialMappingParameters();
mapping_parameters.resolutionMeter = 0.03f; // Set resolution to 3cm
mapping_parameters.resolutionMeter = SpatialMappingParameters.get(MAPPING_RESOLUTION.LOW); // Or use preset.
```

### Adjusting Range
You can manually specify the range of depth data to be integrated into the mapping process, or set it through one of the following presets:

* `MAPPING_RANGE::NEAR`: integrates depth up to 3.5 meters.
* `MAPPING_RANGE::MEDIUM`: integrates depth up to 5 meters.
* `MAPPING_RANGE::FAR`: integrates depth up to 10 meters.

Since depth accuracy decreases with distance, mapping range has been limited to 10 meters. Use a careful balance of resolution and range when mapping to maintain a good quality/performance ratio.

**C++**
```cpp
SpatialMappingParameters mapping_parameters;
mapping_parameters.range_meter = 5 ;  // Set maximum depth mapping range to 5m
mapping_parameters.range_meter = SpatialMappingParameters::get(MAPPING_RANGE::MEDIUM); // Or use preset
```

**Python**
```python
mapping_parameters = sl.SpatialMappingParameters()
mapping_parameters.range_meter = 5 # Set maximum depth mapping range to 5m
mapping_parameters.range_meter = mapping_parameters.get_range_preset(sl.MAPPING_RANGE.MEDIUM) # Or use preset
```

**C#**
```csharp
SpatialMappingParameters mapping_parameters = new SpatialMappingParameters();
mapping_parameters.rangeMeter = 5f; // Set maximum depth mapping range to 5m
mapping_parameters.rangeMeter = SpatialMappingParameters.get(MAPPING_RANGE.MEDIUM); // Or use preset.
```

### Choosing the Map Type
The spatial map can be of the following types:

* `SPATIAL_MAP_TYPE::MESH`: Generates a mesh with points, faces and edges.
* `SPATIAL_MAP_TYPE::FUSED_POINT_CLOUD`: Generates a colored point cloud. This format takes more memory due to color information.

**C++**
```cpp
SpatialMappingParameters mapping_parameters;
// Set mapping with mesh output
spatial_mapping_parameters.map_type = SpatialMappingParameters::SPATIAL_MAP_TYPE::MESH;
// or select point cloud output
spatial_mapping_parameters.map_type = SpatialMappingParameters::SPATIAL_MAP_TYPE::FUSED_POINT_CLOUD;
```

**Python**
```python
mapping_parameters = sl.SpatialMappingParameters()
# Set mapping with mesh output
mapping_parameters.map_type = sl.SPATIAL_MAP_TYPE.MESH
# or select point cloud output
mapping_parameters.map_type = sl.SPATIAL_MAP_TYPE.FUSED_POINT_CLOUD 
```

**C#**
```csharp
SpatialMappingParameters mapping_parameters = new SpatialMappingParameters();
// Set mapping with mesh output
mapping_parameters.map_type = sl.SPATIAL_MAP_TYPE.MESH;
// or select point cloud output
mapping_parameters.map_type = sl.SPATIAL_MAP_TYPE.FUSED_POINT_CLOUD;
```


### Enabling textures
By allowing the spatial mapping to save images of the scene, you can export a textured version of the mesh. This option is available only for Meshes, not for Fused Point Cloud, which already have color information attached to each point.

**C++**
```cpp
SpatialMappingParameters mapping_parameters;
mapping_parameters.save_texture = true ;  // Scene texture will be recorded
```

**Python**
```python
mapping_parameters = sl.SpatialMappingParameters()
mapping_parameters.save_texture = True # Scene texture will be recorded
```

**C#**
```csharp
SpatialMappingParameters mapping_parameters = new SpatialMappingParameters();
mapping_parameters.saveTexture = true;  // Scene texture will be recorded
```

## Getting a 3D Map
To start spatial mapping, you just need to call `grab()`. The Spatial Mapping will ingest new images, depth information, and tracking poses in the background to create the spatial map.


### One-time Mapping
The process to map a whole area and save the result is:

- Start spatial mapping. Capture the entire area.
- When done, use `extractWholeSpatialMap()` to retrieve the spatial map. This can take some time depending on the size and resolution of the spatial map.

If you choose to create a Mesh you can:

- Use `mesh.filter()` to refine mesh.
- Create the mesh texture using `mesh.applyTexture()`.

With both types you can:

- Save the map with `map.save("filename.obj")`.

**C++**
```cpp
// Configure spatial mapping parameters
sl::SpatialMappingParameters mapping_parameters(SpatialMappingParameters::MAPPING_RESOLUTION::LOW,
                                                SpatialMappingParameters::MAPPING_RANGE::FAR);
// In this cas we want to create a Mesh
mapping_parameters.map_type = SpatialMappingParameters::SPATIAL_MAP_TYPE::MESH;
mapping_parameters.save_texture = true;
filter_params.set(MeshFilterParameters::MESH_FILTER::LOW); // not available for fused point cloud

// Enable tracking and mapping
zed.enableTracking();
zed.enableSpatialMapping(mapping_parameters);

sl::Mesh mesh; // Create a mesh object
int timer=0;

// Grab 500 frames and stop
while (timer < 500) {
  if (zed.grab() == ERROR_CODE::SUCCESS) {
    // When grab() = SUCCESS, a new image, depth and pose is available.
    // Spatial mapping automatically ingests the new data to build the mesh.
    timer++;
  }
}

// Retrieve the spatial map
zed.extractWholeSpatialMap(mesh);
// Filter the mesh
mesh.filter(filter_params); // not available for fused point cloud
// Apply the texture
mesh.applyTexture(); // not available for fused point cloud
// Save the mesh in .obj format
mesh.save("mesh.obj");
```

**Python**
```python
# Configure spatial mapping parameters
mapping_parameters = sl.SpatialMappingParameters(sl.MAPPING_RESOLUTION.LOW,
                                                 sl.MAPPING_RANGE.FAR)
mapping_parameters.map_type = sl.MAP_TYPE.MESH
mapping_parameters.save_texture = True
filter_params = sl.MeshFilterParameters() # not available for fused point cloud
filter_params.set(sl.MESH_FILTER.LOW) # not available for fused point cloud

# Enable tracking and mapping
tracking_parameters = sl.TrackingParameters()
zed.enable_tracking(tracking_parameters)
zed.enable_spatial_mapping(mapping_parameters)

mesh = sl.Mesh() # Create a mesh object
timer=0

# Grab 500 frames and stop
while timer < 500 :
  if zed.grab() == sl.ERROR_CODE.SUCCESS :
    # When grab() = SUCCESS, a new image, depth and pose is available.
    # Spatial mapping automatically ingests the new data to build the mesh.
    timer+=1

# Retrieve the spatial map
zed.extract_whole_spatial_map(mesh)
# Filter the mesh
mesh.filter(filter_params) # not available for fused point cloud
# Apply the texture
mesh.apply_texture() # not available for fused point cloud
# Save the mesh in .obj format
mesh.save("mesh.obj")
```

**C#**
```csharp
// Configure spatial mapping parameters
SpatialMappingParameters mappingParams = new SpatialMappingParameters();
mappingParams.resolutionMeter = SpatialMappingParameters.get(MAPPING_RESOLUTION.LOW);
mappingParams.rangeMeter = SpatialMappingParameters.get(MAPPING_RANGE.FAR);
mappingParams.saveTexture = true;

//Enable tracking and mapping
PositionalTrackingParameters trackingParams = new PositionalTrackingParameters();
zed.EnablePositionalTracking(ref trackingParams);
zed.EnableSpatialMapping(ref mappingParams);

RuntimeParameters runtimeParameters = new RuntimeParameters();

int timer=0;
Mesh mesh = new Mesh();

// Grab 500 frames and stop
while (timer < 500) {
  if (zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS) {
    // When grab() = SUCCESS, a new image, depth and pose is available.
    // Spatial mapping automatically ingests the new data to build the mesh.
    timer++;
  }
}
// Retrieve the spatial map
zed.ExtractWholeSpatialMap();
// Filter the mesh
zed.FilterMesh(FILTER.LOW, ref mesh); // not available for fused point cloud
// Apply the texture
zed.ApplyTexture(ref mesh); // not available for fused point cloud
// Save the mesh in .obj format
zed.SaveMesh("mesh.obj", MESH_FILE_FORMAT.OBJ);
```


### Continuous Mapping
To get a spatial map continuously, a request and update system is available:

- Enable spatial mapping.
- Send a request to get an updated spatial map using `requestSpatialMapAsync()`. This will launch spatial map extraction in the background.
- Check the request status with `getSpatialMapRequestStatusAsync()`. When the spatial map is ready, a SUCCESS status is returned.
- Retrieve the spatial map with `retrieveSpatialMapAsync(sl::Mesh).`
- Use in your application.

Requesting and retrieving a spatial map is resource-intensive. To improve performance, do not request a new spatial map too frequently.

**C++**
```cpp
// Request an updated spatial map every 0.5s
sl::Mesh mesh; // Create a Mesh object or a FusedPointCloudObject
int timer=0;
while (1) {
  if (zed.grab() == ERROR_CODE::SUCCESS) {

      // Request an update of the mesh every 30 frames (0.5s in HD720 mode)
      if (timer%30 == 0)
         zed.requestSpatialMapAsync();

      // Retrieve spatial map when ready
      if (zed.getSpatialMapRequestStatusAsync() == ERROR_CODE::SUCCESS && timer > 0)
         zed.retrieveSpatialMapAsync(mesh);

      timer++;
  }
}
```

**Python**
```python
# Request an updated spatial map every 0.5s
mesh = sl.Mesh() # Create a Mesh object or FusedPointCloud
timer = 0

while 1 :
  if zed.grab() == sl.ERROR_CODE.SUCCESS :

      # Request an update of the spatial map every 30 frames (0.5s in HD720 mode)
      if timer%30 == 0 :
         zed.request_spatial_map_async()

      # Retrieve spatial_map when ready
      if zed.get_spatial_map_request_status_async() == sl.ERROR_CODE.SUCCESS && timer > 0 :
         zed.retrieve_spatial_map_async(mesh)

      timer+=1
```

**C#**
```csharp
// Request an updated spatial map every 0.5s
int timer=0;
RuntimeParameters runtimeParameters = new RuntimeParamters();
Mesh mesh = new Mesh();

while (1) {
  if (zed.Grab(ref runtimeParameters) == ERROR_CODE.SUCCESS) {
      // Request an update of the mesh every 30 frames (0.5s in HD720 mode)
      if (timer%30 == 0)
        zed.RequestSpatialMap();

      // Retrieve spatial map when ready
      if (zedCamera.GetMeshRequestStatus() == ERROR_CODE.SUCCESS && timer > 0){
        zed.RetrieveSpatialMap(ref mesh);
      }       

      timer++;
  }
}
```


#### Disabling Spatial Mapping
Once spatial mapping is disabled, you will not be able to retrieve the spatial map anymore. Make sure to extract it before disabling the modules and closing the camera.

**C++**
```cpp
// Disable spatial mapping, positional tracking and close the camera
zed.disableSpatialMapping();
zed.disableTracking();
zed.close();
return 0;
```

**Python**
```python
# Disable spatial mapping, positional tracking and close the camera
zed.disable_spatial_mapping()
zed.disable_tracking()
zed.close()
```

**C#**
```csharp
// Disable spatial mapping, positional tracking and close the camera
zed.DisableSpatialMapping();
zed.DisablePositionalTracking();
zed.Close();
```


#### Spatial Mapping States
When using spatial mapping, you can check its state by calling `getSpatialMappingState()`.

**C++**
```cpp
SPATIAL_MAPPING_STATE state = zed.getSpatialMappingState();
```

**Python**
```python
state = zed.get_spatial_mapping_state()
```

**C#**
```csharp
SPATIAL_MAPPING_STATE state = zed.GetSpatialMappingState();
```

When spatial mapping is running correctly, the module will return `SPATIAL_MAPPING_STATE::OK`. If a system does not allow the ZED SDK to reach the framerate required for a consistent mapping experience, the module will return `SPATIAL_MAPPING_STATE::FPS_TOO_LOW`. Similarly, if the module reaches the memory limit, it will return `SPATIAL_MAPPING_STATE::NOT_ENOUGH_MEMORY`. In both cases, spatial mapping will stop integrating new data into the model, but you can still extract the 3D model.

## Code Example
For code examples, check out the [Tutorial](https://github.com/qt-truong/zed-examples/tree/master/09-Tutorials/tutorial%205%20-%20spatial%20mapping) and [Sample](https://github.com/qt-truong/zed-examples/tree/master/10-Samples/spatial%20mapping) on GitHub.

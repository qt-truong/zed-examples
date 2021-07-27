# ZED Spatial Mapping

Spatial mapping (also called 3D reconstruction) is the ability to create a 3D map of the environment. It allows a device to understand and interact with the real world. Spatial mapping is useful for collision avoidance, motion planning, and realistic blending of the real and virtual world.

## Overview

* [How It Works](#how-it-works)
* [Getting a 3D Map](#getting-a-3d-map)
* [Spatial Mapping Configuration](#spatial-mapping-configuration)
* [Code Examples](#code-examples)
* [Integrations](#integrations)
* [Docs](#documentation)

## How It Works

The ZED continuously scans its surroundings and creates a 3D map of what it sees. It updates this map as the device moves around and captures new elements in the scene. Since the camera perceives distances beyond the range of traditional RGB-D sensors, it can quickly reconstruct 3D maps of large indoor and outdoor areas.

The SDK saves mapping data relative to a fixed reference coordinate frame, known as the World Frame. If [Area Memory](https://www.stereolabs.com/docs/positional-tracking/area-memory/) is enabled and an Area file is provided during initialization, the map can be loaded repeatedly across sessions and will always appear in the same physical location.

## Getting a 3D Map

After opening the camera, enable positional tracking using `enablePositionalTracking()` and spatial mapping using `enableSpatialMapping()` with `SpatialMappingParameters` default parameters.

`SpatialMappingParameters` has two main settings to adjust: resolution and range. Refer to the [documentation](https://www.stereolabs.com/docs/spatial-mapping/using-mapping/#enabling-spatial-mapping) for details about the available parameters.

### One-time mapping
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

It is also possible to get a spatial map continuously. For more details on how to perform this, refer to [our documentation](https://www.stereolabs.com/docs/spatial-mapping/using-mapping/#continuous-mapping)


## Spatial Mapping Configuration
Spatial Mapping configuration is similar to previous modules. Use `InitParameters` to set the video mode, coordinate system and units. To read more about spatial mapping configuration, check out this dedicated [section](https://www.stereolabs.com/docs/spatial-mapping/using-mapping/#spatial-mapping-configuration)


## Code Examples
For code examples, check out the Spatial Mapping [Tutorial](../09-Tutorials/tutorial%205%20-%20spatial%20mapping) and [Sample](../10-Samples/spatial%20mapping) in this repository.


## Integrations
Check the [Integrations](../11-Integrations#overview) list to use spatial mapping with your favorite suite of tools and libraries.

## Documentation
For more information, read the [Documentation](https://www.stereolabs.com/docs/spatial-mapping/) or these specific sections:

* [Capturing a Spatial Map](https://www.stereolabs.com/docs/spatial-mapping/#capturing-a-spatial-map)
* [Spatial Mapping Parameters](https://www.stereolabs.com/docs/spatial-mapping/#spatial-mapping-parameters)
* [Spatial Mapping Configuration](https://www.stereolabs.com/docs/spatial-mapping/using-mapping/#spatial-mapping-configuration)
* [Getting a 3D Map](https://www.stereolabs.com/docs/spatial-mapping/using-mapping/#getting-a-3d-map)
* [Plane Detection](https://www.stereolabs.com/docs/spatial-mapping/plane-detection/)
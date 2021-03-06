# ProAut OctoMap

## Introduction

This package was designed to automatically remove outdated voxels from the [octomap](http://wiki.ros.org/octomap).

We described our motivation and concept on our [octomap-website](https://www.tu-chemnitz.de/etit/proaut/octo) - here you will also find two supportive videos.
For further explanations, you may want to have a look at this [workshop abstract](http://nbn-resolving.de/urn:nbn:de:bsz:ch1-qucosa-226576).


## Nodes

Our implementation of decay:
```
rosrun octomap_pa octree_stamped_pa_node
roslaunch octomap_pa octomap_stamped_pa.launch
```

The native implementation of decay by the [original octomap package](https://octomap.github.io):
```
rosrun octomap_pa octree_stamped_native_node
roslaunch octomap_pa octomap_stamped_native.launch
```

Simple node without decay:
```
rosrun octomap_pa octree_pa_node
roslaunch octomap_pa octomap_pa.launch
```


### Input and Output Topics:

Topic Name             | Type                                                                                     | Description
-----------------------|------------------------------------------------------------------------------------------|---------------------------------
"~/in_cloud"           | [sensor_msgs/PointCloud2](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud2.html) | Input as <em>new</em> pointcloud type.
"~/in_cloud_old"       | [sensor_msgs/PointCloud](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud.html)   | Input as <em>old</em> pointloud type. Will be converted to new pointcloud type.
"~/in_laser"           | [sensor_msgs/LaserScan](http://docs.ros.org/api/sensor_msgs/html/msg/LaserScan.html)     | Input as single laser scan. Will be converted to new pointcloud type by package "laser_geometry".
"~/out_octomap"        | [octomap_msgs/Octomap](http://docs.ros.org/api/octomap_msgs/html/msg/Octomap.html)       | Output of binary octomap - voxels are either free or occupied (smaller in size).
"~/out_octomap_full"   | [octomap_msgs/Octomap](http://docs.ros.org/api/octomap_msgs/html/msg/Octomap.html)       | Output of octomap (full size).
"~/out_cloud_free"     | [sensor_msgs/PointCloud2](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud2.html) | Output of all free voxels as pointcloud.
"~/out_cloud_occupied" | [sensor_msgs/PointCloud2](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud2.html) | Output of all occupied voxels as pointcloud.

All topics can be remapped using parameters (see below).


### Services:

Service Name       | Type                                                                                                            | Description
-------------------|-----------------------------------------------------------------------------------------------------------------|---------------------------------
"~/clear"          | std_srvs/Empty                                                                                                  | Deletes internal octomap.
"~/getsize"        | [octomap_pa/OctomapPaGetSize](https://github.com/TUC-ProAut/ros_octomap/blob/master/srv/OctomapPaGetSize.srv)   | Returning number of nodes, total size in bytes and number of inserted measurments.
"~/save"           | [octomap_pa/OctomapPaFileName](https://github.com/TUC-ProAut/ros_octomap/blob/master/srv/OctomapPaFileName.srv) | Storing the current octomap as file - timestamps are not saved.
"~/load"           | [octomap_pa/OctomapPaFileName](https://github.com/TUC-ProAut/ros_octomap/blob/master/srv/OctomapPaFileName.srv) | Loading a octomap from file - timestamps are ignored.


### Parameters:

#### degrading of voxels
Parameter Name               | Type                 | Description
-----------------------------|----------------------|-------------------------------------
"~/degrading_time"           | double               | Duration how long the outdated nodes will be kept.
"~/auto_degrading"           | bool                 | Turns on automatic degrading.
"~/auto_degrading_intervall" | double               | Intervall for automatic degrading.

#### pointcloud insertion
Parameter Name               | Type                 | Description
-----------------------------|----------------------|-------------------------------------
"~/map_prob_hit"             | double               | Probability that a positive measurement relates to a occupied voxel.
"~/map_prob_miss"            | double               | Probability that a negative measurement relates to a occupied voxel.
"~/pcd_voxel_active"         | bool                 | Use voxel-filter for pointcloud insertion.
"~/pcd_voxel_explicit"       | bool                 | Use pcl-filter instead of octomap-filter.
"~/pcd_voxel_explicit_relative_resolution" | double | Relative resolution of pcl-filter.

#### octomap in general
Parameter Name               | Type                 | Description
-----------------------------|----------------------|-------------------------------------
"~/output_frame"             | string               | Coordinate system for insertion and output.
"~/map_resolution"           | double               | Side length of one voxel (in meters).
"~/map_prob_threshold"       | double               | Threshold for binary evaluation of single voxels.
"~/map_clamp_min"            | double               | Lower clamping value of occupancy probability.
"~/map_clamp_max"            | double               | Upper clamping value of occupancy probability.

#### topics and services
Parameter Name               | Type                 | Description
-----------------------------|----------------------|-------------------------------------
"~/topic_in_cloud"           | string               | Name of input topic for new pointclouds.
"~/topic_in_cloud_old"       | string               | Name of input topic for old pointclouds.
"~/topic_in_laser"           | string               | Name of input topic for laser scans.
"~/topic_out_octomap"        | string               | Name of output topic for binary octomap.
"~/topic_out_octomap_full"   | string               | Name of output topic for full octomap.
"~/topic_out_cload_free"     | string               | Name of output topic for free voxels.
"~/topic_out_cloud_occupied" | string               | Name of output topic for occupied voxels.


See also [this config file](https://github.com/TUC-ProAut/ros_octomap/blob/master/config/parameter.yaml).
It contains all parameters and their default value.


## Links

Source code at github:
> https://github.com/TUC-ProAut/ros_octomap

Related packages:
> https://github.com/TUC-ProAut/ros_parameter


## ROS Build-Status and Documentation

ROS-Distribution | Build-Status                                                                                                                                                        | Documentation
-----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------
Indigo           | [![Build Status](http://build.ros.org/buildStatus/icon?job=Idev__octomap_pa__ubuntu_trusty_amd64)](http://build.ros.org/job/Idev__octomap_pa__ubuntu_trusty_amd64/) | [docs.ros.org](http://docs.ros.org/indigo/api/octomap_pa/html/index.html)
Jade             | EOL May 2017                                                                                                                                                        | -
Kinetic          | [![Build Status](http://build.ros.org/buildStatus/icon?job=Kdev__octomap_pa__ubuntu_xenial_amd64)](http://build.ros.org/job/Kdev__octomap_pa__ubuntu_xenial_amd64/) | [docs.ros.org](http://docs.ros.org/kinetic/api/octomap_pa/html/index.html)
Lunar            | [![Build Status](http://build.ros.org/buildStatus/icon?job=Ldev__octomap_pa__ubuntu_xenial_amd64)](http://build.ros.org/job/Ldev__octomap_pa__ubuntu_xenial_amd64/) | [docs.ros.org](http://docs.ros.org/lunar/api/octomap_pa/html/index.html)

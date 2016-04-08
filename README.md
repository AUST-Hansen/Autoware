sick_ldmrs_laser
================

[![Build Status](https://travis-ci.org/SICKAG/sick_ldmrs_laser.svg?branch=indigo)](https://travis-ci.org/SICKAG/sick_ldmrs_laser)

This stack provides a ROS driver for the SICK LD-MRS series of laser scanners.

![](https://www.mysick.com/saqqara/image.aspx?id=im0056631)


Supported Hardware
------------------

This driver should work with all of the following products. However, it has
only been tested and confirmed working on the LD-MRS800001S01 so far. If you
try any of the other scanners and run into trouble, please open an issue.

| **device name**    | **part no.**                                                                | **description**      | **tested?** |
|--------------------|-----------------------------------------------------------------------------|----------------------|:-----------:|
| LD-MRS400001       | [1045046](https://www.mysick.com/eCatSEO.aspx?go=DataSheet&ProductID=34057) | 4 layer (standard)   |             |
| LD-MRS400001S01    | [1052960](https://www.mysick.com/eCatSEO.aspx?go=DataSheet&ProductID=55876) | 4 layer (standard)   |             |
| LD-MRS400102 HD    | [1047145](https://www.mysick.com/eCatSEO.aspx?go=DataSheet&ProductID=34058) | 4 layer (heavy duty) |             |
| LD-MRS400102S01 HD | [1052961](https://www.mysick.com/eCatSEO.aspx?go=DataSheet&ProductID=55877) | 4 layer (heavy duty) |             |
| LD-MRS800001S01    | [1069408](https://www.mysick.com/eCatSEO.aspx?go=DataSheet&ProductID=96894) | 8 layer (standard)   |      ✔      |


Installation
------------

In the following instructions, replace `<rosdistro>` with the name of your ROS distro (e.g., `indigo`).

### From binaries

The driver has not been released yet. But once that happens, you can install it directly by typing:

~~`sudo apt-get install ros-<rosdistro>-sick-ldmrs-laser`~~

### From source

```bash
source /opt/ros/<rosdistro>/setup.bash
mkdir -p ~/ros_catkin_ws/src/
cd ~/ros_catkin_ws/src/
git clone git@gitlab.informatik.uni-osnabrueck.de:sickag/libsick-ldmrs.git                    # TODO: GitHub
git clone -b <rosdistro> git@gitlab.informatik.uni-osnabrueck.de:sickag/sick_ldmrs_laser.git  # TODO: GitHub
cd ..
catkin_make_isolated --install -DCMAKE_BUILD_TYPE=Release
source ~/ros_catkin_ws/install_isolated/setup.bash
```

Quick Start
-----------

```bash
roslaunch sick_ldmrs_tools sick_ldmrs_demo.launch
rosrun rviz rviz -d $(rospack find sick_ldmrs_tools)/config/sick_ldmrs.rviz
rosrun rqt_reconfigure rqt_reconfigure
rosrun rqt_robot_monitor rqt_robot_monitor
```

Package overview
----------------

* **sick_ldmrs_driver**: This package provides the main driver node,
  `sick_ldmrs_node`.
* **sick_ldmrs_description**: This package provides an URDF description of the
  scanner along with the corresponding mesh.
* **sick_ldmrs_msgs**: This package provides ROS message definitions for the
  scanner's object tracking functionality and a PCL point type used in the
  PointCloud2 topic.
* **sick_ldmrs_tools**: This package provides example nodes that demonstrate
  how to subscribe to the topics provided by the scanner and use the data.


ROS API
-------

### sick\_ldmrs\_node

#### Published Topics

`cloud` ([sensor\_msgs/PointCloud2](http://docs.ros.org/api/sensor_msgs/html/msg/PointCloud2.html))

- The published point cloud. The meaning of the fields is documented in
  [sick_ldmrs_point_type.h](sick_ldmrs_msgs/include/sick_ldmrs_msgs/sick_ldmrs_point_type.h).

`objects` (sick_ldmrs_msgs/ObjectArray)

- The output of the object tracking functionality of the scanner. See
  [ObjectArray.msg](sick_ldmrs_msgs/msg/ObjectArray.msg) and
  [Object.msg](sick_ldmrs_msgs/msg/ObjectArray.msg).

`diagnostics` ([diagnostic\_msgs/DiagnosticArray](http://docs.ros.org/api/diagnostic_msgs/html/msg/DiagnosticArray.html))

- ROS [diagnostics](http://wiki.ros.org/diagnostics) information.


#### Parameters

##### Dynamically Reconfigurable Parameters

See the [dynamic\_reconfigure](http://wiki.ros.org/dynamic_reconfigure)
package for details on dynamically reconfigurable parameters.

`~frame_id` (`str`, default: ldmrs)

- The TF frame in which point clouds will be returned.

`~start_angle` (`double`, default: 0.872664625997)

- The angle of the first range measurement \[rad\]. Range: -1.04610672041 to 0.872664625997

`~end_angle` (`double`, default: -1.0471975512)

- The angle of the last range measurement \[rad\]. Range: -1.0471975512 to 0.871573795215

`~scan_frequency` (`int`, default: 2)

- Scan frequency. Possible values are:
  ScanFreq1250 (0): Scan frequency 12.5 Hz,
  ScanFreq2500 (1): Scan frequency 25.0 Hz,
  ScanFreq5000 (2): Scan frequency 50.0 Hz.

`~sync_angle_offset` (`double`, default: 0.0)

- Angle under which the LD-MRS measures at the time of the sync pulse \[rad\].
  Range: -3.14159265359 to 3.1410472382

`~angular_resolution_type` (`int`, default: 1)

- Angular resolution type. Possible values are:
  FocusedRes (0): Focused resolution,
  ConstantRes (1): Constant resolution,
  FlexRes (2): Flexible resolution

`~layer_range_reduction` (`int`, default: 0)

- Possible values are:
  RangeFull (0): All layers full range,
  RangeLowerReduced (1): Lower 4 layers reduced range,
  RangeUpperReduced (2): Upper 4 layers reduced range,
  RangeAllReduced (3): All 8 layers reduced range.

`~ignore_near_range` (`bool`, default: False)

- Ignore scan points up to 15m. Requires `layer_range_reduction` = RangeLowerReduced.

`~sensitivity_control` (`bool`, default: False)

- Reduce the sensitivity automatically in case of extraneous light.

`~flexres_start_angle1` (`double`, default: 0.872664625997)

- FlexRes: start angle of sector 1. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle2` (`double`, default: 0.610865238198)

- FlexRes: start angle of sector 2. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle3` (`double`, default: 0.523598775598)

- FlexRes: start angle of sector 3. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle4` (`double`, default: 0.349065850399)

- FlexRes: start angle of sector 4. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle5` (`double`, default: 0.0)

- FlexRes: start angle of sector 5. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle6` (`double`, default: -0.349065850399)

- FlexRes: start angle of sector 6. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle7` (`double`, default: -0.523598775598)

- FlexRes: start angle of sector 7. Range: -1.04610672041 to 0.872664625997

`~flexres_start_angle8` (`double`, default: -0.698131700798)

- FlexRes: start angle of sector 8. Range: -1.04610672041 to 0.872664625997

`~flexres_resolution1` (`int`, default: 32)

- FlexRes: angular resolution of sector 1. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution2` (`int`, default: 16)

- FlexRes: angular resolution of sector 2. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution3` (`int`, default: 8)

- FlexRes: angular resolution of sector 3. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution4` (`int`, default: 4)

- FlexRes: angular resolution of sector 4. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution5` (`int`, default: 8)

- FlexRes: angular resolution of sector 5. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution6` (`int`, default: 16)

- FlexRes: angular resolution of sector 6. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution7` (`int`, default: 32)

- FlexRes: angular resolution of sector 7. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~flexres_resolution8` (`int`, default: 16)

- FlexRes: angular resolution of sector 8. Possible values are:
  Res0125 (4): Angular resolution 0.125 degrees,
  Res0250 (8): Angular resolution 0.25 degrees,
  Res0500 (16): Angular resolution 0.5 degrees,
  Res1000 (32): Angular resolution 1.0 degrees

`~contour_point_density` (`int`, default: 2)

- Contour point density. Possible values are:
  ClosestPointOnly (0): Closest point only,
  LowDensity (1): Low density,
  HighDensity(2): High density

`~min_object_age` (`int`, default: 0)

- Minimum tracking age (number of scans) of an object to be transmitted.
  Range: 0 to 65535

`~max_prediction_age` (`int`, default: 0)

- Maximum prediction age (number of scans) of an object to be transmitted.
  Range: 0 to 65535

##### Not Dynamically Reconfigurable Parameters

`~hostname` (`string`, default: "192.168.0.1")

- The host name or IP address of the laser scanner.


------------------------------------------------------------------------

![SICK Logo](img/Logo_SICK_AG_2009.png "SICK Logo")
![DFKI Logo](img/DFKI-Logo_eng_sm.png "DFKI Logo")

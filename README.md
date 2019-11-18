# mas_navigation_tools

## Introduction

This package provides different nodes which provides additional features related for navigation.

## pose_visualiser

### Scripts

* `pose_visualiser`:
    1. reads poses from a navigation goals yaml file in which poses are represented as `<NAME>: [x, y, theta]` pairs and
    2. publishes two marker arrays: one array with arrow markers and another one with text markers

### Published Topic

* `/poses`: visualization_msgs.MarkerArray
* `/pose_text`: visualization_msgs.MarkerArray

### Launch Files
1. `pose_visualiser.launch`: Launch file for the `pose_visualiser` node. Two parameters can be specified in the launch file:
    * `pose_frame`: Frame in which the poses should be published
    * `pose_description_file`: Absolute path to a yaml file in which poses are represented as `<NAME>: [x, y, theta]` pairs

## path_length_calculator_node

### Scripts
1. path_length_calculator_node: This node provides listens to nav_msgs Path topic (which contains a global plan for the mobile base) as an array of poses and calculates the path lenght based on the distance between two points of each pose.

### Subscribed Topics
* "event_in": Start/Stop node
* "plan": Calculate length of this plan.

### Published Topics
* "event_out": Succes/Failure string output
* "path_length": Gives length of the path published on plan

### Launch Files
1. path_length_calculator.launch: Launch path_length_calculator_node.  

## pose_array_to_path
1. pose_array_to_path_node: Subscribes to pose array topic, republishes as nav_msgs/Path topic.

### Subscribed Topics
* "pose_array": PoseArray input topic.

### Published Topics
* "path": Path output topic.

## save_base_map_poses_to_file

### Scripts
1. save_base_map_poses_to_file: Used to create a file in the current folded which contains points of interest in a given map.  

## Arguments

* The default created file is named "navigation_goals.yaml". If a name is provided as an argument, then file name is <provided_name>.yaml

### Parameters
* node_frequency: Frecuency of node
* "~plan" : set the global_plan_topic to be calculated.

1. pose_array_to_path_converter.launch

### Parameters
* node_frequency: Frecuency of node.
* "~pose_array": Pose Array subscribed topic.
* "~path": Path published topic name.

## map_saver

1. map_saver: Stores a map on the path of the package mcr_default_env_config using the node map_saver from map_server package.

### Arguments

The map name is introduced on the commandline once the script is running.


## Laser distances

### Parameters
* "footprint": rectangular footprint of the robot

### Subscribed Topics
* "scan_front" and "scan_rear": laser scan topics

### Published Topics
* "distances": Minimum distance from footprint to obstacle in the front, right, rear and left directions (in that order)

## Map Annotation Tool
The Map Annotation Tool eases annotating simulated robot navigation maps with goal poses, by allowing storage, retrieval, and editing of navigational goal markers.

Authors:
1. Sushant Vijay Chavan
2. Ahmed Faisal Abdelrahman

The map annotation tool facilitates annotating navigation maps efficiently and flexibly. It includes the following functionalities:
- Setting and saving robot goal pose arrow markers on the map by clicking and dragging to set (x, y, theta)
- Editing set goal pose values from a drop-down menu
- Renaming goal pose markers
- Resizing the markers
- Reloading previously saved goal poses

The following GIF demonstrates the map annotation tool being used to add and update a new pose on RViz:

![Demo](docs/MAT_Demo.gif)

### Installation
Installing the plugin requires simply cloning this repository in the catkin workspace and building. For a workspace named catkin_ws in the root directory:
```sh
$ cd ~/catkin_ws/src/
$ git clone git@github.com:b-it-bots/map_annotation_tool.git
$ catkin_make or catkin build
```

### Instructions
#### Using the Map Annotation tool
The tool can be used by following these steps:
##### Initializing the tool:
- Having started RViz, click on the "Add a new tool" button (+) on the top panel
- In the pop-up window, double-click on the MapAnnotationTool. At this point, any goals saved to the file will appear as markers on the map (see instructions for file path settings below)
- Open the "Tool Properties" panel from the "Panels" tab. The panel contains the Map Annotation Field drop-down menu, from which navigation goals can be edited.
##### Placing navigational goals:
- To place a new goal marker, click on "Map Annotation Tool" on the top panel.
- On the map, click on the desired location on the map, and while holding the left mouse button, drag the cursor around to set the desired orientation. Releasing the left mouse button sets the new navigation pose.
##### Editing poses:
- To change the size of the pose markers, adjust the value in the "Marker Size" field.
- To change goal properties, expand the "Marker Container" drop-down list, and expand the entry of the goal pose to be edited.
- To change the name, position in x or y, or orientation, edit the respective field.


#### Setting the path for the navigation goals file
The navigational poses saved to and retrieved to a YAML file (navigation_goals.yaml), and stored in the following path in the catkin workspace:
```
~/catkin_ws/src/mas_common_robotics/mcr_environments/mcr_default_env_config/[ROBOT_ENV]
```
The containing folder is named according to the ROBOT_ENV environment variable. In order to read from the file and write to it correctly, this must be correctly set before using the tool.

### Architecture
![Architecture Diagram](docs/MAT_Architecture.png)
# Grasp Pose Detection (GPD)

## Required Modifications for GPD to compile and run on different machines:
- In gpd/cfg, the files ros_eigen_params.cfg and ros_eigen_params_vertical.cfg
    - The wights file path must be updated to match the file path on your system (line 35)
    - Additionally, the robot hand geometry (lines 7 - 20) must be changed if the class is being used on a robot other than Fetch.
- Make sure gpd is installed as a library, by using the sudo make install command, as specified in the gpd README.md
- In each launch file in gpd_ros
  - The config file must be updated to point to a config file, such as the ones modified in gpd/cfg

## Using the gpd_ros services
- Each launch file starts a different service.  
  - serv.launch starts a basic service, with a low init_bite value, and a no restictions on orientation
    - init_bite is the amount of the object that must be inside the robot's gripper
  - serv_vertical.launch launches an identical service, but with a restriction on orientation.  All grasps from this service will be within 45 degrees of the z (vertical) axis
  - serv_large_verticle.lainch startes a service identical to serv_verticle, but with a larger init_bite
    - A larger init bite helps the robot grasp larger objects, as more of the object is in the robot's hand, giving it a better grip
      - Using too large of an init_bite on a small object can cause no grasps to be found, as such, which one is used should depend on the object size
- The ur5.launch file included launches a node that can detect grasps
  - This topic waits for a message on the topic specified in the launch file and publishes the result to another topic
    - This version is not recommended due to needing to publish to one topic and subscribe for a result, as opposed to the service format
    - This launch file can be used to test the different types of messages that can be passed to gpd.  The node can accept a normal pointcloud, a cloud samples message, or a cloud indexed message, however the services can only use cloud indexed messages

## Required Modifications in order to create Additional detectors and Additional config files:
To create different versions of the gpd_detect service:
- A new launch file should be made
  - The format of serv.launch can be copied and modified
    - The node name must be modified and unique
    - The config file should be changed to the configuration desired for this instance of the gpd detector
    - Optionally the rviz_topic value field can be changed to make the detector publish to a different topic
- A config file should be created
  - The format of ros_eigen_params.cfg (in gpd/cfg) can be copied and modified
    - The comments in the config file explains what each parameter does.  Note that 0 is false, 1 is true

  
### If the GPD service crashes when processing a request:
- If the detector crashes after a request is made, re-start the detector and carefully look over the output on startup.  Specifically, look for lines saying if a file could not be found.  
  - Note that these lines are not printed in a way to distinguish them from other output lines, and will not cause any issues on startup, but will cause the detector to crash when a request is made
- If the GPD crashes after printing the line "Received cloud with x points, and y samples
  Processing cloud with z points.", ensure that Nans are removed from the pointcloud of both the scene and object before passing them to gpd.  (use pcl::removeNaNFromPointCloud)
  - GPD does use a remove Nan function, however this function can cause the service to crash when Nans are present
    - Function is in gpd/src/gpd/util/cloud.cpp, named Cloud::removeNans(), and crashes on the line eifilter.filter(*cloud_processed_);
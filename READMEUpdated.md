# Grasp Pose Detection (GPD)

## Required Modifications for GPD to compile and run on different machines:
- In gpd/cfg, the files ros_eigen_params.cfg and ros_eigen_params_vertical.cfg
    - The wights file path must be updated to match the file path on your system (line 35)
    - Additionally, the robot hand geometry (lines 7 - 20) must be changed if the class is being used on a robot other than Fetch.
- In each launch file in gpd_ros
  - The config file must be updated to point to a config file, such as the ones modified in gpd/cfg

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
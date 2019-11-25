# musc-le_experiment
Neurorobotics platform experiment description featuring 2DOF tendon-driven arm

![alt text](https://github.com/Roboy/musc-le_experiment/blob/master/images/screen.jpg)

## Setup in NRP

### Ubuntu 18
This NRP is shipped with ROS melodic and Gazebo 9

Get prerequisites
```
sudo apt install ros-$ROS_DISTRO-desktop-full libeigen3-dev libxml2-dev coinor-libipopt-dev \
qtbase5-dev qtdeclarative5-dev qtmultimedia5-dev qml-module-qtquick2 \
qml-module-qtquick-window2 qml-module-qtmultimedia qml-module-qtquick-dialogs \
qml-module-qtquick-controls qml-module-qt-labs-folderlistmodel qml-module-qt-labs-settings \
ros-$ROS_DISTRO-moveit-msgs doxygen swig mscgen ros-$ROS_DISTRO-grid-map \
ros-$ROS_DISTRO-controller-interface ros-$ROS_DISTRO-controller-manager ros-$ROS_DISTRO-aruco-detect \
ros-$ROS_DISTRO-effort-controllers libxml++2.6-dev ros-$ROS_DISTRO-robot-localization libalglib-dev \
ros-$ROS_DISTRO-tf ros-$ROS_DISTRO-interactive-markers ros-$ROS_DISTRO-tf-conversions \
ros-$ROS_DISTRO-robot-state-publisher
```

Update the NST arm experiment
```
cd $HBP/Experiments
git clone https://github.com/Roboy/musc-le_experiment.git
mv musc-le_experiment/* myoarm_nst
```

Compile the custom myorobotics model plugin & its dependencies
```
cd $HBP/GazeboRosPackages/src
git clone https://github.com/CARDSflow/cardsflow_gazebo.git -b nrp-gazebo
git clone https://github.com/Roboy/common_utilities.git
git clone https://github.com/Roboy/roboy_communication.git
git clone https://github.com/Roboy/musc-le_models.git

cd ..
catkin_make
```

Update the model & links
```
cp -a $HBP/GazeboRosPackages/src/musc-le_models/musc_le $HBP/Models
cd $HBP/Models
./create-symlinks.sh
```
Note: if you used the `Myoarm NST 2 DOF` experiment before, delete it from the list of experiments in the NRP GUI and clone anew from the templates list (found at http://localhost:9000/#/esv-private?dev).

### Ubuntu 16
Prerequisites (included in NRP installation):
- ROS kinetic
- Gazebo7

```
sudo apt install -y libxml++2.6-dev ros-kinetic-interactive-markers ros-kinetic-tf-conversions ros-kinetic-eigen-conversions

cd $HBP/Experiments
git clone https://github.com/Roboy/musc-le_experiment.git
mv musc-le_experiment/* myoarm_nst

cd $HBP/GazeboRosPackages/src
git clone https://github.com/CARDSflow/cardsflow_gazebo.git -b gazebo7
git clone https://github.com/Roboy/common_utilities.git
git clone https://github.com/Roboy/roboy_communication.git
git clone https://github.com/Roboy/musc-le_models.git

cd ..
catkin_make

cp -a $HBP/GazeboRosPackages/src/musc-le_models/musc_le $HBP/Models
cd $HBP/Models
./create-symlinks.sh
```

## Controlling the robot
```
rosparam set /cardsflow_xml ~/.gazebo/models/musc_le/cardsflow.xml
```

Navigate to http://localhost:9000/#/esv-private?dev, clone Myoarm 2 DOF experiment and launch it.

Adjust NRP physics properties using the following command: 
```rosservice call /gazebo/set_physics_properties "time_step: 0.001
max_update_rate: 1000.0
gravity: {x: 0.0, y: 0.0, z: -9.8}
ode_config: {auto_disable_bodies: false, sor_pgs_precon_iters: 0, sor_pgs_iters: 50,
  sor_pgs_w: 1.3, sor_pgs_rms_error_tol: 0.0, contact_surface_layer: 0.001, contact_max_correcting_vel: 100.0,
  cfm: 0.0, erp: 0.2, max_contacts: 20}
opensim_config: {integrator: '', integrator_accuracy: 0.0, min_step_size: 0.0, stiffness: 0.0,
  dissipation: 0.0, transitionVelocity: 0.0, staticFriction: 0.0, dynamicFriction: 0.0,
  viscousFriction: 0.0}"
  ```
  
Click play. Now you can control the robot. 

`rostopic list` should show `/roboy/middleware/MotorStatus` and `/roboy/middleware/MotorCommand`. Don't start the experiment yet.

By default all muscles are in the force mode, i.e. controlling the spring compression. Initial setpoint is set to 0.

For example, command 
```
rostopic pub /roboy/middleware/MotorCommand roboy_middleware_msgs/MotorCommand "id: 0
motors: [0,1,2,3]
set_points: [0,30,0,0]"
```
will make the muscle with id 1 contract untill its spring displacement reaches 200 encoder ticks (~25 N). The setpoint will be kept unless a new command arrives. 

Additionally, muscle velocity and muscle length control modes are available.

To monitor the status of the robot's muscles:
```
rostopic echo /roboy/middleware/MotorStatus 
```

## Alternative setup with Gazebo only

```
sudo apt install -y libxml++2.6-dev ros-kinetic-interactive-markers ros-kinetic-tf-conversions ros-kinetic-eigen-conversions
```

```
mdkir ~/musc_ws/src -p
cd ~/musc_ws/src

# for ROS kinetic + Gazebo7
git clone https://github.com/CARDSflow/cardsflow_gazebo.git -b gazebo7
# for ROS melodic + Gazebo9
git clone https://github.com/CARDSflow/cardsflow_gazebo.git

git clone https://github.com/Roboy/common_utilities.git
git clone https://github.com/Roboy/roboy_communication.git
git clone https://github.com/Roboy/musc-le_models.git

cd ..
catkin_make

ln -s ~/musc_ws/src/musc-le_models/musc_le ~/.gazebo/models
```

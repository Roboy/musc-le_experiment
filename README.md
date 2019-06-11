# musc-le_experiment
Neurorobotics platform experiment description featuring 2DOF tendon-driven arm

![alt text](https://github.com/Roboy/musc-le_experiment/blob/master/images/screen.jpg)

## Setup
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

cp -a $HBP/GazeboRosPackages/src/musc-le_models/musc-le $HBP/Models
cd $HBP/Models
./create-symlinks.sh
```

## Controlling the robot
```
rosparam set /cardsflow_xml ~/.gazebo/models/musc-le/cardsflow.xml
```

Navigate to http://localhost:9000/#/esv-private?dev, clone Myoarm 2 DOF experiment and launch it.
`rostopic list` should show `/roboy/middleware/MotorStatus` and `/roboy/middleware/MotorCommand`. Don't start the experiment yet.

Adjust NRP physics properties using the following command: 
`rosservice call /gazebo/set_physics_properties "time_step: 0.001
max_update_rate: 1000.0
gravity: {x: 0.0, y: 0.0, z: -9.8}
ode_config: {auto_disable_bodies: false, sor_pgs_precon_iters: 0, sor_pgs_iters: 50,
  sor_pgs_w: 1.3, sor_pgs_rms_error_tol: 0.0, contact_surface_layer: 0.001, contact_max_correcting_vel: 100.0,
  cfm: 0.0, erp: 0.2, max_contacts: 20}
opensim_config: {integrator: '', integrator_accuracy: 0.0, min_step_size: 0.0, stiffness: 0.0,
  dissipation: 0.0, transitionVelocity: 0.0, staticFriction: 0.0, dynamicFriction: 0.0,
  viscousFriction: 0.0}"
  `
Click play. Now you can control the robot. 

By default all muscles are in the displacement mode, i.e. controlling the spring compression.

For example, command 
```
rostopic pub /roboy/middleware/MotorCommand roboy_middleware_msgs/MotorCommand "id: 0
motors: [0,1,2,3]
set_points: [0,200,0,0]"
```
will make the muscle with id 1 contract untill its spring displacement reaches 200 encoder ticks (~25 N). The setpoint will be kept unless a new command arrives. 

To monitor the status of the robot's muscles:
```
rostopic echo /roboy/middleware/MotorStatus 
```

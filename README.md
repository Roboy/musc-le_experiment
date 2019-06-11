# musc-le_experiment
Neurorobotics platform experiment description featuring 2DOF tendon-driven arm

![alt text](https://github.com/Roboy/musc-le_experiment/blob/master/images/screen.jpg)

## Setup
```
sudo apt install -y libxml++2.6-dev ros-kinetic-interactive-markers ros-kinetic-tf-conversions ros-kinetic-eigen-conversions

cd $HBP/Experiments
git clone https://github.com/Roboy/musc-le_experiment.git
mv musc-le/* myoarm_nst

cd $HBP/GazeboRosPackages/src
git clone https://github.com/CARDSflow/cardsflow_gazebo.git -b gazebo7
git clone https://github.com/Roboy/common_utilities.git
git clone https://github.com/Roboy/roboy_communication.git
git clone https://github.com/Roboy/musc-le_models.git

cd ..
catkin_make

ln -s $HBP/GazeboRosPackages/src/musc-le_models/musc-le $HBP/Models
cd $HBP/Models
./create-symlinks.sh
```

## Controlling the robot
```
rosparam set /cardsflow_xml ~/.gazebo/models/musc-le/cardsflow.xml
```

Navigate to http://localhost:9001/#/esv-private?dev, clone Myoarm 2 DOF experiment and launch it.
`rostopic list` should show `/roboy/middleware/MotorStatus` and `/roboy/middleware/MotorCommand`.

For example, command 
```
rostopic pub /roboy/middleware/MotorCommand roboy_middleware_msgs/MotorCommand "id: 0
motors: [0,1,2,3]
set_points: [0,1000,0,0]"
```
will make the muscle with id 1 relax (unspool) with the velocity 1000 ticks/s (1 tick = 2.54392914e-7 m).

To monitor the status of the robot's muscles:
```
rostopic echo /roboy/middleware/MotorStatus 
```

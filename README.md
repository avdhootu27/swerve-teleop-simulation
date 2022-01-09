# swerve-teleop-simulation

Here we are moving swerve drive using teleop in simulation using [*swerve_steering_controller*](https://github.com/MarkNaeem/ros_controllers/tree/noetic-devel/swerve_steering_controller).

You can find some info about the controller [here](https://discourse.ros.org/t/independent-swerve-steering-controller-for-ros-control/21534).

## Steps to implement teleop in simulation -
1. You need Ubuntu 20.04 and ros noetic.
2. Download ros_controllers package from [here](https://github.com/MarkNaeem/ros_controllers) from noetic-devel branch because this controller is developed in ros-noetic. Solve catkin_make errors (if any).
3. Create your own urdf. I have added a custom made urdf 'swerve_description' in this repository and also added whole cad in .stl format. I extracted that urdf from Autodesk Fusion360. Add your package in same workspace in which you have downloded ros_controller package.
4. Add robotNamespace and robotSimType parameters in gazebo_ros_control plugin (if not present)
    ```
      <gazebo>
        <plugin name="gazebo_ros_control" filename="libgazebo_ros_control.so">
          <robotNamespace>/</robotNamespace>
          <robotSimType>gazebo_ros_control/DefaultRobotHWSim</robotSimType>
        </plugin>
      </gazebo>
    ```
5. Add following gazebo plugin
    ```
      <gazebo>
        <plugin name="ground_truth" filename="libgazebo_ros_p3d.so">
          <alwaysOn>true</alwaysOn>
          <updateRate>50.0</updateRate>
          <gaussianNoise>0.01</gaussianNoise>
          <bodyName>base_footprint</bodyName>
          <topicName>odomw</topicName>
        </plugin>
      <gazebo>
    ```
6. Now inside urdf check the transmissions. The hardwareInterface parameter inside joint of wheels should be VelocityJointInterface and for holders it should be PositionJointInterface.
   
   e.g. for wheel's joint
   ```
    <hardwareInterface>hardware_interface/VelocityJointInterface</hardwareInterface>
   ```
   for holder's joint
   ```
    <hardwareInterface>hardware_interface/PositionJointInterface</hardwareInterface>
   ```
   If you are having any doubt, check our [swerve.trans](swerve_description/urdf/swerve.trans) file.
7. Make the changes in launch file 'robot.launch' inside swerve_steering_controller/test/launch according to your urdf. We have added our [robot.launch](robot.launch) file too.
8. Change the parameters in 'ros_controllers.yaml' inside swerve_steering_controller/test/config. We have added [that file](ros_controllers.yaml) too. 
9. Now you are ready to launch 'robot.launch' from swerve_steering_controller/test/launch.
10. In order to run teleop_twist_keyboard you need to change topic name inside teleop_twist_keyboard.py from 'cmd_vel' to 'swerve_steering_controller/cmd_vel'. We have done that and added teleop_twist_keyboard.py file [here](swerve_description/src/).
11. Now, first launch 'robot.launch' then run 'teleop_twist_keyboard.py' and you can see your swerve drive moving.

The video recording of our execution is available [here](https://drive.google.com/file/d/1kYHKWMpJTP2jgG3VVFELKF4HQYFddLgw/view?usp=sharing).

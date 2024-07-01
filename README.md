# Software-trials_task_1

This repo is for task 1 of software trials for team Deimos.

Summary of what we do in this repo :
1. We launch a custom gazebo world with a turtlebot.
2. We create a map of that gazebo world by teleoperating turtlebot3_burger in that world and save it using map_server node.
3. We do autonomous navigation of turtlebot3_burger on that map using DWA planner in ROS and visualize LIDAR sensor data.

Resources used : ROS Noetic, turtlebot3 robot(burger) , ROS navigation stack, Gazebo, RViz.

Here are the step to follow to achieve the goal : 

1. Create our workspace folder named catkin_ws and initialize it using the following commands :

       mkdir -p ~/catkin_ws/src

       cd ~/catkin_ws/src

       catkin_init_workspace


2. Clone necessary turtlebot3 libraries into our workspace folder :

       git clone https://github.com/ROBOTIS-GIT/turtlebot3.git

       git clone https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git

   Compile the workspace using : catkin_make


3. We setup and launch our custom gazebo world with turtlebot3 in it.

   https://github.com/mlherd/Dataset-of-Gazebo-Worlds-Models-and-Maps.git

   From the above repo, download the hospital.world and model zip files from 'hospital' folder.

   Extract all these zip files to the following folder(create it too) : /home/ashutosh/.gazebo/models/hospital/models/

   Create a folder name my_gazebo_world in catkin_ws folder :
   
       cd ~/catkin_ws/src
   
       mkdir my_gazebo_world

       cd ~/catkin_ws/src/

       mkdir worlds

       mkdir launch

   Copy the hospital.world file from Downloads to the worlds folder created above.

   Go to the launch folder : cd ~/catkin_ws/my_gazebo_world/launch and create a launch file 'turtlebot3_slam.launch' with the following content :

       <launch>
       
       <!-- Launch the Gazebo simulation with your custom world -->
       <include file="$(find gazebo_ros)/launch/empty_world.launch">
       <arg name="world_name" value="$(find my_gazebo_world)/worlds/hospital.world"/>
       </include>
         <!-- Spawn the TurtleBot3 model with a specific initial pose -->
         <param name="robot_description" command="$(find xacro)/xacro '$(find turtlebot3_description)/urdf/turtlebot3_burger.urdf.xacro'" />
         <node name="spawn_urdf" pkg="gazebo_ros" type="spawn_model" args="-param robot_description -urdf -model turtlebot3_burger -x 0 -y 0 -z 0.1 -R 0 -P 0 -Y 0" output="screen"/>

       </launch>

    
     Now, source the ROS enviroment and build your workspace using catkin_make command.

     Now launch the gazebo world with turtlebot3 with the following commands :

       export GAZEBO_MODEL_PATH=/home/ashutosh/.gazebo/models/hospital/models/
   
       export TURTLEBOT3_MODEL=burger

       roslaunch my_gazebo_world turtlebot3_slam.launch

   
5. Creating a map of our custom launched gazebo world using SLAM (gmapping) by teleoperating turtlebot3 (could be done autonomously using frontier exploration package)

      Open a new terminal and launch following command :
   
       roslaunch turtlebot3_slam turtlebot3_slam.launch slam_methods:=gmapping

      Now, launch teleoperation on turtlebot3 :
   
       roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch

      Teleoperate and see the map building on RViz and save the map when done using the following command :
   
       rosrun map_server map_saver -f ~/map

      This will save map.yaml and map.pgm in home directory
   

7.  Autonoumous navigation on the map using DWA planner (local planner in ROS)
      
       Run the following commands :

         roslaunch turtlebot3_gazebo turtlebot3_empty_world.launch
       
         roslaunch turtlebot3_navigation turtlebot3_navigation.launch map_file:=$HOME/map.yaml

         roslaunch turtlebot3_navigation move_base.launch   

       Make sure that the move_base launch file contains the folloeing parameter to use DWA planner for navigation :
         
         <param name="base_local_planner" value="dwa_local_planner/DWAPlannerROS"/>

         
       Now using RVIZ, set 2DNavgoal to autonomously navigate from one point to another.

       To visualise LIDAR sensor data :

       Add LaserScan option in RViz and set topic to '/scan'. You can also add 'RobotModel', 'Tf', 'Map'

   
   
   


   






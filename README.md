# Clearpath-Husky-Robot-Upgrade-ROS-1-to-ROS-2-Foxy
This tutorial is to show you how to update your Clearpath Husky robot from ROS 1 to ROS 2 Foxy. 

Upgrading Clearpath Husky robot to ROS 2 and Ubuntu 20.04

This tutorial is for upgrading your Clearpath Husky robot to ROS 2 and Ubuntu 20.04. We installed their official Noetic Husky ISO image first, then installed the ROS 2 Foxy version. For installing the Husky packages for ROS 2, currently the best way is installing from source (date: 05-18-2022). Considering the ROS 2 is compatible with ROS 1, we keep both in our Husky robot. Of course, you can also install ROS 2 only. Here are our detailed steps and some common mistakes we met during our upgrade; we wish this can help you build your ROS 2 quickly. You can also find correlated instructions at the following links:
1.	https://clearpathrobotics.com/assets/guides/foxy/husky/HuskyInstallSoftware.html
2.	http://www.clearpathrobotics.com/assets/guides/noetic/husky/

The Husky robot comes pre-configured with ROS 1 or ROS 2. You only need to follow the necessary steps based on your own requirements. 

ROS 1 part: 
1. Download the appropriate Noetic Husky ISO image from https://packages.clearpathrobotics.com/stable/images/.
For us, we used https://packages.clearpathrobotics.com/stable/images/0.5.12/
2. Copy the image to a USB drive using unetbootin, rufus, balena etcher, or a similar program. For example:
sudo unetbootin isofile="clearpath-universal-noetic-amd64-0.4.17.iso"
3. Connect your Husky robot on-board PC to wired internet access, a keyboard, and a monitor. Make sure that the Husky robot is connected to shore power, or the Husky battery is fully charged.
4. Boot your robot PC from the USB drive, and let installer work its magic. Choose Husky platform. It probably will ask you to install packages related to sensors, you can click Tab key to skip that if you are not sure if you want to install or not. If asked for a partitioning method, choose Guided - use entire disk and set up LVM.
5. The setup process will be automated and may take a long time depending on the speed of your internet connection.
6. Once the setup process is complete, the Husky on-board PC will turn off. Please unplug the USB drive and turn the Husky on-board PC back on.
7. On first boot, the username will be administrator and the password will be clearpath.
8. To setup a factory-standard Husky robot, ensure all your peripherals are plugged in, and run the following command: rosrun husky_bringup install
9. The install script will configure a ros upstart service, that will bring up the base Husky launchfiles on boot. The script will also detect any standard peripherals (IMU, GPS, etc.) you have installed, and add them the service.
10. Run sudo systemctl daemon-reload && sudo systemctl start ros, the COMM light on your Husky should go from red to green.
11. Your husky should now be accepting commands from your joystick. The service will automatically start each time you boot your Husky’s PC. By default, the Husky use the PS4 controller for teleoperation and ignore the F710. To enable the F710 to control the robot, run sudo nano /etc/ros/setup.bash and add the following line to the middle of the file, under the six # characters: 
######
export HUSKY_LOGITECH=1

Then restart ROS by running sudo systemctl restart ros or rebooting the robot. 

Installing ROS 2
1. Considering ROS 2 is compatible with ROS 1, we installed ROS 2 directly without deleting ROS 1.
2. To install ROS 2, we recommend the Debian packages method for Ubuntu 20.04.
https://docs.ros.org/en/foxy/Installation/Ubuntu-Install-Debians.html
3. In the above link, you can probably skip the following steps and install ROS 2 packages directly because it seems that these settings have already been done if you installed the Clearpath .iso image files:
	Set locale
	Setup sources
4. Install ROS 2 packages
sudo apt update
sudo apt install ros-foxy-ros-base 
5.  After you successfully install ROS 2 Foxy, add source /opt/ros/foxy/setup.bash to your /etc/ros/setup.bash file and comment source /opt/ros/noetic/setup.bash (You may have a different version instead of noetic). Make sure you only keep ROS 2. You can reboot your robot.
6. You may realize that your COMM light turns back to red, it is because we need to install the Husky packages for ROS 2. For installing the Husky packages for ROS 2, currently the best way is installing from source (date: 05-18-2022).
7.  Create a workspace directory. In terminal, run:
mkdir -p ~/husky_ws/src
8. Clone the Husky repositories into your workspace directory. In terminal, run:
cd ~/husky_ws/src
git clone -b foxy-devel https://github.com/husky/husky.git
cd ..
9. Install additional dependencies. In terminal, run:
rosdep install --from-paths src --ignore-src --rosdistro=$ROS_DISTRO -y
10. During the step 9, you may encounter packages missing issues. This is because some packages are not officially released yet, you can probably find the right packages at http://repo.ros2.org/status_page/ros_foxy_default.html
For us, we installed the following two packages 
1.	) imu_filter_madgwick
https://github.com/CCNYRoboticsLab/imu_tools/tree/foxy
2.	) interactive_maker_twist_Server
https://github.com/ros-visualization/interactive_marker_twist_server/tree/foxy-devel
11. Once you install the missed packages, you should be able to see #All required rosdeps installed successfully
12. Build your workspace, in husky_ws directory, run
colcon build
13. You can now source your workspace to make use of the packages you just built. In terminal, run:
source install/setup.sh (it might also be setup.bash). 
14. You can also add it to your ~/.bashrc file so that you do not need to source it every time.
15. Run ros2 launch husky_base base.launch.py (Do not use ros2 run husky_bringup install)
16. Your COMM light will turns to green. You may find that your joystick can still not control your Husky robot. That issue may come to the following files:

~/dev_ws/src/husky/husky_control/config$ sudo nano teleop_logitech.yaml 

![image](https://user-images.githubusercontent.com/106189785/170120113-100f794f-0cd9-4555-840d-a216655a5ac1.png)


All you need to do is to change the device_name to dev. (The Clearpath team may fix this bug in the future, so no worries).
Done! You Husky robot is ready to go with ROS 2 Foxy!

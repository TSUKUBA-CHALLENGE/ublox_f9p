# ROS driver for ublox ZED-F9P receiver

This is for the ArduSimple ZED-F9P boards. (Mainly affects which port on the board is being used.) I am connecting the F9P board to a Linux laptop with USB cable. (MovingBaseline requires connecting a single wire between two receiver boards.)

I copied the ublox ROS driver from https://github.com/bao-eng/ublox

Looking at my initial commit comments I really didn't change anything to get it to work.

I added ROS topic RTCM correction support so the board produces an RTK fix. (The correction data is sent through the ROS driver node.) The RTCM server is here: https://github.com/ros-agriculture/ntrip_ros A similar server (may be better than mine) is here: https://github.com/dayjaby/ntrip_ros

I added launch files and yaml files to allow running two F9P boards at the same time. This allows operation in MovingBaseline mode. 

**I need to add the uBlox config files which get manually loaded on to the boards through uBlox u-center software.**

Below is original README from bao-eng that I started with:

=======================================================================

# ROS driver for ublox ZED-F9P receiver

Just quick hardcode to publish some UBX messages to ROS.
Disabled configuration of the receiver via yaml. Reciever should be configured via u-center software.
Hardcoded to work as HPG Rover device.
NavRELPOSNED.msg updated to match u-blox 9 protocol version 27.1

## RTCM Messages
It may also require this package: https://github.com/tilk/rtcm_msgs

## Options

zed-f9p.yaml (only for seting up device connection and published messages)

## Launch

```roslaunch ublox_gps ublox_zed-f9p.launch```

## catkin_make error
以下のエラーがcatkin_makeをして出た

```bash
CMake Error at /opt/ros/melodic/share/catkin/cmake/catkinConfig.cmake:83 (find_package):
  Could not find a package configuration file provided by "rtcm_msgs" with
  any of the following names:

    rtcm_msgsConfig.cmake
    rtcm_msgs-config.cmake

  Add the installation prefix of "rtcm_msgs" to CMAKE_PREFIX_PATH or set
  "rtcm_msgs_DIR" to a directory containing one of the above files.  If
  "rtcm_msgs" provides a separate development package or SDK, be sure it has
  been installed.
Call Stack (most recent call first):
  ublox/ublox_gps/CMakeLists.txt:3 (find_package)


-- Configuring incomplete, errors occurred!
See also "/home/move/catkin_ws/build/CMakeFiles/CMakeOutput.log".
See also "/home/move/catkin_ws/build/CMakeFiles/CMakeError.log".
Invoking "cmake" failed
```

しかし以下のコマンドを行ってからcatkin_makeを行うと成功する

```bash
sudo apt-get install ros-melodic-rtcm-msgs
```

status.status = 2 は、基準局を基づいて解析Fix解が得られています。
2ではなく、0の状態。

int8 STATUS_NO_FIX =  -1        # unable to fix position
int8 STATUS_FIX =      0        # unaugmented fix
int8 STATUS_SBAS_FIX = 1        # with satellite-based augmentation
int8 STATUS_GBAS_FIX = 2        # with ground-based augmentation

なので、stateが-1のときにはGPS navigationをやめて、それ以外のときにはGPS navigationを実行する

  

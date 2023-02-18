# cartographer_with_rslidar_and_scout

Use cartographer for mapping with recorded points cloud by rslidar

## Clone

This repo is full of submodules, be sure to clone it with ```--recurse-submodules``` option.

```sh
mkdir carto_ws && cd carto_ws
mkdir src && cd src
git clone --recurse-submodules https://github.com/Steve-Mr/cartographer_with_rslidar_and_scout.git .
```

## Build And Install Cartographer

The Building and Installation process bascially identical to [official doc](https://google-cartographer-ros.readthedocs.io/en/latest/compilation.html), but with some modification to make it work on Ubuntu 20.04 & Noetic.

Install needed tools:

```sh
sudo apt-get update
sudo apt-get install -y python3-wstool python3-rosdep ninja-build
```

Then install cartographer_ros' dependencies.

```sh
sudo rosdep init # If your have already executed it there will be an error, just ignore it.
rosdep update
rosdep install --from-paths src --ignore-src --rosdistro=${ROS_DISTRO} -y
```

Then we need to build and install abseil befor run catkin_make on the workspace.

### Build and Install Abseil

First remove any installed ROS abseil-cpp although there is no "ros-noetic-abseil-cpp"

```sh
sudo apt-get remove ros-${ROS_DISTRO}-abseil-cpp
```

Then clone the Abseil repo.

```sh
git clone https://github.com/abseil/abseil-cpp.git
cd abseil-cpp
git checkout 215105818dfde3174fe799600bb0f3cae233d0bf # version 20211102.0 which is specified in cartographer/scripts/install_abseil.sh
```

Then we should follow the [official doc](https://abseil.io/docs/cpp/quickstart-cmake) but compile it with C++17 and -fPIC:

```sh
mkdir build && cd build
cmake -DABSL_BUILD_TESTING=ON -DABSL_USE_GOOGLETEST_HEAD=ON -DCMAKE_POSITION_INDEPENDENT_CODE=ON -DCMAKE_CXX_STANDARD=17 ..
cmake --build . --target all
sudo make install
```

### Last Step

Goto your workspace directory like ```~/carto_ws``` 

```sh
catkin_make_isolated --install --use-ninja
```

Beware that after you built the workspace with ```catkin_make_isolated```, you need to source your environment by running ```source install_isolated/setup.bash``` first, and you need to run the build command everytime after you make any changes even edit launch file.

## Run with your own bag

First source your environment 

```sh
source install_isolated/setup.bash
```

Then run demo_my_robot.launch with recorded bag from rslidar

```sh
roslaunch cartographer_ros demo_my_robot.launch bag_filename:='<your/bag/file/path>'
```

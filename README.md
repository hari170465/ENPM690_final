# ENPM690_final
This Repository has the code for the final project of ENPM690 - Robot Learning

## Description
The project is about training two robots using reinforcement learning evade and pursue each other in a 3D environment. The 3D environment is created using ROS2 foxy and Gazebo. The robots are trained using the DQN algorithm. The project is implemented using the PyTorch library. The robot model used is turtlebot3.
## Demonstration Videos
[![Algorithm Demo 2](https://img.youtube.com/vi/MK8Tr_1hM-Q/0.jpg)](https://youtu.be/MK8Tr_1hM-Q)
## Docker Installation
Use this only if your system has an NVIDIA GPU and you have Docker installed on your system.
Install Docker on your system by following the instructions on the official Docker website: https://docs.docker.com/get-docker/

to install nvidia container toolkit follow the instructions on the official NVIDIA website: https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/install-guide.html

or

Follow The instructions below

First, setup the package repository and the GPG key:
```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) \
      && curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
      && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
            sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
            sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

Install the nvidia-container-toolkit:
```
sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
```

configure docker daemon:
```
sudo nvidia-ctk runtime configure --runtime=docker
```

Restart the docker daemon:
```
sudo systemctl restart docker
```
check if the nvidia runtime is enabled:
```
sudo docker run --rm --runtime=nvidia --gpus all nvidia/cuda:11.6.2-base-ubuntu20.04 nvidia-smi
```


Clone the git repository:
```
git clone https://github.com/Nitish05/ENPM690_final.git
```
cd into the repository:
```
cd ENPM690_final
```

Build the docker image:
```
docker build -t enpm690_final .
```
 Give the docker container access to the X server(for display permissions):
```
xhost +local:docker
```

Run the docker container: replace /PATH/TO/REPO/ENPM690_final with the path to the repository on your system
```
docker run -it --gpus all --privileged   --env NVIDIA_VISIBLE_DEVICES=all   --env NVIDIA_DRIVER_CAPABILITIES=all  --env DISPLAY=${DISPLAY}  --env QT_X11_NO_MITSHM=1  --volume /tmp/.X11-unix:/tmp/.X11-unix -v /PATH/TO/REPO/ENPM690_final:/home/ENPM690_final   --network host enpm690_final
```
## Ubuntu Installation
unzip the file you will get the following folders:
```
ENPM690_final
- src
```
cd into the src folder and run the following commands:
```
colcon build
source install/setup.bash
```
befor running the code make sure paste this in the ~/.bashrc file:
```
source /opt/ros/foxy/setup.bash

export ROS_DOMAIN_ID=1

WORKSPACE_DIR=~/ENMP690_final
export DRLNAV_BASE_PATH=$WORKSPACE_DIR

# Source the workspace
source $WORKSPACE_DIR/install/setup.bash

# Allow gazebo to find our turtlebot3 models
export GAZEBO_MODEL_PATH=$GAZEBO_MODEL_PATH:$WORKSPACE_DIR/src/turtlebot3_simulations/turtlebot3_gazebo/models

# Select which turtlebot3 model we will be using (default: burger, waffle, waffle_pi)
export TURTLEBOT3_MODEL=burger

# Allow Gazebo to find the plugin for moving the obstacles
export GAZEBO_PLUGIN_PATH=$GAZEBO_PLUGIN_PATH:$WORKSPACE_DIR/src/turtlebot3_simulations/turtlebot3_gazebo/models/turtlebot3
```
### Prerequisites
List any software, libraries, or hardware needed to run this project.
 Lists : 
 - **Ubuntu 20.04 LTS (Focal Fossa)** - https://releases.ubuntu.com/20.04/
 - **ROS2 Foxy Fitzroy** - https://docs.ros.org/en/foxy/index.html
 - **Gazebo 11** - https://gazebosim.org/home
 - **PyTorch** - https://pytorch.org/

## Usage

### Training
To train the robots, run the following commands after sourcing the workspace:

To train the EVADER:\
1st terminal: This command will launch the evader and pursuer robots in the environment. 
```
ros2 launch turtlebot3_multi_robot evader_and_pursuer.launch.py enable_drive:=False
```
2nd terminal: This command will launch the evader environment to stream the data to the agent.
```
ros2 run DQN_evader environment
```
3rd terminal: This command will launch the evader goal publisher to. The goal publisher will publish the goal for the evader robot to reach
```
ros2 run DQN_evader gazebo_goals
```
4th terminal: This command will train the evader robot using the DQN algorithm.
```
ros2 run DQN_evader train_agent dqn
```
To train the PURSUER:\
5th terminal: This command will launch the pursuer environment to stream the data to the agent.
```
ros2 run DQN_pursuer environment
```
6th terminal: This command will launch the pursuer goal publisher to. The goal publisher will publish the evader's pose for the pursuer robot to reach
```
ros2 run DQN_pursuer gazebo_goals
```
7th terminal: This command will train the pursuer robot using the DQN algorithm.
```
ros2 run DQN_pursuer train_agent dqn
```
### Loading the trained model
There is two separate models for the evader and pursuer. Firstly, copy the model 'dqn_1_stage_4' from the examples folder and paste it into an folder in your PC's name inside the models folder this is for the evader. Do the same for the pursuer this time the model is named 'dqn_0_stage_4' To load the trained model, run the following commands after sourcing the workspace:
```
ENPM690_final
    - src
        - DQN_evader
            - models
                - examples
                    - dqn_1_stage_4
                - your_PC's_name
                    - dqn_1_stage_4
        - DQN_pursuer
            - models
                - examples
                    - dqn_0_stage_4
                - your_PC's_name
                    - dqn_0_stage_4

```
```
ros2 run DQN_evader train_agent dqn 'dqn_1_stage_4' 5000
```
```
ros2 run DQN_pursuer train_agent dqn 'dqn_0_stage_4' 3500
```
### Testing
To test the trained models, run the following commands after sourcing the workspace:
```
ros2 run DQN_evader test_agent dqn 'dqn_1_stage_4' 5000
```
```
ros2 run DQN_pursuer test_agent dqn 'dqn_0_stage_4' 3500
```
## Video:
[Training Video](https://drive.google.com/file/d/1tZYc_06mt21np6Y6LVzP8JrUPDd7xHvM/view)
[Training and Testing](https://drive.google.com/file/d/1xSXV4YkElqgCyZ_0qVD5Ol1GYBYdgInE/view)
#### Note:
The number of episodes can be changed by changing the number at the end of the command.

## References
- ENPM690 - Robot Learning course material
- https://github.com/tomasvr/turtlebot3_drlnav
- https://github.com/arshadlab/turtlebot3_multi_robot
- https://github.com/MBaranPeker/Pursuit-Evasion-Game-with-Deep-Reinforcement-Learning-in-an-environment-with-an-obstacle

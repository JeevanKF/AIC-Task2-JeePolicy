# AI for Industry Challenge
## Custom JeePolicy

## I. About

This repository contains my implementation of a custom **JeePolicy** for the **AI for Industry Challenge (AIC)** robotics evaluation environment. The objective of this project is to autonomously control an industrial robotic arm to perform a cable insertion task using ROS 2 and computer-controlled motion planning.

The project was developed as part of my Robotics Internship, where I learned to work with the complete AIC toolkit, configure the simulation environment, and implement a custom policy for robotic manipulation.

The simulation uses a **Universal Robots UR5e** robotic arm equipped with a gripper to insert an **SC cable connector** into its corresponding port on a simulated task board. The environment is powered by **Gazebo Harmonic**, while the robot software is built on **ROS 2 Kilted Kaiju**, Docker, and the AIC evaluation framework.

My implementation modifies the provided example policy by creating a custom **JeePolicy**, compiling it within the ROS 2 workspace, and successfully executing all evaluation trials in simulation. The robot autonomously detects the task, approaches the connector, aligns the cable with the target port, performs the insertion sequence, and completes the evaluation process.

This repository also provides step-by-step setup instructions, build commands, execution workflow, troubleshooting tips, and demonstration videos to help others reproduce the simulation environment and run the project successfully.
## II. MY Contribution
Implemented and evaluated a custom ROS2 JeePolicy derived from the WaveArm baseline for autonomous cable insertion in the AI for Industry Challenge simulation

## III. Visual

### Simulation Screenshots
#### i. RViz Environment Overview

<img width="958" height="543" alt="Env" src="https://github.com/user-attachments/assets/9b53ac20-34e1-4c9d-8f13-0790b4a414ef" />

The RViz environment displaying the UR5e robotic manipulator, TF coordinate frames, cable model, and task board used during the cable insertion challenge.

#### ii. Camera View of the Task Board

<img width="322" height="287" alt="Port" src="https://github.com/user-attachments/assets/a87d2328-464e-4d18-b42d-f8b636391742" />

RGB camera view showing the simulated cable connector and target insertion port used by the robot during execution.

#### iii. Robot Arm Overview

<img width="742" height="522" alt="Riv Arm" src="https://github.com/user-attachments/assets/a71fdfed-30f4-4972-a830-3648e8974458" />

The UR5e robotic manipulator initialized in the RViz environment. This view displays the robot model together with the ROS2 TF coordinate frames used for pose estimation and motion planning.

#### iv. Cable Insertion Process

<img width="587" height="475" alt="Gazebo Arm" src="https://github.com/user-attachments/assets/b403b512-bae7-4b15-aa2a-3e5c9b450e25" />

The UR5e robot executing the custom JeePolicy while aligning the cable connector and performing the insertion task inside the simulated environment.

### Demonstration Video
The complete simulation, including Gazebo, RViz visualization, robot motion, and successful cable insertion using the custom JeePolicy, is available below.

[![Watch the Demo](assets/demo_thumbnail.png)](https://youtu.be/qBZrdnfjYKI)

▶ Click the image above to watch the full demonstration.

### 1. Gazebo Simulation – Cable Insertion Demo

https://github.com/user-attachments/assets/f1dcc82c-1766-4cc4-9bbb-56e5b271ea6a
Shows the UR5e robot performing the cable insertion task inside the Gazebo simulation.

## IV. System Requirements

Ubuntu 24.04,
Docker,
ROS2 Kilted,
Pixi,
Git.

## V. Installation & Running the Simulation

#### 1. Clone the Repository

```bash
git clone https://github.com/JeevanKF/AIC-Task2-JeePolicy.git
cd AIC-Task2-JeePolicy
```
#### 2. Start the Docker Container

```bash
docker start aic_eval
```
Verify that the container is running:

```bash
docker ps
```
Expected output:
```
aic_eval
```
#### 3. Open the Docker Container

```bash
docker exec -it aic_eval bash
```
#### 4. Go to the Workspace

```bash
cd /home/jee/ws_aic
```

#### 5. Source ROS2 and Workspace

```bash
source /opt/ros/kilted/setup.bash
source install/setup.bash
```

#### 6. Build the Required Packages (First Time or After Modifications)

Build the policy package:

```bash
colcon build --packages-select aic_example_policies
```

Build the model package:

```bash
colcon build --packages-select aic_model
```

Reload the workspace:

```bash
source install/setup.bash
```

#### Running the Simulation

The simulation requires **three terminals**.

#### Terminal 1 — Zenoh Router

```bash
docker exec -it aic_eval bash

cd /home/jee/ws_aic

source /opt/ros/kilted/setup.bash
source install/setup.bash

ros2 run rmw_zenoh_cpp rmw_zenohd
```
Leave this terminal running.


#### Terminal 2 — JeePolicy

```bash
docker exec -it aic_eval bash

cd /home/jee/ws_aic

source /opt/ros/kilted/setup.bash
source install/setup.bash

ros2 run aic_model aic_model \
  --ros-args \
  -p use_sim_time:=true \
  -p policy:=aic_example_policies.ros.JeePolicy
```
Leave this terminal running.


#### Terminal 3 — Gazebo + Evaluation Engine

```bash
docker exec -it aic_eval bash

cd /home/jee/ws_aic

source /opt/ros/kilted/setup.bash
source install/setup.bash

AIC_RESULTS_DIR=~/aic_results \
ros2 launch aic_bringup aic_gz_bringup.launch.py \
ground_truth:=true \
start_aic_engine:=true
```

Gazebo will launch automatically.

The robot will:

* Spawn into the environment
* Receive the cable insertion task
* Execute JeePolicy
* Insert the cable
* Complete all evaluation trials
* Save the results in:

```
~/aic_results/
```
#### Results

The final evaluation score can be found in:

```
~/aic_results/scoring.yaml
```

The ROS bag recordings are stored in:

```
~/aic_results/
```

#### Restarting the Simulation

After a completed run:

1. Press **Ctrl+C** in all three terminals.
2. Exit the Docker container if desired.
3. Restart the container:

```bash
docker restart aic_eval
```

4. Repeat the Terminal 1 → Terminal 3 steps above.


## VI. Run Zenoh
```
~/aic_results/
ros2 run rmw_zenoh_cpp rmw_zenohd
```
## VII. Launch Gazebo Simulation

Open a **new terminal** and enter the Docker container:

```bash
docker exec -it aic_eval bash
```

Navigate to the workspace:

```bash
cd /home/jee/ws_aic
```

Source the ROS 2 environment and workspace:

```bash
source /opt/ros/kilted/setup.bash
source install/setup.bash
```

Launch the Gazebo simulation together with the AIC evaluation engine:

```bash
AIC_RESULTS_DIR=~/aic_results \
ros2 launch aic_bringup aic_gz_bringup.launch.py \
ground_truth:=true \
start_aic_engine:=true
```

After a few seconds, Gazebo will open automatically. The simulation environment, robot, cable, and task board will be spawned, and the evaluation engine will begin preparing the task.

## VIII. Run the Custom JeePolicy
This custom policy is based on the provided WaveArm baseline and was modified to improve cable alignment during insertion.

Main features:

TF-based pose estimation
Quaternion interpolation (SLERP)
XY error integration
Smooth Cartesian interpolation
Gradual Z-axis insertion
Force-stable insertion
Automatic task execution

Open another terminal and enter the Docker container:

```bash
docker exec -it aic_eval bash
```

Navigate to the workspace:

```bash
cd /home/jee/ws_aic
```

Source the ROS 2 environment and workspace:

```bash
source /opt/ros/kilted/setup.bash
source install/setup.bash
```

Start the custom JeePolicy node:

```bash
ros2 run aic_model aic_model \
  --ros-args \
  -p use_sim_time:=true \
  -p policy:=aic_example_policies.ros.JeePolicy
```

Once the Gazebo simulation is running, the robot will receive the cable insertion task, execute the custom JeePolicy, align the connector with the target port, perform the insertion, and complete the evaluation automatically.

The final evaluation score and ROS bag recordings will be saved in:

```text
~/aic_results/
```


## IX. Expected Workflow

1. Launch Gazebo simulation.
2. Spawn the UR5e robot.
3. Receive the cable insertion task.
4. Execute the custom JeePolicy.
5. Align the cable connector.
6. Insert the cable.
7. Complete evaluation.
8. Save results to ~/aic_results.
Robot -> moves -> aligns cable -> plugs cable


## X. Troubleshooting

During the setup and execution of the AI for Industry Challenge environment, several common issues were encountered and resolved. The following solutions may help if you experience similar problems.


#### 1. `ros2: command not found`

**Error**

```bash
ros2: command not found
```

**Solution**

The ROS 2 environment was not sourced. Run:

```bash
source /opt/ros/kilted/setup.bash
source /home/jee/ws_aic/install/setup.bash
```

#### 2. `Package 'aic_model' not found`

**Error**

```bash
Package 'aic_model' not found
```

**Solution**

Build the package and reload the workspace.

```bash
cd /home/jee/ws_aic

colcon build --packages-select aic_model

source install/setup.bash
```

#### 3. `No module named 'aic_example_policies'`

**Error**

```bash
ModuleNotFoundError: No module named 'aic_example_policies'
```

**Solution**

The custom policy package was not built.

```bash
colcon build --packages-select aic_example_policies

source install/setup.bash
```

#### 4. Gazebo GUI does not open

**Symptoms**

* Gazebo starts but no GUI appears.
* Terminal keeps running without displaying the simulation.

**Solution**

Allow Docker to access the X11 display.

On the host machine:

```bash
xhost +SI:localuser:root
```

Inside Docker:

```bash
export DISPLAY=:1
```

Verify:

```bash
echo $DISPLAY
```

#### 5. Zenoh Router Error

**Error**

```bash
Address already in use
```

**Cause**

Another Zenoh router instance is already running.

**Solution**

Restart the Docker container or stop the existing Zenoh router before launching a new one.

```bash
docker restart aic_eval
```


#### 6. Docker Container Not Running

**Error**

```bash
Error response from daemon:
container is not running
```

**Solution**

Start the container before entering it.

```bash
docker start aic_eval

docker exec -it aic_eval bash
```

#### 7. Permission Denied while executing JeePolicy.py

**Error**

```bash
Permission denied
```

**Cause**

Python policy files should not be executed directly.

**Solution**

Run the policy using ROS 2.

```bash
ros2 run aic_model aic_model \
  --ros-args \
  -p use_sim_time:=true \
  -p policy:=aic_example_policies.ros.JeePolicy
```

#### 8. Git "Dubious Ownership" Error

**Error**

```bash
fatal: detected dubious ownership in repository
```

**Solution**

Mark the repository as safe.

```bash
git config --global --add safe.directory /home/jee/ws_aic/src/aic
```

#### 9. Git Push Rejected

**Error**

```bash
failed to push some refs
```

**Cause**

The remote repository contains commits that are not available locally.

**Solution**

Pull the latest changes or push to a new repository if creating a personal implementation.

#### 10. Simulation Completed Successfully

A successful execution should produce:

* Gazebo launches successfully.
* UR5e robot appears in the simulation.
* Robot receives the cable insertion task.
* Custom JeePolicy executes automatically.
* Cable insertion is completed.
* Evaluation finishes successfully.
* Results are saved in:

```text
~/aic_results/
```

The evaluation summary and final score can be found in:

```text
~/aic_results/scoring.yaml
```
## XI. Results

Trials Passed: 3 / 3
Score: 136.24


| Trial     | Status        |      Score |
| --------- | ------------- | ---------: |
| Trial 1   | Success       |      64.29 |
| Trial 2   | Success       |      65.72 |
| Trial 3   | Success       |       6.24 |
| **Total** | **Completed** | **136.24** |

Evaluation Summary

| Trial	  | Status	   | Result                               |
| -------- |  --------  |  ----------------------------------  |
|Trial 1   | Success    |Partial cable insertion               |
|Trial 2	  | Success	   |Partial cable insertion               |
|Trial 3	  | Completed  |Task executed with evaluation metrics |

## XII. Future Improvements

• Vision-based cable localization
• Reinforcement Learning policy
• MuJoCo implementation
• Force-feedback optimization
• Improved trajectory planning

## XIII. References

Intrinsic AI for Industry Challenge
ROS2 Kilted Kaiju
Gazebo Harmonic
Docker

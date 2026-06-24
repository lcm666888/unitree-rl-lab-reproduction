# Unitree G1 Reinforcement Learning & Sim2Sim Project

This repository documents my work on reinforcement learning-based locomotion control for Unitree robots (G1) using Isaac Sim, Isaac Lab, RSL-RL, and MuJoCo, including training, debugging, policy deployment, and sim-to-sim transfer.

The goal of this project is to build and analyze robust locomotion policies and understand real-world failure modes such as drift, asymmetric turning, and unstable gait behaviors.

---

# Key Contributions

## 1. Reinforcement Learning Training (Isaac Lab + RSL-RL)

Trained locomotion policies for Unitree G1 in simulation  
Used PPO-based reinforcement learning pipeline (RSL-RL)  
Successfully completed long-horizon training runs (up to 10k iterations)  
Monitored reward decomposition and curriculum progression  

Key observations during training:
- Velocity tracking improved steadily over curriculum levels  
- Gait stability increased as action smoothing improved  
- Joint deviation and action-rate penalties strongly affected balance behavior  
- Turning asymmetry (left/right drift) observed and analyzed  

---

## 2. Reward Design & Behavior Analysis

Analyzed and iteratively adjusted reward terms to improve locomotion stability:

- Velocity tracking (linear + angular)  
- Gait regularization  
- Joint velocity / acceleration penalties  
- Foot contact and clearance constraints  
- Orientation stability (base tilt / roll / pitch)  

Key improvements addressed:
- Reduced “arm swinging noise” during locomotion  
- Reduced diagonal drift during straight walking  
- Improved turning consistency (left vs right asymmetry reduction)  

---

## 3. Sim-to-Sim Deployment (Isaac →  MuJoCo)

Successfully deployed trained policies from Isaac Lab to MuJoCo simulation  
Verified consistency of locomotion behavior across simulation engines  
Used ONNX exported policy for inference  

Implemented FSM-based controller for state transitions:
- Passive →  FixStand →  Velocity control →  RL policy execution  

---

## 4. Keyboard / Control System Integration

Implemented keyboard-based velocity command interface:

- W / A / S / D →  planar velocity control  
- Q / E →  yaw rotation  

Integrated into observation pipeline as:

```
keyboard_velocity_commands
```

This replaces joystick dependency and enables direct keyboard control during deployment.

---

## 5. Debugging & System-Level Fixes

Resolved multiple real-world robotics engineering issues:

- Missing checkpoint loading errors during evaluation  
- ONNX runtime library linking issues  
- MuJoCo scene loading inconsistencies  
- Keyboard input event handling limitations in Linux terminal environment  

---

# Core Models

Stored in `g1_29dof/models/`:

- model_4999.pt →  early stable policy
- model_7999.pt →  intermediate policy
- model_9999.pt →  final trained policy
- policy.onnx →  deployment model

---

# Training Results Summary

- Final training iterations: ~10,000  
- Stable convergence observed after curriculum progression  
- Mean reward steadily improved across training phases  
- Policy demonstrates stable forward locomotion in simulation  

### Key snapshots:
- `results/screenshots/5000.jpg`
- `results/screenshots/8000.jpg`
- `results/screenshots/adjusted_10000.jpg`

---

# System Architecture

```
Isaac Sim / MuJoCo
        ↓
Isaac Lab (RL Environment)
        ↓
RSL-RL PPO Training
        ↓
Policy Export (ONNX / PT)
        ↓
FSM Controller (G1)
        ↓
Keyboard / Command Interface
        ↓
Simulation / Deployment
```

---

# Repository Structure

```
.
├── g1_29dof/
│   ├── commands/
│   │   ├── export_policy.md
│   │   ├── run_g1_ctrl.md
│   │   └── run_mujoco.md
│   │
│   ├── configs/
│   │   ├── config_keyboard.yaml
│   │   ├── deploy_keyboard.yaml
│   │   └── velocity_env_cfg_modified.py
│   │
│   ├── models/
│   │   ├── model_4999.pt
│   │   ├── model_7999.pt
│   │   ├── model_9999.pt
│   │   └── policy.onnx
│   │
│   ├── patches/
│   │   └── g1_keyboard_sim2sim.patch
│   │
│   ├── results/
│   │   ├── screenshots/
│   │   │   ├── 5000.jpg
│   │   │   ├── 8000.jpg
│   │   │   └── adjusted_10000.jpg
│   │   ├── sim2sim_summary.md
│   │   └── training_summary.md
│   │
│   ├── README.md
│
├── media/
│   └── g1_demo.mp4
│
├── .gitignore
└── README.md
```

---

# Key Technical Stack

- Isaac Sim 5.1  
- Isaac Lab  
- RSL-RL (PPO)  
- MuJoCo 3.3.6  
- Unitree G1 robot model  
- ONNX Runtime  
- Linux (Ubuntu 22.04)  
- Python 3.11  

---

# How to Run (Minimal Reproduction)

This project provides step-by-step execution instructions in the `commands/` directory.

Each file contains detailed setup and execution commands for a specific stage of the pipeline.

---

## MuJoCo Simulation

```bash
# See instructions in:
g1_29dof/commands/run_mujoco.md
```

---

## Controller Execution

```bash
# See instructions in:
g1_29dof/commands/run_g1_ctrl.md
```

---

## Policy Export

```bash
# See instructions in:
g1_29dof/commands/export_policy.md
```

---

# Network Interface Note

Network interface names vary across machines.

Common examples:
- wlp131s0
- wlan0
- wlo1
- enp3s0

Find your interface using:

```bash
ip link show
```

Then replace `<your_interface_name>` in all execution commands.

---

# Demo

Simulation demo:

```
media/g1_demo.mp4
```

---

# What This Project Demonstrates

This project demonstrates:
  
- Reinforcement learning for legged locomotion
- Reward function debugging and tuning
- Sim-to-sim transfer (Isaac →  MuJoCo)
- FSM-based robot control pipeline
- Real-world robotics system debugging
- Policy deployment with ONNX runtime

---

# Notes on Reproducibility

This project depends on:

- NVIDIA Isaac Sim (must be installed separately)  
- Isaac Lab environment  
- Unitree robot SDK / models  Reinforcement learning for legged locomotion
- GPU-enabled system  

Checkpoint and ONNX models are included for inference only.

---

# Known Issues

- Network interface differs across machines
- DDS communication requires correct interface binding
- Slight turning asymmetry in sim2sim deployment
- Minor lateral drift under velocity control

---

# Future Work

- Improve left/right turning symmetry  
- Reduce lateral drift under velocity commands  
- Extend policy to uneven terrain (rough terrain training)  
- Deploy to real Unitree hardware (if available)  
- Improve sim-to-real robustness  

---

# Base Framework

This work is based on the Unitree RL Lab framework:

https://github.com/unitreerobotics/unitree_rl_lab

which provides reinforcement learning environments for Unitree robots using Isaac Lab and RSL-RL.

We build upon this framework and introduce:
- Sim-to-sim deployment to MuJoCo
- FSM-based controller integration in C++
- Custom reward redesign for locomotion stability
- Keyboard-driven control interface for debugging and evaluation

---

# Author

Chunmiao Li  
University of California, Berkeley  

---

# Important

This repository focuses on research reproduction + engineering implementation, not a full standalone framework.# Unitree RL Lab Reproduction and Sim2Sim Deployment

This repository documents my reproduction and extension work on Unitree robot reinforcement learning using Isaac Lab, Unitree RL Lab, and MuJoCo sim2sim.


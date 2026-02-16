<div align="center">
  <h1>Unitree Simulation Environment</h1>
  <h3>High-Fidelity Robot Simulation with Isaac Sim & Isaac Lab</h3>
  <p>
    <a href="README.md">English</a> | <a href="README_zh-CN.md">中文</a>
  </p>
  <a href="https://discord.gg/ZwcVwxv5rq"><img src="https://img.shields.io/badge/-Discord-5865F2?style=flat&logo=Discord&logoColor=white" alt="Discord"></a>
  <img src="https://img.shields.io/badge/Isaac_Sim-5.0.0-green" alt="Isaac Sim">
  <img src="https://img.shields.io/badge/Isaac_Lab-2.2.0-blue" alt="Isaac Lab">
</div>

---

## 📖 Overview

High-fidelity simulation environment for **Unitree G1 and H1-2 humanoid robots**, built on **NVIDIA Isaac Sim 5.0** and **Isaac Lab**. Enables real robot deployment, RL training, teleoperation, and data collection.

**Supported Robots**: G1 (29-DOF), H1-2 (27-DOF)  
**Grippers**: Dex1 (2-finger), Dex3 (3-finger dexterous), Inspire hand

**Key Features**:
- 🤖 Real robot deployment (same DDS protocol as physical robots)
- 🧠 RL policy training (parallel simulation of 1000s of robots)
- 🎮 Teleoperation (keyboard and VR control)
- 📊 Data collection (integration with xr_teleoperate)
- 🔄 Sim-to-real transfer

---
## 📚 Resources

### External Links
- **Official Repo**: https://github.com/unitreerobotics/unitree_sim_isaaclab
- **Isaac Lab**: https://isaac-sim.github.io/IsaacLab/
- **Isaac Sim**: https://docs.omniverse.nvidia.com/isaacsim/
- **Unitree SDK**: https://github.com/unitreerobotics/unitree_sdk2_python
- **XR Teleoperate**: https://github.com/unitreerobotics/xr_teleoperate
- **Community Discord**: https://discord.gg/ZwcVwxv5rq

### Installation Guides
- [Isaac Sim 5.0 Installation](https://github.com/unitreerobotics/unitree_sim_isaaclab/blob/main/doc/isaacsim5.0_install.md)
- [Isaac Sim 4.5 Installation](https://github.com/unitreerobotics/unitree_sim_isaaclab/blob/main/doc/isaacsim4.5_install.md)
- [Isaac Lab Official Guide](https://github.com/unitreerobotics/unitree_sim_isaaclab?tab=readme-ov-file)

---

## ⚙️ Installation Guide

### Prerequisites

- **Operating System**: Ubuntu 20.04 or Ubuntu 22.04
- **GPU**: NVIDIA GPU with 8GB+ VRAM (tested on RTX 3080, 3090, 4090)
  - RTX 50 series: Use Isaac Sim 5.0.0
  - RTX 30/40 series: Use Isaac Sim 4.5.0 or 5.0.0
- **CUDA**: Version 12.2 or higher
- **RAM**: At least 16GB
- **Disk Space**: ~30GB for installation

---

### Step 1: Clone This Repository

```bash
git clone https://github.com/unitreerobotics/unitree_sim_isaaclab.git
cd unitree_sim_isaaclab
git submodule update --init --depth 1
```

**Configure Teleimager** (optional, for image streaming):
```bash
# Edit teleimager/cam_config_server.yaml
image_shape: [480, 640]
type: isaacsim
```

---

### Step 2: Install Isaac Sim + Isaac Lab

Choose based on your Ubuntu version:

#### **Option A: Ubuntu 22.04 (Recommended - Pip Installation)**

**1. Create Virtual Environment**
```bash
conda create -n unitree_sim_env python=3.11
conda activate unitree_sim_env
```

**2. Install PyTorch**  
Install according to your CUDA version (example for CUDA 12.6):
```bash
pip install torch==2.7.0 torchvision==0.22.0 torchaudio==2.7.0 \
  --index-url https://download.pytorch.org/whl/cu126
```

**3. Install Isaac Sim 5.0.0**
```bash
pip install --upgrade pip
pip install "isaacsim[all,extscache]==5.0.0" --extra-index-url https://pypi.nvidia.com
```

**Verify Installation**:
```bash
isaacsim
# First run will ask: Do you accept the EULA? (Yes/No): Yes
```

**4. Install Isaac Lab**
```bash
git clone https://github.com/isaac-sim/IsaacLab.git
cd IsaacLab
git checkout v2.2.0

sudo apt install cmake build-essential
./isaaclab.sh --install
```

**Verify Isaac Lab**:
```bash
python scripts/tutorials/00_sim/create_empty.py
# or
./isaaclab.sh -p scripts/tutorials/00_sim/create_empty.py
```

---

#### **Option B: Ubuntu 20.04 (Binary Installation)**

**1. Download Isaac Sim Binary**  
Download [Isaac Sim 5.0.0 binary](https://docs.isaacsim.omniverse.nvidia.com/latest/installation/download.html) and extract to a directory (e.g., `~/tools/isaac-sim`).

**2. Set Environment Variables**  
Replace with your actual path:
```bash
export ISAACSIM_PATH="${HOME}/tools/isaac-sim"
export ISAACSIM_PYTHON_EXE="${ISAACSIM_PATH}/python.sh"

# Add to ~/.bashrc for persistence
echo 'export ISAACSIM_PATH="${HOME}/tools/isaac-sim"' >> ~/.bashrc
echo 'export ISAACSIM_PYTHON_EXE="${ISAACSIM_PATH}/python.sh"' >> ~/.bashrc
source ~/.bashrc
```

**Verify Setup**:
```bash
${ISAACSIM_PATH}/isaac-sim.sh
# or
${ISAACSIM_PYTHON_EXE} -c "print('Isaac Sim configuration complete.')"

# Note: Deactivate all conda environments (including base) before running
```

**3. Install Isaac Lab**
```bash
git clone https://github.com/isaac-sim/IsaacLab.git
cd IsaacLab
git checkout v2.2.0

# Create symbolic link (replace with your path)
ln -s ${HOME}/tools/isaac-sim/ _isaac_sim

# Create conda environment and install
./isaaclab.sh --conda unitree_sim_env
conda activate unitree_sim_env
./isaaclab.sh --install
```

---

### Step 3: Install Unitree SDK

```bash
git clone https://github.com/unitreerobotics/unitree_sdk2_python.git
cd unitree_sdk2_python
pip3 install -e .
```

---

### Step 4: Install Project Dependencies

```bash
cd ~/unitree_sim_isaaclab  # Or your clone directory
pip install -r requirements.txt

# Install teleimager (optional, for image streaming)
cd teleimager
pip install -e .
```

---

### Step 5: Download Robot Assets

```bash
cd ~/unitree_sim_isaaclab
sudo apt update && sudo apt install git-lfs -y
bash fetch_assets.sh
```

---

### Step 6: Verify Installation

```bash
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab

python sim_main.py \
  --task Isaac-PickPlace-Cylinder-G129-Dex1-Joint \
  --robot_type g129 \
  --enable_cameras \
  --enable_dex1_dds
```

**Expected**: Isaac Sim window opens, G1 robot appears in warehouse scene.

**Note**: First run may take 3-5 minutes for shader compilation.

---

### Common Installation Issues

#### Issue 1: `libstdc++.so.6` version too low

**Error**:
```
OSError: version GLIBCXX_3.4.30' not found
```

**Solution**:
```bash
conda install -c conda-forge libstdcxx-ng
```

---

#### Issue 2: CycloneDDS not found during unitree_sdk2_python installation

**Error**:
```
Could not locate cyclonedds. Try to set CYCLONEDDS_HOME or CMAKE_PREFIX_PATH
```

**Solution**: Install CycloneDDS manually:
```bash
# Clone CycloneDDS
git clone https://github.com/eclipse-cyclonedds/cyclonedds -b releases/0.10.x ~/cyclonedds
cd ~/cyclonedds
mkdir build install && cd build

# Build and install
cmake .. -DCMAKE_INSTALL_PREFIX=../install
cmake --build . --target install

# Set environment variable
export CYCLONEDDS_HOME=~/cyclonedds/install
echo 'export CYCLONEDDS_HOME=~/cyclonedds/install' >> ~/.bashrc

# Now install unitree_sdk2_python
cd ~/unitree_sdk2_python
pip3 install -e .
```

See [unitree_sdk2_python FAQ](https://github.com/unitreerobotics/unitree_sdk2_python#faq) for more details.

---

#### Issue 3: GPU not detected

**Solution**:
```bash
# Check NVIDIA driver
nvidia-smi

# If not working, reinstall drivers
sudo apt install nvidia-driver-535  # Or latest version
sudo reboot
```

---

#### Issue 4: Import errors

**Solution**:
```bash
# Verify environment
conda activate unitree_sim_env
python -c "import isaaclab; print('IsaacLab OK')"
python -c "import torch; print(f'PyTorch {torch.__version__}, CUDA: {torch.cuda.is_available()}')"

# If fails, reinstall Isaac Lab
cd ~/IsaacLab
./isaaclab.sh --install
```

---

## 🚀 Quick Start

### Run Your First Simulation

```bash
conda activate unitree_sim_env
cd ~/unitree_sim_isaaclab

python sim_main.py \
  --task Isaac-Move-Cylinder-G129-Dex1-Wholebody \
  --robot_type g129 \
  --enable_cameras \
  --enable_wholebody_dds
```

---

### Control Robot with Keyboard

**Terminal 1** - Start simulation:
```bash
python sim_main.py \
  --task Isaac-Move-Cylinder-G129-Dex1-Wholebody \
  --robot_type g129 \
  --enable_cameras \
  --enable_wholebody_dds
```

**Terminal 2** - Run keyboard controller:
```bash
python send_commands_keyboard.py
```

**Controls**:
- `W` - Forward
- `S` - Backward
- `A` - Left
- `D` - Right
- `Z` - Rotate left
- `X` - Rotate right
- `C` - Stop
- `↑/↓` - Adjust height

---

## 🎮 Available Tasks

### Task Types

Two main categories of tasks:

```
┌──────────────────────────┬────────────────────────────┐
│ Non-Wholebody Tasks      │ Wholebody Tasks            │
│ (Upper body only)        │ (Full body with walking)   │
├──────────────────────────┼────────────────────────────┤
│ • Fixed base             │ • Mobile base              │
│ • Arms + hands control   │ • Legs + arms + hands      │
│ • Pick-and-place         │ • Navigation + manipulation│
│ • Stacking               │ • Mobile manipulation      │
└──────────────────────────┴────────────────────────────┘
```

**Important**: Only tasks with `Wholebody` in the name enable leg movement!

### Example Tasks

| Task Name | Robot | Gripper | Mobile | Description |
|-----------|-------|---------|--------|-------------|
| `Isaac-PickPlace-Cylinder-G129-Dex1-Joint` | G1 | 2-finger | ❌ | Pick cylinder with fixed base |
| `Isaac-Stack-RgyBlock-G129-Dex3-Joint` | G1 | 3-finger | ❌ | Stack colored blocks |
| `Isaac-Move-Cylinder-G129-Dex1-Wholebody` | G1 | 2-finger | ✅ | Mobile manipulation |
| `Isaac-Move-Cylinder-G129-Dex3-Wholebody` | G1 | 3-finger | ✅ | Dexterous mobile tasks |
| `Isaac-PickPlace-RedBlock-H12-Inspire-Joint` | H1-2 | Inspire | ❌ | H1-2 manipulation |

**Command Format**:
```bash
python sim_main.py --task <TASK_NAME> --robot_type <ROBOT> --enable_<GRIPPER>_dds
```

---

## 🔧 Development Guide

### Understanding the Architecture

```
┌─────────────────────────────────────────────────────────┐
│ What do you want to do?                                 │
└─────────────────────────────────────────────────────────┘
                          ↓
        ┌─────────────────┴─────────────────┐
        ↓                                   ↓
┌───────────────────┐             ┌──────────────────────┐
│ Deploy algorithm  │             │ Train RL policy      │
│ on REAL ROBOT     │             │ to learn behavior    │
│                   │             │                      │
│ (MPC, IK, etc.)   │             │ (Neural networks)    │
└───────────────────┘             └──────────────────────┘
        ↓                                   ↓
📘 Use DDS Control              🧠 Use RL Training
Same code in sim & real!        1000s of parallel robots!
```

---

### Approach 1: DDS Control (Real Robot Ready)

**Use when**: You want to deploy your algorithm on the real robot

**Key Concept**: Your control script runs as a separate process and communicates with the simulation (or real robot) via DDS topics.

#### How It Works

```
┌─────────────────────────────────────────────┐
│ Your Algorithm (Python/C++)                 │
│   ↓ publishes to DDS topics                 │
│ Simulation (testing)                        │
│   ↓ same DDS topics                         │
│ Real Robot (deployment)                     │
└─────────────────────────────────────────────┘
```

#### Advantages

- ✅ **Same code** works in simulation and real robot
- ✅ **Easy to debug** (separate process)
- ✅ **Standard protocol** (Unitree SDK)
- ✅ **Quick deployment** - Change one line: `ChannelFactoryInitialize(1)` → `(0)`

#### DDS Topics

**For Wholebody Tasks** (robot can walk):
- Subscribe: `rt/lowstate` - Robot state (joint positions, velocities, IMU)
- Publish: `rt/run_command/cmd` - Velocity commands `[x_vel, y_vel, yaw_vel, height]`

**For Non-Wholebody Tasks** (fixed base):
- Subscribe: `rt/lowstate` - Robot state
- Publish: `rt/lowcmd` - Direct joint commands

#### Example: Simple Walking Controller

**In simulation**:
```python
from unitree_sdk2py.core.channel import ChannelPublisher, ChannelFactoryInitialize
from unitree_sdk2py.idl.default import String_

# Initialize DDS (channel 1 for simulation)
ChannelFactoryInitialize(1)
pub = ChannelPublisher("rt/run_command/cmd", String_)
pub.Init()

# Send velocity command: [x_vel, y_vel, yaw_vel, height]
cmd = String_(data=str([0.3, 0.0, 0.0, 0.75]))  # Walk forward 0.3 m/s
pub.Write(cmd)
```

**On real robot** - Just change channel number:
```python
ChannelFactoryInitialize(0)  # 0 = real robot
# Rest of code stays the same!
```

#### Use Cases

- Model Predictive Control (MPC)
- Inverse Kinematics (IK)
- Custom walking controllers
- Teleoperation systems
- Sensor fusion algorithms

---

### Approach 2: RL Training (Parallel Learning)

**Use when**: You want the robot to learn behavior from scratch

**Key Concept**: Train neural network policies using thousands of parallel robots running on GPU.

#### How It Works

```
┌─────────────────────────────────────────────┐
│ Training: 4096 parallel robots (GPU)        │
│   ↓ 60x faster than single robot            │
│ Export: policy.onnx (portable model)        │
│   ↓ deploy via DDS                          │
│ Real Robot: Load policy → publish commands  │
└─────────────────────────────────────────────┘
```

#### Advantages

- ✅ **Learn complex behaviors** automatically
- ✅ **GPU-accelerated** - 1000s of robots at once
- ✅ **Fast training** - Hours instead of days
- ✅ **Data-driven** optimization

#### Training Workflow

**1. Define Task** (observations, rewards, actions)  
**2. Train Policy** with parallel simulations  
**3. Export to ONNX**  
**4. Deploy via DDS** script on real robot

#### Why Parallel Robots?

```
Single Robot Training:  ~100 hours  
Parallel (4096 robots): ~1.5 hours  (60x faster!)
```

Training requires GPU tensors for batch processing - that's why we use parallel simulation.

#### Use Cases

- Learning to walk
- Dexterous manipulation
- Navigation policies
- End-to-end vision-based control

---

### Decision Matrix

| Your Goal | Use This | Why |
|-----------|----------|-----|
| Deploy MPC controller on real robot | **DDS Control** | Same code in sim & real |
| Test IK algorithm before deploying | **DDS Control** | Easy debugging |
| Keyboard teleoperation | **DDS Control** | Simple setup |
| Train walking from scratch | **RL Training** | Automatic learning |
| Learn manipulation policy | **RL Training** | Data-driven |
| Optimize for task performance | **RL Training** | Exploration-based |

---

## 🔑 Key Concepts

### DDS vs Tensors

**DDS (Data Distribution Service)**:
- Network communication protocol
- Used by real Unitree robots
- Publish/subscribe pattern
- Works for **one robot** at a time
- **Use for**: Real robot deployment

**Tensors (PyTorch arrays)**:
- Multi-dimensional arrays on GPU
- Process **thousands of robots** in parallel
- Used during RL training only
- **Use for**: Learning policies

**Important**: When deploying to real robot, always use DDS!

---

### Wholebody vs Non-Wholebody Tasks

```
┌───────────────────────────────────────────────────┐
│ Task Name Contains "Wholebody"?                   │
└───────────────────────────────────────────────────┘
           ↓                    ↓
         YES                   NO
           ↓                    ↓
   Full body control    Upper body only
   ✅ Legs work         ❌ Legs fixed
   ✅ Can walk          ❌ Base stationary
   ✅ Mobile tasks      ❌ Manipulation only
```

**Examples**:
- ❌ `Isaac-PickPlace-Cylinder-G129-Dex1-Joint` → Upper body only
- ✅ `Isaac-Move-Cylinder-G129-Dex1-Wholebody` → Full body with walking

**To control legs**: Always use a task with `Wholebody` in the name!

---

## 🐛 Troubleshooting

### Robot Legs Not Moving

**Symptom**: Sending keyboard commands but robot doesn't walk

**Solutions**:
1. ✅ Verify task name contains `Wholebody`
2. ✅ Check `--enable_wholebody_dds` flag is set
3. ✅ Confirm DDS channel: `ChannelFactoryInitialize(1)` for simulation
4. ✅ Test keyboard script is publishing to `rt/run_command/cmd`

---

### GPU Out of Memory

**Symptom**: Simulation crashes with CUDA OOM error

**Solutions**:
```bash
# Optimized settings for 16GB VRAM (RTX 4080/4070 Ti)
python sim_main.py --task <TASK> \
  --render_interval 5 \      # Render every 5 physics steps
  --physics_dt 0.01 \         # 100 Hz physics (default 200 Hz)
  --num_envs 1                # Single environment
```

---

### DDS Communication Not Working

**Symptom**: Commands sent but robot doesn't respond

**Solutions**:
1. Check environment variable:
   ```bash
   echo $CYCLONEDDS_HOME  # Should output path to cyclonedds/install
   ```

2. Verify DDS initialization:
   ```bash
   python -c "from unitree_sdk2py.core.channel import ChannelFactoryInitialize; \
              ChannelFactoryInitialize(1); print('DDS OK')"
   ```

3. Check channel number:
   - Simulation: `ChannelFactoryInitialize(1)`
   - Real robot: `ChannelFactoryInitialize(0)`

4. Test topic publishing:
   ```bash
   # Run send_commands_keyboard.py and check for errors
   python send_commands_keyboard.py
   ```

---

### Simulation Window Not Responding

**Symptom**: First startup appears frozen for 3-5 minutes

**Expected Behavior**: Shader compilation on first run (this is normal!)

**Solutions**:
- ✅ Wait patiently (3-5 minutes first time only)
- ✅ Run headless first for faster compilation: `--headless` flag
- ✅ Check GPU: `nvidia-smi`
- ✅ Subsequent runs will be fast (<30 seconds)

---

### Import Errors

**Symptom**: `ModuleNotFoundError: No module named 'isaaclab'`

**Solutions**:
```bash
# 1. Verify environment is activated
conda activate unitree_sim_env

# 2. Test imports
python -c "import isaaclab; print('IsaacLab OK')"
python -c "import torch; print(f'PyTorch {torch.__version__}, CUDA: {torch.cuda.is_available()}')"

# 3. If fails, reinstall Isaac Lab
cd ~/IsaacLab
./isaaclab.sh --install
```

---

### Camera/Rendering Issues

**Symptom**: Black screen or rendering artifacts

**Solutions**:
1. Update Vulkan drivers:
   ```bash
   sudo apt install vulkan-tools
   vulkaninfo | grep deviceName  # Check GPU detected
   ```

2. Check Isaac Sim cache:
   ```bash
   rm -rf ~/.cache/ov
   rm -rf ~/.nvidia-omniverse/cache
   ```

3. Try different render mode:
   ```bash
   python sim_main.py --task <TASK> --headless  # Test without GUI
   ```

---

## 📊 Performance Optimization

### For 16GB VRAM GPUs

**Recommended Settings**:
```bash
python sim_main.py \
  --task <TASK> \
  --render_interval 5 \      # Reduces GPU load
  --physics_dt 0.01 \         # Coarser physics (faster)
  --num_envs 1 \              # Single environment
  --enable_cameras            # Enable if needed
```

---

### For RL Training (24GB+ VRAM)

**Maximum Parallelization**:
```bash
python train_policy.py \
  --num_envs 4096 \           # 4096 parallel robots
  --headless \                # No GUI (faster)
  --render_interval 10        # Minimal rendering
```

**Training Tips**:
- Use `--headless` to skip GUI rendering
- Increase `--num_envs` based on available VRAM
- Monitor GPU usage: `nvidia-smi -l 1`

---

### For Remote Development

**Headless Mode + Image Streaming**:
```bash
# On remote server
python sim_main.py --task <TASK> --headless --enable_cameras

# Built-in teleimager streams images via ZMQ (port 60000)
# View images from local machine using ZMQ client
```

**SSH with X11 Forwarding**:
```bash
ssh -X user@server
# Then run simulation normally
```




## 🤝 Contributing

We welcome contributions! Please:
- Follow Isaac Lab coding standards
- Test changes thoroughly
- Document new features
- Discuss major changes on Discord first

---

## 📝 License

This project follows the same license as Isaac Lab and Unitree SDK.

---

## 🙏 Acknowledgments

- **NVIDIA** - Isaac Sim and Isaac Lab frameworks
- **Unitree Robotics** - Robot platforms and SDK
- **Community Contributors** - Bug reports and improvements

---

<div align="center">
  <p>⭐ Star this repo if you find it useful!</p>
  <p>Made with ❤️ by the Unitree community</p>
</div>

<!--

**Here are some ideas to get you started:**

🙋‍♀️ A short introduction - what is your organization all about?
🌈 Contribution guidelines - how can the community get involved?
👩‍💻 Useful resources - where can the community find your docs? Is there anything else the community should know?
🍿 Fun facts - what does your team eat for breakfast?
🧙 Remember, you can do mighty things with the power of [Markdown](https://docs.github.com/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax)
-->

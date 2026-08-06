codingmaster24@codingmaster24-tech-24:~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 26.04 LTS
Release:	26.04
Codename:	resolute

wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2604/x86_64/cuda-ubuntu2604.pin

sudo mv cuda-ubuntu2604.pin /etc/apt/preferences.d/cuda-repository-pin-600

wget https://developer.download.nvidia.com/compute/cuda/13.3.1/local_installers/cuda-repo-ubuntu2604-13-3-local_13.3.1-610.43.02-1_amd64.deb

sudo dpkg -i cuda-repo-ubuntu2604-13-3-local_13.3.1-610.43.02-1_amd64.deb

sudo cp /var/cuda-repo-ubuntu2604-13-3-local/cuda-*-keyring.gpg /usr/share/keyrings/

sudo apt-get update

sudo apt-get -y install cuda-toolkit-13-3

# How to Install CUDA Toolkit 13.3 and Set Environment PATH Variables on Ubuntu 26.04 LTS

Setting up deep learning environment frameworks, GPU acceleration, or high-performance C++ applications on Linux often requires installing NVIDIA CUDA Toolkit. If you are running **Ubuntu 26.04 LTS (Noble Numbat / Future LTS)**, this step-by-step guide will walk you through downloading, installing, and permanently configuring the CUDA PATH environment variables.

---

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Step 1: System Preparation & Update](#step-1-system-preparation--update)
3. [Step 2: Add NVIDIA CUDA Repository & Pinning](#step-2-add-nvidia-cuda-repository--pinning)
4. [Step 3: Download and Install CUDA 13.3 Package](#step-3-download-and-install-cuda-133-package)
5. [Step 4: Update APT and Install CUDA Toolkit](#step-4-update-apt-and-install-cuda-toolkit)
6. [Step 5: Set CUDA PATH Environment Variables (Crucial Step)](#step-5-set-cuda-path-environment-variables-crucial-step)
7. [Step 6: Verify the Installation](#step-6-verify-the-installation)
8. [Troubleshooting & Common Issues](#troubleshooting--common-issues)
9. [Conclusion](#conclusion)

---

## Prerequisites

Before proceeding, ensure that:
* You have an **NVIDIA GPU** installed in your machine.
* You are running **Ubuntu 26.04 (x86_64 architecture)**.
* You have `sudo` administrative privileges.
* Proper NVIDIA display drivers are installed (`nvidia-smi` should run successfully).

To check your GPU and current driver status, run:
```bash
nvidia-smi
```

---

## Step 1: System Preparation & Update

Start by ensuring your system packages and repository headers are up to date.

```bash
sudo apt-get update
sudo apt-get upgrade -y
sudo apt-get install -y build-essential dkms wget software-properties-common
```

---

## Step 2: Add NVIDIA CUDA Repository & Pinning

APT repository pinning ensures that system package updates don't unexpectedly overwrite or conflict with official CUDA packages provided directly by NVIDIA.

Execute the following commands to download and configure the repository pin:

```bash
# Download the APT repository pin file
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2604/x86_64/cuda-ubuntu2604.pin

# Move the pin file to APT preferences
sudo mv cuda-ubuntu2604.pin /etc/apt/preferences.d/cuda-repository-pin-600
```

---

## Step 3: Download and Install CUDA 13.3 Package

Next, download the local installer Debian package (`.deb`) for CUDA 13.3 on Ubuntu 26.04.

```bash
# Download the CUDA local repository package
wget https://developer.download.nvidia.com/compute/cuda/13.3.1/local_installers/cuda-repo-ubuntu2604-13-3-local_13.3.1-610.43.02-1_amd64.deb

# Install the repository package using dpkg
sudo dpkg -i cuda-repo-ubuntu2604-13-3-local_13.3.1-610.43.02-1_amd64.deb

# Copy the GPG keyring to APT's trusted keyrings directory
sudo cp /var/cuda-repo-ubuntu2604-13-3-local/cuda-*-keyring.gpg /usr/share/keyrings/
```

---

## Step 4: Update APT and Install CUDA Toolkit

Now that the local repository and keyring are properly configured, update package lists and install `cuda-toolkit-13-3`:

```bash
# Update local package index
sudo apt-get update

# Install CUDA Toolkit 13.3
sudo apt-get -y install cuda-toolkit-13-3
```

> **Note:** If you also need the full display drivers included alongside the toolkit, you can install `sudo apt-get -y install cuda` instead of just `cuda-toolkit-13-3`.

---

## Step 5: Set CUDA PATH Environment Variables (Crucial Step)

By default, installing the CUDA Toolkit places binaries in `/usr/local/cuda-13.3/bin` and libraries in `/usr/local/cuda-13.3/lib64`. However, Linux will **not** automatically recognize commands like `nvcc` until you update your shell environment variables.

### Option A: Set PATH for Current Shell (Temporary)
To temporarily add CUDA to your current terminal session:

```bash
export PATH=/usr/local/cuda-13.3/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-13.3/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### Option B: Set PATH Permanently for User (Recommended)
To ensure CUDA paths are available every time you open a terminal or log in, append the variables to your `~/.bashrc` file (or `~/.zshrc` if using Zsh):

```bash
# Open bash profile
echo '' >> ~/.bashrc
echo '# NVIDIA CUDA 13.3 Environment Setup' >> ~/.bashrc
echo 'export PATH=/usr/local/cuda-13.3/bin${PATH:+:${PATH}}' >> ~/.bashrc
echo 'export LD_LIBRARY_PATH=/usr/local/cuda-13.3/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}' >> ~/.bashrc

# Reload bashrc configuration
source ~/.bashrc
```

*Alternatively, you can use symlinks pointing to `/usr/local/cuda` so that future version upgrades don't break your configuration:*

```bash
export PATH=/usr/local/cuda/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

### Option C: Set System-Wide Library Path
For system-wide CUDA dynamic library access across all users:

```bash
echo "/usr/local/cuda-13.3/lib64" | sudo tee /etc/ld.so.conf.d/cuda-13-3.conf
sudo ldconfig
```

---

## Step 6: Verify the Installation

After setting your environment variables, verify that CUDA compiler and driver communications work properly:

### 1. Check `nvcc` Compiler Version
```bash
nvcc --version
```
**Expected Output:**
```text
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2026 NVIDIA Corporation
Built on ...
Cuda compilation tools, release 13.3, V13.3.x
```

### 2. Verify CUDA Installation Path
```bash
which nvcc
```
**Expected Output:**
`/usr/local/cuda-13.3/bin/nvcc` (or `/usr/local/cuda/bin/nvcc`)

### 3. Test GPU Driver Communication
```bash
nvidia-smi
```

---

## Conclusion

You have successfully installed CUDA Toolkit 13.3 on Ubuntu 26.04 LTS and properly configured environment PATH variables! You are now ready to compile CUDA programs, run GPU acceleration frameworks like PyTorch and TensorFlow, or build custom C++/CUDA kernels.
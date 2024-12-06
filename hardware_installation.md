---
layout: default
title: Hardware and Installation Instructions
description: Details on system requirements and hardware/software install.
---

## Table of Contents
1. [Hardware Requirements](#hardware-requirements)
2. [Software Requirements](#software-requirements)
3. [OS Installation](#os-installation)
4. [CUDA Installation](#cuda-installation)
5. [openCV Installation](#opencv-installation)
6. [PIXCI Installation](#pixci-installation)
7. [Camera Setup](#camera-setup)
8. [Agent Tracker Installation](#agent-tracker-installation)
9. [TODO: Homography](#todo-homography)
10. [Testing Agent Tracker](#testing-agent-tracker)

## Hardware Requirements

### Computer
- CPU: Intel Core i9-10920X or better (NOTE: processor must support a minimum of PCI 48 lanes for the framegrabbers and GPU, eg. [Intel Xeon 6960p](https://www.intel.com/content/www/us/en/products/sku/240775/intel-xeon-6960p-processor-432m-cache-2-70-ghz/specifications.html))
- Motherboard: ASUS WS X299 SAGE or better (NOTE: motherboard must support the same number of PCI lanes as the CPU)
- GPU: NVIDIA GeForce RTX 3080 or better
- RAM: 64GB or more
- Storage: Samsung SSD 980 PRO 2TB or similar

### Camera System
* EPIX PIXCI E8 frame grabber cards (4x)
* CameraLink cables (4x, terminators may depend on camera model)
* Camera power supplies (4x, may depend on camera model)
* High speed cameras (4x, see tested camera models below)
    * Sentech STC-CMB401PCL-NIR
    * Basler acA2040-180km
    * Baumer HXC40
* Navitar NMV-8M1 lenses (4x, compatible with camera models above)


## Software Requirements
Ubuntu 22.04.1 (requires kernel < 6.8 -- we have used 5.15.0-43-generic successfully)

## OS Installation
1. Download [Ubuntu 22.04.1 image](https://old-releases.ubuntu.com/releases/22.04.1/ubuntu-22.04.1-desktop-amd64.iso). (**CRITICAL**: current software is not compatible with newer images)
2. Create installation media by writing the image to a USB drive (using Etcher or Win32 Disk Image).
3. Insert the boot disk into the configured computer and boot using the installation media.
4. If the computer is connected to the internet, disconnect it. (**CRITICAL**: Make sure there is no internet connection, otherwise it will automatically install kernel security updates!)
5. Select the "Install Ubuntu Option". Perform a normal installation, with no other options checked.
6. Under "Name", type research (**CRITICAL**: user must be research). Under "Computer Name", type habitat3 or something similar.
7. Run the install.
8. Before restoring internet connection, check that the kernel is less than 6.8.
        
       uname -a

9. Then manually block automatic kernel updates.

        sudo apt remove unattended-upgrades
        sudo apt remove update-notifier
        sudo apt remove update-manager

10. Restore the internet connection.
11. Add research user to sudo.

        sudo bash -c "echo 'research ALL=(ALL) NOPASSWD: ALL' >> /etc/sudoers"

12. Make a base research directory.

        sudo mkdir /research
        sudo chmod a+rwx /research -R
        cd /research

## CUDA Installation

1. Download CUDA 11.8.

        wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2204/x86_64/cuda-ubuntu2204.pin
        sudo mv cuda-ubuntu2204.pin /etc/apt/preferences.d/cuda-repository-pin-600
        wget https://developer.download.nvidia.com/compute/cuda/11.8.0/local_installers/cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb

2. Install CUDA.

        sudo dpkg -i cuda-repo-ubuntu2204-11-8-local_11.8.0-520.61.05-1_amd64.deb
        sudo cp /var/cuda-repo-ubuntu2204-11-8-local/cuda-*-keyring.gpg /usr/share/keyrings/
        sudo apt update
        sudo apt -y install cuda

3. Restart the computer

## openCV Installation

1. Install required libraries.

       sudo apt install cmake git gcc g++ python3 python3-dev python3-pip python3-numpy libgtk2.0-dev pkg-config libcurl4-openssl-dev libcanberra-gtk-module libavcodec-dev libavformat-dev libswscale-dev libgstreamer1.0-dev libgtk-3-dev libpng-dev libjpeg-dev libopenexr-dev libtiff-dev libwebp-dev -y

2.  Make an opencv directory.

        mkdir /research/opencv
        cd /research/opencv

3. Clone openCV.

        git clone --depth=1 https://github.com/opencv/opencv.git
        git clone --depth=1 https://github.com/opencv/opencv_contrib.git

4. Create an openCV build location.

        mkdir /research/opencv/opencv/build
        cd /research/opencv/opencv/build

5. Set cmake options.

        cmake \
        -D CMAKE_BUILD_TYPE=RELEASE \
        -D CMAKE_INSTALL_PREFIX=/usr/local \
        -D WITH_CUDA=ON \
        -D WITH_CUDNN=ON \
        -D WITH_CUBLAS=ON \
        -D WITH_TBB=ON \
        -D OPENCV_DNN_CUDA=OFF \
        -D OPENCV_ENABLE_NONFREE=ON \
        -D CUDA_ARCH_BIN=8.6 \
        -D OPENCV_EXTRA_MODULES_PATH=/research/opencv/opencv_contrib/modules \
        -D BUILD_EXAMPLES=OFF \
        -D HAVE_opencv_python3=ON \
        ..

6. Run cmake to build openCV.

        make -j 16
        sudo make install

## PIXCI Installation
1. Install correct libc packages.

        sudo dpkg --add-architecture i386
        sudo apt-get update
        sudo apt-get install libc6:i386 libncurses5:i386 libstdc++6:i386

2. Install xclib (requires product key, product keys and these installation instructions can be found [here](https://drive.google.com/file/d/1HsYmDMjm96eamof9v3L0xhfs6f8xE5rr/view?usp=drive_link)).

        wget https://www.epixinc.com/support/files/software/xclib_v38/xcliplnx_x86_64.bin
        chmod +777 xcliplnx_x86_64.bin
        sudo ./xcliplnx_x86_64.bin

3. Install XCAP for linux (requires product key).

        mkdir /research/pixci
        cd /research/pixci
        wget https://www.epixinc.com/support/files/downloads/xcaplnx_x86_64.bin
        chmod +777 xcaplnx_x86_64.bin
        sudo ./xcaplnx_x86_64.bin

4. **CRITICAL**: During install, make sure to select Shared Files when prompted.

5. Test installation by running XCAP. This will typically require PIXCI driver installation and authentication via USB key or other product keys.

        sudo xcap


## Camera Setup
1. Plug in the cameras and frame grabbers. If the case allows, install a fan above the frame grabbers, as they get quite hot during video capture.
2. Open XCAP. If not completed before, activate it using a product key and/or USB dongle supplied by EPIX.

        sudo xcap

3. Set up each camera in XCAP.
    
    a. Under PIXCI Tab>PIXCI Open/Close
    
    b. Click Close

    c. Click Camera & Format

    d. Under Configure Software for Camera & Format select the appropriate camera model (for Sentech cameras, select: Sentech STC-CMB401CL, or Baslers, select: Basler Ace acA2040-180km, for Baumers, select: Baumer HXC40).

    e. Press OK.

    f. Click Open.

4. This should open windows with camera feeds and capture settings for each camera.

5. In each Capture & Adjust window for each camera, go to the Data tab and configure settings until you get a good image (no lines or interruptions) and about 120FPS. This may take some fiddling, depends on the lighting in the environment. Note that the configurable settings are different for each camera model.
    
    a. For Sentech STC-CMB401CL
        
        Output & Taps: 8 Tap x 2048
        Pixel Clock: 85.0 MHz
        Video Out: 8 bits
        Exposure: ~6ms but will depend on lighting situation

    b. For Basler acA2040-180km

        CL Tap Geometry: 1X10-1Y
        CL Pixel clock: 65 Mhz
        Sensor Taps: 1 Tap
        Pixel Size: 8 bits
        Sensor Bit Depth: 10 Bits
        Exposure: ~8ms but will depend on lighting situation

    c. For Baumer HXC40
        
        Pixel format: Mono 8
        C.L. Tap Control: 8 Tap
        Sensor Taps: 16 Tap
        Sensor Clock: 48 Mhz
        Exposure: ~8ms but will depend on lighting situation

6. Adjust gain and exposure as necessary, but note that increasing exposure can affect frame rate, and increasing gain can increase image noise.

7. Repeat for all cameras until images are good and framerate is near 120FPS.

## Agent Tracker Installation

(can also be found [here](https://drive.google.com/drive/folders/14y1hyg6ethvXalvqckkChWj2zbwmM0BH))

1. Go to the research folder.

        cd /research

2. Clone the cellworld data cache and create an environment variable pointing to it.

        git clone https://github.com/germanespinosa/cellworld_data
        echo "export CELLWORLD_CACHE=\"/research/cellworld_data\"" >> ~/.profile

3. Clone the agent tracking library.

        git clone https://github.com/cellworld/cellworld_habitat_cv

4. Install CLion (optional, can be done from the web, but requires a JetBrains account, ask Alex for account details).

        cd ~
        wget https://download-cdn.jetbrains.com/cpp/CLion-2024.2.2.tar.gz
        sudo tar -xvzf CLion-2024.2.2.tar.gz -C /opt

5. To run CLion from the command line, execute the following.

        cd /opt/clion-2024.2.2/bin
        sudo ./clion.sh

6. To build habitat_cv, use CLion.

    a. Configure the CMake options under File > Settings > Build, Execution, Deployment > CMake > CMake Options.

    b. Here, name the build environment to your preference, and under CMake Options configure the build settings. The main settings are as follows:

       USE_CUDA: Use cuda accelleration for frame processing
       CONNECT_ALL_DEPENDENCIES: Allows update, commit, pull and push from the dependencies folder to the corresponding repository
       USE_FAKE_CAMERA: In case cameras are not yet connected to the system, 4 image files can be used to test. If ON, the system requires an image per camera called "camera_[CAMERA_NUMBER].png" (ie camera_0.png for the first camera) in the fake_camera folder.
       USE_MCP2221: Uses the MCP2221 usb-gpio device to syncronize with the neuro recordings

    c. For example, if you want to build a standard environment without monitoring a sync channel, use the following settings:

        -DUSE_CUDA=ON -DCONNECT_ALL_DEPENDENCIES=ON -DUSE_FAKE_CAMERA=OFF -DUSE_MCP2221=OFF

    d. Alternatively, if you want to enable external sync monitoring, use the following:

        -DUSE_CUDA=ON -DCONNECT_ALL_DEPENDENCIES=ON -DUSE_FAKE_CAMERA=OFF -DUSE_MCP2221=ON

    e. To build, first right click on the project folder and click "Reload CMake Project". Then select Build > Build Project.

7. Alternatively, the project can be built from the terminal using the following code:

        cd /research/cellworld_habitat_cv
        mkdir build
        cd build
        cmake .. -DUSE_CUDA=ON -DCONNECT_ALL_DEPENDENCIES=ON -DUSE_FAKE_CAMERA=OFF -DUSE_MCP2221=OFF
        make -j 8 

## TODO: Homography
To ensure that the camera view is calibrated to the arena coordinates, our system requires a homography procedure to later warp the image to a flat perspective during image processing. Procedure follows:



## Testing Agent Tracker
1. To test whether the build worked, it is first necessary to specify a camera configuration. To do so, make sure the settings in xcap are correct, then copy the xcap settings to the config directory of the project (replace "NEW" with desired name).

        cp /usr/local/xcap/settings/xcvidset.fmt EPIX_new.fmt

2. If homography has not been created, follow instructions to create a homography file that will be paired with the camera config file. **TODO**

3. Create a run configuration if using CLion.

    a. Click on the drop down corresponding to the desired build, click Edit Configurations.

    b. Under program arguments, minimally provide the habitat configuration parameter (if you named your config file "EPIX_new.fmt", provide "new"):

        -hc new

4. To run agent_tracker, press the run button. If everything functions correctly, a video window with the video stream will pop up.
![logo](images/Open-Zeka-logo_resized.png)
- [First Steps to Follow After Installing JetPack](#first_step)
  - [How to get IP address of Jetson and make connection via SSH](#first_step_ssh)
  - [How to change password](#password_change)
  - [How to install text editor](#editor_install)
  - [How to change hostname](#hostname_change)
  - [(Optional) How to install ZSH](#zsh_install)
  - [(Optional) How to install Screen](#screen_install)
- [Rebuilding the kernel](#kernel_build)
- [ROS Kinetic Installation](#ros_install)
- [VESC Driver Installation](#vesc_install)
- [Racecar Installation](#racecar_install)
- [RPLIDAR (/w ROS Environment) Installation](#rplidar_install)
- [IMU Installation](#imu_install)
- [ZED Stereo Camera Installation](#zed_install)
- [USB Port Protocol Configurations](#usb_port)
- [Move Project to Another Directory](#move_project)
- [Necessary Steps to Establish ROS Remote Connection as Default](#ros_remote_con)
- [**Necessary Framework Installations**](#additional_install)
  - [Caffe installation](#caffe) 
  - [Torch installation](#torch)
  - [Tensorflow installation](#tensorflow_1_6)
  - [Keras and other addons](#keras)
  - [Jupyter Notebook installation](#jupyter)
  - [Jetson TX2 high performance mode](#jetson_high_perf)
- [**Make Vehicle Move for the First Time**](#first_move)
- [**Driving Autonomously**](#autonomous_drive)
- [**Collecting Data**](#gather_data)
- [**Training the Neural Net with Collected Data**](#training)
- [**Using the Trained Model**](#using_model)

# <a name="first_step"></a> First Steps to Follow After Installing JetPack
### <a name="first_step_ssh"></a>How to get IP address of Jetson and make connection via SSH
You can use "lanscan" on MAC, "wnetwatcher" on Windows and "angry IP scanner" on Linux. However, you can still use terminal to scan IPs  with the commands below:
```bash
sudo apt-get update && sudo apt-get install arp-scan
sudo arp-scan --localnet ## You might need to look for the "--interface" parameter on terminal.
```
The output should look like this:
```bash
Interface: enp0s5, datalink type: EN10MB (Ethernet)
Starting arp-scan 1.8.1 with 256 hosts (http://www.nta-monitor.com/tools/arp-scan/)
192.168.1.110	XX:XX:XX:XX:XX:XX	NVIDIA

2 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.8.1: 256 hosts scanned in 1.546 seconds (165.59 hosts/sec). 2 responded
```
You can establish a connection after you retrieve the IP address:
```bash
ssh nvidia@<IP address here>
# example:
ssh nvidia@192.168.1.110 # Default password is nvidia
```

### <a name="password_change"></a>How to change password
It's not a good idea to leave the password as default.
```bash
passwd
```

### <a name="editor_install"></a>How to install text editor
It is recommended to use Nano if you are new to the Linux platform.
```bash
sudo apt-get install nano
```
---
### <a name="hostname_change"></a>How to change hostname
It is reasonable to use a local domain name, instead of looking for IP address every time.  
[Asciinema app (a video that you can copy its context)](https://asciinema.org/a/nytXz7ZUMGAXb6VpHY0fLHEJY)
```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install avahi-daemon
```
Go to `/etc/hosts`:
```bash
sudo nano /etc/hosts
```

Find the line below and change `tegra` for what you want. For example you can put your team's name there:
```bash
...

127.0.1.1       tegra
...
```

Write the same name to this file as well:
```bash
sudo nano /etc/hostname
```
Lastly, run the script named `hostname` and restart the device:
```bash
sudo /etc/init.d/hostname.sh

sudo reboot
```
---

Now you can connect using the hostname:
```bash
ssh nvidia@<HostNameHere>.local
# example:
ssh nvidia@teamName.local
```

### <a name="zsh_install"></a>(Optional) How to install ZSH
Ascii cinema: https://asciinema.org/a/x6QlETqxDvdK8t0lmBvOWVoKD

### <a name="screen_install"></a>(Optional) How to install Screen
Screen is an application that will help you to keep scripts running in the background.
```bash
sudo apt-get install screen
```
To learn how to use screen:  
https://www.gnu.org/software/screen/manual/screen.html

## <a name="kernel_build"></a>Rebuilding the kernel
Some applications needs to be installed before move on to rebuild. You can run the command below to install these apps:
```bash
sudo apt-get install cmake ca-certificates
```
First of all, you need to download necessary files like shown below to rebuild kernel:
```bash
cd ~/
git clone https://github.com/jetsonhacks/buildJetsonTX2Kernel.git
```

There are some scripts to run necessary commands to rebuild kernel in this repository. First, it will download the kernel files from NVIDIA website. Run the command shown below to get the kernel files:
```bash
cd ~/buildJetsonTX2Kernel
#Kernel kaynaklarının indirilmesi
./getKernelSources.sh
```

A GUI will come up 

## Install Scanse SDK
```bash
cd ~/
# clone the sweep-sdk repository
git clone https://github.com/scanse/sweep-sdk

# enter the libsweep directory
cd sweep-sdk/libsweep

# create and enter a build directory
mkdir -p build
cd build

# build and install the libsweep library
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
sudo cmake --build . --target install
sudo ldconfig
```

## Install ROS Kinetic
The racecar code runs using ROS (Robot Operating System) libraries, more on that later. Now let's install ROS:
(For more detailed instructions on how to install: http://wiki.ros.org/kinetic/Installation/Ubuntu). Or take the short path:

```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116

sudo apt-get update
sudo apt-get install ros-kinetic-ros-base
sudo rosdep init
rosdep update
```
If you're using bash:
```bash
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
If you're using Zsh:
```bash
echo "source /opt/ros/kinetic/setup.zsh" >> ~/.zshrc
source ~/.zshrc
```
Lastly:
```bash
sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool build-essential
```
## Install drivers for VESC
You need a montior or an OS which uses X Server for this part of the tutorial. X Server runs as an interface that connects apps and OS.

* You can proceed with one of the selections below:
  * Connect the Jetson to a monitor.
  Or
  * Login on an OS which has X Server.
    Connect to the device using a SSH session
    ```bash
    ssh user@host -X
    # Attention to the -X flag!
    ```
    
    Make sure that X Server is up and running:
    ```bash
    nautilus .
    ```
    If you see some windows shows up on your screen, you are ready to go!
 
First, we need to make sure that some dependincies is installed. For that, run the code below:

```bash
sudo apt-get install qtcreator qt-sdk libudev-dev libqt5serialport5-dev 
```

After the installation, we need to pull firmware for the VESC from github and compile it. For that, run the codes below sequentially:

```bash
cd ~
git clone https://github.com/vedderb/bldc-tool
cd bldc-tool
# Let's compile.
qmake -qt=qt5
make clean && make
```

After this, we need to pull the configuration files for VESC:

```bash
cd ~
git clone https://github.com/mit-racecar/hardware.git
```

After all these steps, VESC firmware files is at `~/bldc-tool/firmwares` and configuration files is at `~/hardware/vesc`.

Run these commands sequentially to install VESC Firmware.

```bash
cd ~/bldc-tool
./BLDC-Tool
```
When you run those commands, an interface will show up to install firmware.
First, make sure that battery connected to VESC and also VESC connected to the USB Hub. In addition check the VESC lights. If RED light blinks, your battery level is low. We highly recommend that continue this part of the tutorial after your battery is fulled. If BLUE light is solid than you are good to go!

Under **Serial Connection**, you will see the *VESC-ttyACM0*(this is your VESC). If you don't see this name, make sure that you followed the steps above when you compile kernel and VESC connected to USB Hub.
Whwn you press **Connect** button, you will be connected to the VESC.

Choose **Firmware** from the upper menu.

![bldc_firmware](images/bldc_firmware.png)

Press **Choose**. It will prompt an error as below. Just press **OK** and then you can continue.

<p align="center">
  <img src="images/warning.png" />
</p>

Since we use servo motor, choose the `VESC_servoout.bin` file from `~/bldc-tool/firmwares/hw_410_411_412` directory.

<p align="center">
  <img src="images/save_bldc.png" />
</p>

You can choose **Upload** to start the Firmware installation. Application will be closed once installation is completed. However, you can start it again by following the same steps. We completed VESC firmware installation with this step.

After you restart *bldc-tool*, DO NOT forget to press **connect** with the name of *VESC-ttyACM0*.

Click **Read Configuration**. Current configuration settings will be displayed on window.

Press the **Load XML** button. Choose the file which placed at `~/hardware/vesc/6.141_bldc_old_hw_30k_erpm.xml` directory.

Press **Write Configuration** button. Then choose **Reboot** and VESC will restart itself. Application may shutdown itself during this step. This doesn't harm the *Reboot* process. Once again, you can start the app using the earlier method.

After you restart the application, make sure that **Read Configuration** file is the same as below.

After you configured the VESC settings, you can proceed to *Racecar* installation.

## Install drivers for VESC
We will follow [this](https://www.youtube.com/watch?v=fiaiA-o83c4) tutorial on youtube to install VESC.  
You will need a display for this part, or a desktop environment with the X server. The X server is used as a middle layer between the programs and the OS. What's cool about it is that we can attach to an instance of X server that is running on another machine. It is possible that none of this made sense to you or that you have no idea what I'm talking about. In that case, don't worry, just follow the steps.

* Either:
  * Connect your device (jetson) to a display,  
  or
  * Boot an OS that has X server (linux always does, so does your VM. You can also install [xquartz](https://www.xquartz.org/) on Macos. No idea about Windows)  
  Attach with an ssh session
  ```bash
  ssh user@host -X
  # notice the -X option
  ```
  Verify that X server works:
  ```bash
  nautilus .
  ```
  If you see the window rendered on your screen, you're good to go!
  
  asd

If you have done either of the above, you can proceed with [the video tutorial by Jetsonhacks](https://www.xquartz.org/)  
After you're done with that, we can install the racecar code!

## <a name="installracecar"></a> Install Racecar

Download and build racecar code
```bash
cd ~/
git clone --recursive https://github.com/openzeka/racecar-workspace
```
Racecar's source code is under the `src/` folder. This `racecar-ws` folder is what's called a `catkin workspace`. To get more information on ROS and catkin workspaces:  
wiki.ros.org/ROS/Tutorials  
and to access sample code used in lectures:  
github.com/openzeka/racecar-controllers  
For now let's go ahead and compile the code:
```bash
cd ~/racecar-ws
rm -rf build devel
catkin_make
```

And test it:  

---
If you're using bash:
```bash
source devel/setup.bash
```
If you're using Zsh:
```bash
source devel/setup.zsh
```
```bash
roslaunch racecar teleop.launch
```
You will see a bunch of errors about not being able to find ports and stuff. We need to set up port rules to eliminate those errors. Continue for the port configurations:


## Configuring port rules
Usb sensors, motors etc. are given addresses such as ttyUSB0 by the Linux kernel. These addresses are not always the same for everyone, but we need them to be constant so that we don't have to check them every single time before launching the car.  

First find the usb devices:
```bash
lsusb
```
Output will be similar to:
```bash
Bus 002 Device 003: ID 0bda:0411 Realtek Semiconductor Corp.
Bus 002 Device 002: ID 0bda:0411 Realtek Semiconductor Corp.
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 010: ID 0403:6001 Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC
Bus 001 Device 004: ID 045e:0745 Microsoft Corp. Nano Transceiver v1.0 for Bluetooth
Bus 001 Device 009: ID 046d:c21f Logitech, Inc. F710 Wireless Gamepad [XInput Mode]
Bus 001 Device 007: ID 046d:082d Logitech, Inc. HD Pro Webcam C920
Bus 001 Device 006: ID 1b4f:9d0f
Bus 001 Device 005: ID 0483:5740 STMicroelectronics STM32F407
Bus 001 Device 003: ID 0bda:5411 Realtek Semiconductor Corp.
Bus 001 Device 002: ID 0bda:5411 Realtek Semiconductor Corp.
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
```
We are looking for scanse sweep lidar, vesc, and imu. Filter those devices:
```bash
lsusb | grep "9dof\|STMicro\|Future Technology"
```
Output:
```bash
Bus 001 Device 010: ID 0403:6001 Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC
Bus 001 Device 006: ID 1b4f:9d0f
Bus 001 Device 005: ID 0483:5740 STMicroelectronics STM32F407
```
We need the vendor id's and/or product id's of these devices. Fortunately lsusb lists them in the following format:
```html
Bus <bus number> Device <device number>: ID <vendor id>:<product id> <Device name>
```
Let's obtain them. The first device `(Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC)` is scanse sweep lidar. The second is the imu (see the 9dof reference in product id?). The last (`STMicroelectronics`) is vesc. Vesc's vendor id is `0483`, and product id is `5740` for example. These numbers may be different for your devices, so be sure to check them.

We will edit the usb rules file to configure the usb port rules
```bash
sudo nano /etc/udev/rules.d/99-usb-serial.rules
```
Make sure it looks like this, and write "vendor id"s against `idVendor`, and "product id"s against `idProduct`:
```bash
ATTRS{idVendor}=="0403", SYMLINK+="sweep"
ATTRS{idProduct}=="6015", SYMLINK+="sweep"

ATTRS{idVendor}=="1b4f", SYMLINK+="imu"
ATTRS{idProduct}=="9d0f", SYMLINK+="imu"

ATTRS{idVendor}=="0483", SYMLINK+="vesc"
ATTRS{idProduct}=="5740", SYMLINK+="vesc"
```
**Re-plug your devices**, and test the connection:
```bash
l /dev/vesc && l /dev/sweep && l /dev/imu
```
You should see these symlinks:
```bash
lrwxrwxrwx 1 root root 7 Nov  9 11:16 /dev/vesc -> ttyACM0
lrwxrwxrwx 1 root root 7 Nov  9 10:59 /dev/sweep -> ttyUSB0
lrwxrwxrwx 1 root root 7 Nov  8 21:29 /dev/imu -> ttyACM1
```
# The End :)  
If everything above works fine, then we can start coding! Check out our ROS documents and sample codes:  
[ROS fundamentals](lecture%20materials/ros%20fundamentals.md)  
[Racecar code examples](https://github.com/openzeka/racecar-controllers/tree/bwsi_2017/marc-examples)  

---
In addition, this may be useful, but we don't need it for this application:
```bash
# If you want the serial numbers of devices:
usb-devices | grep "Manufacturer\|Product\|SerialNumber\|^$"

```

# Move the project to your own workspace:
[After cloning the project](#installracecar), if you'd like to move the project to your own workspace and use Git with your teammates (cough, you should!):
go to `.gitmodules` file in the project's main directory. It should look like this:  
```bash
[submodule "src/racecar"]
	path = src/racecar
	url = https://github.com/openzeka/racecar.git
[submodule "src/racecar-controllers"]
	path = src/racecar-controllers
	url = https://github.com/openzeka/racecar-controllers.git
[submodule "src/racecar-simulator"]
	path = src/racecar-simulator
	url = https://github.com/openzeka/racecar-simulator.git
[submodule "src/vesc"]
	path = src/vesc
	url = https://github.com/mit-racecar/vesc
  ```
  What you see here are called submodules (For details: [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)). Thanks to submodules, we can bring together multiple projects under one project, and that's what we did here. However you will probably get a permission error when you commit and push changes to the code. You must use your own repository for this.
  * Create a repository for each each of the projetcs (racecar, racecar-controllers, racecar-simulation, vesc) (Forgot how to?:  [Github: Create A Repo](https://help.github.com/articles/create-a-repo/))
  Or if you think there is a repository which you will not make changes (e.g perhaps vesc), you don't have to do this for that repository.
  * Change the repo addresses in the `.gitmodules` file, write your repos' addresses. Like this:
```bash
[submodule "src/racecar"]
	path = src/racecar
	url = <your git repo address here>
```
* Now create a repository for racecar-ws. And run the following command in the project's main directory (where the `.gitmodules` file is):

```bash
git remote remove origin
git remote add origin <your racecar-ws repository link>
```

To verify:
```bash
git remote -v
```

The output should look like this:
```bash
origin	https://github.com/MuhsinFatih/racecar-workspace (fetch)
origin	https://github.com/MuhsinFatih/racecar-workspace (push)
```
Now do a push to your repository in your project's root folder:
```bash
git push origin master
```
Lastly repeat these for other repositories that you've just created:
```bash
git remote remove origin
git remote add origin <senin racecar/simulator/controller vb. repository nin adresi>
git push origin master
```
Go over Github and verify that the project is now in your repository. When you click on each submodule you should be sent to the repository which that submodule is linked to.

## Set up remote connection for ROS by default
(This step is optional. In fact, it might be a good idea to avoid until you really need this)  
Go to `nano ~/.profile`
add the following lines:

 ---
 On the remote machine (robot):
```bash
export ROS_MASTER_URI=http://$(echo -e $(hostname -I)):11311
export ROS_IP=$(hostname -I)
```
---
On the local machine (Your computer, or perhaps VM):
```bash
export ROS_MASTER_URI=http://$(sudo arp-scan --localnet | grep NVIDIA | awk '{print $1;}'):11311
export ROS_IP=$(hostname -I)
```
---
Go to `nano ~/.bashrc` and add this line to **the beginning of the file, not the end!**:
```bash
source ~/.profile
```
If you are using zsh, go to `nano ~/.zshrc` and add the following lines to **the beginning of the file, not the end!**:
```bash
emulate sh
. ~/.profile
emulate zsh
```

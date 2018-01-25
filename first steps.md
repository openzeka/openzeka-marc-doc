# First steps after installing ubuntu on jetson
### Find Jetson's ip address and connect via ssh
You can use lanscan on mac, wnetwatcher on windows, and "angry ip scanner" on linux. Or, you can scan using terminal:
```bash
sudo apt-get update && sudo apt-get install arp-scan
sudo arp-scan --localnet ## you might need to search for the "--interface" option on your computer
```
The output should look like this:
```bash
Interface: enp0s5, datalink type: EN10MB (Ethernet)
Starting arp-scan 1.8.1 with 256 hosts (http://www.nta-monitor.com/tools/arp-scan/)
192.168.1.110	XX:XX:XX:XX:XX:XX	NVIDIA

2 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.8.1: 256 hosts scanned in 1.546 seconds (165.59 hosts/sec). 2 responded
```
After retrieving the IP address:
```bash
ssh nvidia@<ip address here>
# example:
ssh nvidia@192.168.1.110 # default password is nvidia
```

### Change your password
It's not a good idea to leave the password to default
```bash
passwd
```

### Install a text editor (Use nano especially if you're a linux newbie)
```bash
sudo apt-get install nano
```
---
### Change your hostname (computer's name)
To use a local domain instead of looking for ip every time.  
[Asciinema version (kinda like video but you can copy text)](https://asciinema.org/a/nytXz7ZUMGAXb6VpHY0fLHEJY)
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

Write the same name on this file as well:
```bash
sudo nano /etc/hostname
```
Lastly, run the hostname script and restart the device:
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

### Install zsh (optional)
Ascii cinema: https://asciinema.org/a/x6QlETqxDvdK8t0lmBvOWVoKD

### Install Screen
Screen is a program that will help keep scripts running in the background.
```bash
sudo apt-get install screen
```
To learn how to use screen:  
https://www.gnu.org/software/screen/manual/screen.html


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

If you have done either of the above, you can proceed with [the video tutorial by Jetsonhacks](https://www.xquartz.org/)  
After you're done with that, we can install the racecar code!

## <a name="installracecar"></a> Install Racecar

Download and build racecar code
```bash
cd ~/
git clone --recursive https://github.com/MuhsinFatih/racecar-workspace [referans 0 (kaynak kod)]
```
Racecar's source code is under the `src/` folder. This `racecar-ws` folder is what's called a `catkin workspace`. To get more information on ROS and catkin workspaces:  
wiki.ros.org/ROS/Tutorials  
and to access sample code used in lectures:  
[referans 1 (racecar controllers, bang bang, opencv ve deepLearning)]  
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
[referans 2 (ROS dökümanı, örnek python ve c++)]  
[referans 1 örnek kodlar (bangbang felan)]  

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
	url = https://github.com/MuhsinFatih/racecar.git
[submodule "src/racecar-controllers"]
	path = src/racecar-controllers
	url = https://github.com/MuhsinFatih/racecar-controllers.git
[submodule "src/racecar-simulator"]
	path = src/racecar-simulator
	url = https://github.com/MuhsinFatih/racecar-simulator.git
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

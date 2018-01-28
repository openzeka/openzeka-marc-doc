# Install ros offline



### Downloading packages with internet:
```bash

sudo apt-get --print-uris --yes -d --reinstall install ros-kinetic-desktop python-rosinstall python-rosinstall-generator python-wstool build-essential $(apt-cache depends ros-kinetic-desktop python-rosinstall python-rosinstall-generator python-wstool build-essential | grep "Hängt ab von:" |  sed 's/  Hängt ab von://' | sed ':a;N;$!ba;s/\n//g') | grep ^\' | cut -d\' -f2 >downloads.list


sudo apt-get --print-uris --yes -d --reinstall install catkin_make $(apt-cache depends ros-kinetic-desktop python-rosinstall python-rosinstall-generator python-wstool build-essential | grep "Hängt ab von:" |  sed 's/  Hängt ab von://' | sed ':a;N;$!ba;s/\n//g') | grep ^\' | cut -d\' -f2 >downloads.list

wget --input-file downloads.list

```

### Installing downloaded packages from deb files:

```bash

# just in case, for dependencies
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116

sudo apt-get update

# go to the folder where deb files are copied
sudo dpkg -i *.deb   # this will say 'too many errors, halting'. Don't worry. 
sudo apt-get -f install


sudo rosdep init
rosdep update

echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc




```
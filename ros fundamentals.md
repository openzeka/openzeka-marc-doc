
### create a ROS workspace

```bash
mkdir -p ~/catkin_ws/src
cd ~/catkin_ws
catkin_make
```

<!-- ### Generate stuff that explains stuff about packages :P -->
<!-- ```bash
catkin_init_workspace       # setup a workspace
``` -->

Now there are 3 folders:

```bash
build       # object files etc. good old build folder.. :)
devel       # linked libraries etc. Junk that needs to be seperated from actual implementation
src         # source files
```
### Make
```
catkin_make                 # make (now basically empty project)
# catkin_make builds packages in the source space to the build space
```

### Source the environment
```bash
cd devel
. /setup.bash       # source the environment (Add this project to the environment. Might need to do this in every ssh session)
```

### create package
```bash
cd src/
catkin_create_pkg beginner_tutorials std_msgs rospy roscpp      # std_msgs, rospy and roscpp are dependencies for beginner_tutorials
```
### go into the created package and code what you want

```bash
roscd beginner_tutorials/src
```

### e.g: create 2 files

**listener.py**
```python
#!/usr/bin/env python
import rospy
from std_msgs.msg import String

def callback(data):
    rospy.loginfo("I heard %s", data.data)

def listener():
    rospy.init_node('listener',anonymous=True)
    rospy.Subscriber("chat", String, callback)

    rospy.spin() # keep alive

if __name__ == '__main__':
    listener()
```
**talker.py**
```python
#!/usr/bin/env python
import rospy
from std_msgs.msg import String

def talker():
    pub = rospy.Publisher('chat', String, queue_size=10) # create a new topic called 'chatter' of type string
    rospy.init_node('talker', anonymous=True)
    rate = rospy.Rate(10) # 10Hz
    while not rospy.is_shutdown():
        hello_str = "hello world %s" % rospy.get_time()
        pub.publish(hello_str)
        rate.sleep()

if __name__ == '__main__':
    try:
        talker()
    except rospy.ROSInterruptException:
        pass
```

### make them executable by current user
```bash
chmod +x talker.py listener.py
```

### now build the project
```bash
cd ~/catkin_ws  # cd <your project folder>

```

### run roscore, and the talker node
```bash
roscore

rosrun beginner_tutorials talker.py
```

### listen to the talker node
```bash
rosnode list
rosnode info /talker    # find out what topics it outputs

rostopic list   # list the topics. see the one before
rostopic info /chat
rostopic echo /chat
```

### now run the listener node
```bash
rosrun beginner_tutorial listener.py

```
### 


### roslaunch
```xml
<launch>
    <node name="talker" pkg="tutorial" type="talker.py" output="screen"/>
    <node name="listener" pkg="tutorial" type="listener.py" output="screen"/>
</launch>
```
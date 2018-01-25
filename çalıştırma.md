Kod a girince (`racecar-ws` klasöründe):
```bash
source devel/setup.bash
```
Racecar ndoe larını çalıştırmak için:
```bash
roslaunch racecar teleop.launch
```
Tek başına sweep i çalıştırmak için:
```bash
roslaunch sweep_node sweep_minified.launch
```
OpenCV node unu çalıştırmak için:
```bash
roslaunch openCV opencv.py
```
Kod u değiştirdikten sonra tekrar build etmek için:
```bash
catkin_make
# veya 
catkin_make --pkg <paket ismi>
```
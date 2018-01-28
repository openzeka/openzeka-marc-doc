Kod a girince (`racecar-ws` klasöründe):
(Bunu her terminalde çalıştırın)
```bash
source devel/setup.bash
```
Racecar node larını çalıştırmak için:
(Joystik kontrolü ile birlikte aracı çalıştırır)
```bash
roslaunch racecar teleop.launch
```
Tek başına sweep i çalıştırmak için:
```bash
roslaunch sweep_ros sweep_minified.launch
```
OpenCV, bangbang veya collect_data node unu çalıştırmak için:
(Farklı bir terminal aç ve çalıştır)
```bash
rosrun openCV opencv.py
rosrun bangbang bangbang
rosrun deep_learning collect_data.py
```
Kod u değiştirdikten sonra tekrar build etmek için:
(kodun ana dizininde olman gerek)
```bash
catkin_make
# veya 
catkin_make --pkg <paket ismi>
```


Simulasyon:
```bash
roslaunch racecar_gazebo racecar_BWSI_2017.launch
```

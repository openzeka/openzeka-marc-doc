![logo](images/Open-Zeka-logo_resized.png)

- [Jetson'a, JetPack kurduktan sonra izlenecek ilk adımlar](#jetsona-jetpack-kurduktan-sonra-izlenecek-ilk-adımlar)
  - [Jetson'ın IP adresinin alınması ve SSH ile bağlantı kurulması](#jetson-%C4%B1n-ip-adresini-al-ve-ssh-ile-ba%C4%9Flan)
  - [Şifrenin değiştirilmesi](#%C5%9Eifreyi-de%C4%9Fi%C5%9Ftir)
  - [Text editörün kurulması](#text-edit%C3%B6r-%C3%BC-kur-%C3%B6zellikle-yeni-linux-kullan%C4%B1c%C4%B1s%C4%B1-isen-nano-kullan)
  - [Hostname'in(bilgisayar adı) değiştirilmesi](#hostname-i-bilgisayar-ismini-de%C4%9Fi%C5%9Ftir)
  - [ZSH'ın kurulması (İSTEĞE BAĞLI)](#zshi-kur-iste%C4%9Fe-ba%C4%9Fl%C4%B1)
  - [Screen'in kurulması](#screeni-kur)
- [Kernelin yeniden derlenmesi](#kerneli-yeniden-derle)
- [Scanse SDK kurulumu](#scanse-sdk-s%C4%B1n%C4%B1-kur)
- [ROS Kinetic kurulumu](#ros-kinetic-i-kur)
- [VESC sürücülerinin yüklenmesi](#vesc-s%C3%BCr%C3%BCc%C3%BClerini-y%C3%BCkle)
- [Racecar'ın kurulması](#-racecar-%C4%B1-kur)
- [USB port kuralları konfigürasyonu](#usb-port-kurallar%C4%B1-konfig%C3%BCrasyonu)
- [Projenin belirlenen çalışma alanına taşınması](#projeyi-kendi-%C3%A7al%C4%B1%C5%9Fma-alan%C4%B1na-ta%C5%9F%C4%B1)
- [ROS uzaktan bağlantısının varsayılan olarak açılması için gerekli konfigürasyonlar](#ros-uzaktan-ba%C4%9Flant%C4%B1s%C4%B1n%C4%B1-varsay%C4%B1lan-olarak-a%C3%A7%C4%B1lmas%C4%B1-i%C3%A7in-konfig%C3%BCre-et)
- [**Gerekli eklentilerin kurulması**](#gerekli-eklentilerin-kurulmas%C4%B1)
  - [Caffe kurulumu](#caffe-kurulumu) 
  - [Torch kurulumu](#torch-kurulumu)
  - [Tensorflow kurulumu](#tensorflow)
  - [Keras ve diğer eklentilerin kurulumu](#keras-ve-di%C4%9Fer-eklentiler)
  - [Jupyter Notebook kurulumu](#jupyter-notebook)
  - [Jetson TX2 Yüksek Performans modu](#jetson-tx2-y%C3%BCksek-performans-modu)
- [**Verilerin Toplanması**](#verilerin-toplanmas%C4%B1)
- [**Toplanan verilerle ağın eğitilmesi**](#toplanan-verilerle-a%C4%9F%C4%B1n-e%C4%9Fitilmesi)
- [**Eğitilen ağın kullanılması**](#e%C4%9Fitilen-a%C4%9F%C4%B1n-kullan%C4%B1lmas%C4%B1)
  

# Jetson'a, JetPack kurduktan sonra izlenecek ilk adımlar
### Jetson'ın IP adresinin alınması ve SSH ile bağlantı kurulması
Bu bağlantının yapılabilmesi için MacOS'ta "lanscan", Windows'ta "wnetwatcher" ve Linux'ta "angry IP scanner" kullanılabilir.
Bu yöntemler yerine, terminal kullanılarak da aşağıdaki komutlarda gösterildiği gibi IP taraması yapılabilir:

```bash
sudo apt-get update && sudo apt-get install arp-scan
sudo arp-scan --localnet 	## Bu adımda "--interface" parametresinin kullanımı incelenmelidir.
```
Çıktı aşağıdaki gibi olmalıdır:
```bash
Interface: enp0s5, datalink type: EN10MB (Ethernet)
Starting arp-scan 1.8.1 with 256 hosts (http://www.nta-monitor.com/tools/arp-scan/)
192.168.1.110	XX:XX:XX:XX:XX:XX	NVIDIA

2 packets received by filter, 0 packets dropped by kernel
Ending arp-scan 1.8.1: 256 hosts scanned in 1.546 seconds (165.59 hosts/sec). 2 responded
```
IP öğrenildikten sonra aşağıda gösterildiği gibi bağlantı kurulabilir:
```bash
ssh nvidia@<IP adresi>
# Örneğin:
ssh nvidia@192.168.1.110 # Varsayılan şifre: nvidia
```

### <a name="change-pass"></a>Şifrenin değiştirilmesi
Şifreyi varsayılan olarak bırakmak, güvenlik açısından iyi değildir.
```bash
passwd
```

### Text editörün kurulması (Linux konusunda tecrübesi az olan/olmayan kullanıcıların NANO kullanması tavsiye edilir.)
```bash
sudo apt-get install nano
```

---
### Hostname'in(bilgisayar adı) değiştirilmesi
Her seferinde IP adresini aramak yerine yerel alan adı kullanmak için bu video takip edilebilir:
[Ascii Cinema sürümü (yazılar kopyalayanabilmektedir)](https://asciinema.org/a/nytXz7ZUMGAXb6VpHY0fLHEJY)
```bash
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install avahi-daemon
```
`/etc/hosts` dizinine girilir:
```bash
sudo nano /etc/hosts
```
Aşağıda gösterildiği şekilde benzer olan satır bulunur ve `tegra` istenilen isimle değiştirilebilir:
```bash
...

127.0.1.1       tegra
...
```
Değiştirilen isim, aşağıda belirtilen dosyaya da yazılmalıdır:
```bash
sudo nano /etc/hostname
```
Son olarak aşağıda gösterildiği gibi `hostname` isimli script çalıştırılır ve sistem yeniden başlatılır:
```bash
sudo /etc/init.d/hostname.sh

sudo reboot
```
---

Artık hostname kullanılarak giriş yapılabilir:
```bash
ssh nvidia@<HOSTNAME>.local
# örnek:
ssh nvidia@TAKIM_ADI.local
```

### ZSH'ın kurulması (İSTEĞE BAĞLI)
Bunun için aşağıdaki videoda anlatılan adımlar takip edilebilir:
Ascii cinema: https://asciinema.org/a/x6QlETqxDvdK8t0lmBvOWVoKD

### Screen'in kurulması
Screen, Linux terminalinde program çalıştırılırken bağlantının kopması durumunda, arka planda istediğimiz scriptin çalışmaya devam etmesini sağlayan bir programdır.
```bash
sudo apt-get install screen
```
Screen kullanımı hakkında daha detaylı bilgi için aşağıdaki kaynak takip edilebilir:  
https://www.gnu.org/software/screen/manual/screen.html


Bu aşamadan itibaren devam etmeden önce bazı gerekli uygulamaların kurulması gereklidir. Bunun için terminalde, aşağıdaki komut çalıştırılabilir:
```bash
sudo apt-get install cmake ca-certificates
```

## Kernelin yeniden derlenmesi
Kernelin yeniden derlenmesi için, öncelikle aşağıda gösterildiği gibi gerekli dosyalar indirilir:
```bash
cd ~/
git clone https://github.com/jetsonhacks/buildJetsonTX2Kernel.git
```
Dosyalar Jetson'a kaydedildikten sonra ilk olarak, kernel dosyaları NVIDIA'nın sitesinden indirilmelidir. Bunun için aşağıdaki komutu çalıştırılabilir:
```bash
cd ~/buildJetsonTX2Kernel
#Kernel kaynaklarının indirilmesi
./getKernelSources.sh
```
İşlem tamamlandıktan sonra .config dosyasındaki düzenlemeler için bir grafik arayüzü açılacaktır. 
Burada bazı değişikliklerin yapılması gerekmektedir. JetsonHacks'e ait olan [bu videodan](https://www.youtube.com/watch?v=fxWObd1nK4s)  gerekli adımlar takip edilebilir.

Öncelikle açılan ekrandan **General Setup**'a tıklanır, daha sonra ise sağ tarafta açılan menüden **Local version -append to kernel release** butonuna çift tıklanır. Bu kısma, - ile başlayan bir isim gireilebilir. Burada *-openzeka-v0.1* şeklinde isim verilecektir. 

Daha sonra **Edit > Find** menüsünden açılan pencereye **ACM** yazılarak **USB Modem (CDC ACM) support** aktif hale getirilir. DİKKAT: Nokta ile değil tik işareti ile işaretlediğinizden emin olunuz. 

Daha sonra aynı şekile arama yapılarak **CH341** portu da aktifleştirilir.

Değişiklikler kaydedilir ve uygulama kapatılır. Artık kernel derlenebilir. Aşağıdaki komut çalıştırılır:
```bash
cd ~/buildJetsonTX2Kernel
#Kernel kaynaklarının "build" edilmesi
./makeKernel.sh
```
Bu işlem 10 dakika civarında sürmektedir. Derleme tamamlandıktan sonra, derlenen dosyalar kopyalanabilir. Bunun için aşağıdaki komut çalıştırılabilir:
```bash
cd ~/buildJetsonTX2Kernel
#Kernel kaynaklarının kopyalanması
./copyImage.sh
```
Jetson, yeniden başlatılır.

Bu işlemler bittikten sonra indirilen dosyalar silinebilir. Dosyalar /usr/src/sources dizinine indirilmiştir. Silmek için aşağıdaki komut çalıştırılabilir:
```bash
cd /usr/src/sources
sudo rm -rf kernel_src-tx2.tbz2 
```

## Scanse SDK kurulumu
```bash
cd ~/
# sweep-sdk repository'si indirilir
git clone https://github.com/scanse/sweep-sdk

# libsweep dizinine girilir
cd sweep-sdk/libsweep

# build adında bir klasör oluşturulur ve içerisine girilir.
mkdir -p build
cd build

# libsweep kütüphanesi build edilir ve yüklenir.
cmake .. -DCMAKE_BUILD_TYPE=Release
cmake --build .
sudo cmake --build . --target install
sudo ldconfig
```


## ROS Kinetic Kurulumu
Racecar ROS (Robot Operating System) kütüphaneleri ile çalışıyor. Bundan daha sonra bahsedeceğiz, şimdi kuralım:  
(Kurulum ile ilgili ayrıntılı ve açıklamalı döküman için: http://wiki.ros.org/kinetic/Installation/Ubuntu . Veya kısaca aşağıdakileri de uygulayabilirsin:)
```bash
sudo sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list'

sudo apt-key adv --keyserver hkp://ha.pool.sks-keyservers.net:80 --recv-key 421C365BD9FF1F717815A3895523BAEEB01FA116

sudo apt-get update
sudo apt-get install ros-kinetic-desktop-full
# Bu kurulum biraz uzun sürmektedir.
sudo rosdep init
rosdep update
```
Bash kullanıyorsan:
```bash
echo "source /opt/ros/kinetic/setup.bash" >> ~/.bashrc
source ~/.bashrc
```
Zsh kullanıyorsan:
```bash
echo "source /opt/ros/kinetic/setup.zsh" >> ~/.zshrc
source ~/.zshrc
```
Son olarak:
```bash
sudo apt-get install python-rosinstall python-rosinstall-generator python-wstool build-essential
```

## RPLIDAR kurulumu ve Test Edilmesi (ROS kurulumundan sonra yapılmalıdır)
```bash
cd ~/
# rplidar_ros repository'si indirilir
git clone https://github.com/robopeak/rplidar_ros.git

# rplidar ros paketi ile yapılan taramanın grafiksel arayüzde görüntülenmesi
roslaunch rplidar_ros view_rplidar.launch
```

## VESC sürücülerinin yüklenmesi
Bu bölümün bir monitör varlığında veya X Server mevcut olan bir işletim sisteminde yapılması gerekmektedir. X Server, programlar ve işletim sistemi arasında bir ara birim olarak görev yapar. X Server sayesinde, bu uygulamanın çalıştığı başka bir cihaza da bağlantı yapmak mümkündür.

* Aşağıdaki işlemlerden birisi ile devam edin:
  * Jetson, HDMI ile monitöre bağlanır.
  veya
  * X Server mevcut olan bir işletim sisteminde oturum açıldıktan sonra (Bu özellik, Linux'ta gömülü olarak gelmektedir. Aynı zamanda MacOS'ta [xquartz](https://www.xquartz.org/) da kullanılabilir.)
  
  Bir SSH oturumu ile bağlantı yapılır:
  ```bash
  ssh user@host -X
  # -X parametresine dikkat edilmelidir.
  ```
  X Server'ın çalıştığından emin olunmalıdır:
  ```bash
  nautilus .
  ```
  Eğer bir pencere açılmışsa bir sonraki adıma geçliebilir.

Bu aşamadan itibaren devam edilebilmesi için öncelikle bazı gereksinimlerin yüklü olduğundan emin olunmalıdır. Bunun için aşağıdaki komut çalıştırılır:

```bash
sudo apt-get install qtcreator qt-sdk libudev-dev libqt5serialport5-dev 
```

Yükleme bittikten sonra VESC için firmware, GitHub'dan çekilir ve derlenir. Bunun için aşağıdaki komutlar sırasıyla çalıştırılır:

```bash
cd ~
git clone https://github.com/vedderb/bldc-tool
cd bldc-tool

# Derleme işlemi
qmake -qt=qt5
make clean && make
```

Daha sonra VESC için konfigürasyon dosyaları indirilir: 
```bash
cd ~
git clone https://github.com/mit-racecar/hardware.git
```

Bu işlemlerden sonra VESC Firmware'ı `~/bldc-tool/firmwares` dizininde; konfigürasyon dosyaları ise `~/hardware/vesc` dizininde bulunmaktadır. 

VESC Firmware'ı yüklemek için aşağıdaki komutları sırasıyla uygulayınız: 

```bash
cd ~/bldc-tool
./BLDC-Tool
```

Bu komut çalıştırıldığında, firmware'ın yüklenmesi için aşağıdaki arayüz ile karşılaşılacaktır. 
Öncelikle VESC'e bataryanın bağlı olduğundan ve USB kablosuyla Hub'a bağlı olduğundan emin olunuz. Ayrıca VESC ışıklarını da kontrol ediniz. Eğer kırmızı ışık yanıp sönüyorsa batarya(aracın alt kısmında bulunan) bitmek üzere demektir. Bataryanızı şarj ettikten sonra bu işleme devam etmeniz önerilmektedir. Sabit bir mavi ışığın yanması, bataryanızın dolu olduğu anlamına gelmektedir ve ek birşey yapmanıza gerek yoktur. 

**Serial Connection** altında *VESC-ttyACM0* adı ile VESC görülecektir. Eğer bu isim ile bir cihaz görünmüyor ise kernel derlenirken belirtilen ayarların yapıldığından ve VESC'in Hub'a bağlı olduğundan emin olunuz. **Connect** butonuna basıldığında, VESC ile bağlantınız kurulacaktır. 

Üst menüden **Firmware**'a tıklayınız. 

![bldc_firmware](images/bldc_firmware.png)

**Choose** butonuna tıklayınız. Aşağıdaki gibi bir uyarı alınacaktır. **OK** butonuna tıkladıktan sonra devam ediniz. 

<p align="center">
  <img src="images/warning.png" />
</p>

Araçta servo motor kullanıldı için `~/bldc-tool/firmwares/hw_410_411_412` dizininde bulunan `VESC_servoout.bin` dosyası seçilmelidir.

<p align="center">
  <img src="images/save_bldc.png" />
</p>

**Upload** butonuna basılarak VESC'e Firmware yükleme işlemi başlatılabilir. Yükleme bittikten sonra uygulama kapanacaktır. Uygulama, terminalden yukarıda anlatıldığı gibi tekrar başlatılabilir. Bu adım ile VESC'e firmware yüklenmiş olunur. 

bldc-tool'u yeniden başlattıktan sonra *VESC-ttyACM0* adı ile **Connect**'e tıklamayı unutmayınız. 

**Read Configuration**'a tıklayınız. Mevcut konfigürasyon ayarları mevcut ekranda görünecektir. 

**Load XML** butonuna tıklayın. `~/hardware/vesc/6.141_bldc_old_hw_30k_erpm.xml` dizinindeki dosyası seçiniz. 

**Write Configuration** butonuna tıklayınız. Daha sonra **Reboot**'a tıklayarak VESC'i yeniden başlatınız. Bu adımda uygulama yine kapanabilir. Yukarıda belirtilen yol ile tekrar uygulamayı başlatabilirsiniz. Uygulamanın kapanması, VESC'in yeniden başlatılmasını engellemez.

Uygulamayı yeniden başlattıktan sonra **Read Configuration**'a tıklayarak ayarların aşağıdaki gibi olduğundan emin olunuz:

VESC ayarlarını yaptıktan sonra *Racecar* kurulumuna geçebilirsiniz. 

## <a name="installracecar"></a> Racecar'ı kur

## <a name="installracecar"></a> Racecar'ı kur
Ana dizine racecar-workspace klasörünü indirmek için aşağıdaki komutu sırasıyla çalıştırınız:

```bash
cd ~/
git clone --recursive https://github.com/openzeka/racecar-workspace
```

Bu `racecar-workspace` klasörü `catkin workspace` adında bir çalışma ortamıdır. ROS kullanımı ve catkin workspace ile ilgili daha ayrıntılı bilgiye ulaşmak için:  
wiki.ros.org/ROS/Tutorials  
Eğitimde kullanılan örneklere ulaşmak için:  
github.com/openzeka/racecar-controllers  

Aşağıdaki komut ile indirdiğiniz `catkin workspace` alanını derleyiniz:
```bash
cd ~/racecar-workspace
rm -rf build devel
catkin_make
```
catkin_make sırasında *ackermann_msgs* hatası alırsanız, aşağıdaki eklentileri kurduktan sonra tekrarr **catkin_make** yapınız. 

```bash
sudo apt-get install ros-kinetic-ackermann-msgs 
sudo apt-get install ros-kinetic-serial
cd ~/racecar-workspace
rm -rf build devel
catkin_make
```

Aşağıdaki eklentilerin de kurulu olduğundan emin olunuz.

```bash
sudo apt-get install ros-kinetic-joy
sudo apt-get install ros-kinetic-joy-teleop 
```

Aşağıdaki komutlar ile test aşamasına geçebilirsiniz.

```bash
cd ~/racecar-workspace
source devel/setup.bash
```

```bash
roslaunch racecar teleop.launch
```

Bu aşamada "Portlar bulunamadı" hasatsı görmeniz muhtemel. Bu aşamada portların konfigürasyon ayarlarının yapılması gerekmektedir.

---

## Usb Port Kuralları Konfigürasyonu
Usb sensörleri, motoru ve diğer donanımları taktığımızda linux bunlara ttyUSB0 gibi adresler verecektir. Bu adresler herkesde aynı olmayabilir, fakat sabit olması bizi "daha sonra hangi porta hangi cihaz bağlı" uğraşından kurtarmaktadır. 

Öncelikle USB cihazları bulunuz:
```bash
lsusb
```
Çıktı farklı olabilir fakat büyük oranda şu şekilde olacaktır:
```bash
Bus 002 Device 004: ID 2b03:f580  
Bus 002 Device 003: ID 0bda:0411 Realtek Semiconductor Corp. 
Bus 002 Device 002: ID 0bda:0411 Realtek Semiconductor Corp. 
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 013: ID 046d:c219 Logitech, Inc. Cordless RumblePad 2
Bus 001 Device 007: ID 10c4:ea60 Cygnal Integrated Products, Inc. CP210x UART Bridge / myAVR mySmartUSB light
Bus 001 Device 012: ID 0483:5740 STMicroelectronics STM32F407
Bus 001 Device 010: ID 1b4f:9d0f  
Bus 001 Device 005: ID 045e:0745 Microsoft Corp. Nano Transceiver v1.0 for Bluetooth
Bus 001 Device 003: ID 0bda:5411 Realtek Semiconductor Corp. 
Bus 001 Device 002: ID 0bda:5411 Realtek Semiconductor Corp. 
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

```
VESC ve IMU cihazlarını bu listede arıyoruz. 
### Eğer sweep-lidar kullanıyorsanız aşağıdaki adımları takip ediniz:
***sweep-lidar* kullananlar için, bu cihazın da port ayarlarının yapılması gerektiğinden aşağıdaki adımları uygulaması gerekmektedir.**
Bu cihazları filtrelememiz çalışma açısından kolaylık sağlayacaktır:
```bash
lsusb | grep "9d0f\|STMicro\|Future Technology"
```
Çıktı:
```bash
Bus 001 Device 010: ID 0403:6001 Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC
Bus 001 Device 006: ID 1b4f:9d0f
Bus 001 Device 005: ID 0483:5740 STMicroelectronics STM32F407
```
Bu cihazlara ait Vendor ID ve/veya Product ID'ye ihtiyacımız vardır. `'lsusb` komutu aşağıdaki format ile bu iki bilgiyi de vermektedir:
```html
Bus <bus number> Device <device number>: ID <vendor id>:<product id> <Device name>
```

İlk cihaz `(Future Technology Devices International, Ltd FT232 USB-Serial (UART) IC)` scanse sweep-lidar, ikincisi IMU, son olarak(`STMicroelectronics`) de VESC cihazlarını göstermektedir. Örneğin VESC'e ait vendor ID `0483`, ve product ID `5740` olarak görünmektedir. Bu ID'ler sizin cihazınızda farklı olabilir. Lütfen kend komut satırı çıktısınızda gördüğünüz ID'leri kullanınız.

Usb port kurallarını ayarlamak için usb kural dosyasını düzenleyeceğiz:
```bash
sudo nano /etc/udev/rules.d/99-usb-serial.rules
```
Aşağıda gösterilen kodları usb kural dosyasına yapıştırınız, `idVendor` karşısına kendi cihazınızın "vendor id"lerini, `idProduct` karşısına kendi cihazınızın "product id"lerini yazınız.
```bash
ATTRS{idVendor}=="0403", SYMLINK+="sweep"
ATTRS{idProduct}=="6015", SYMLINK+="sweep"

ATTRS{idVendor}=="1b4f", SYMLINK+="imu"
ATTRS{idProduct}=="9d0f", SYMLINK+="imu"

ATTRS{idVendor}=="0483", SYMLINK+="vesc"
ATTRS{idProduct}=="5740", SYMLINK+="vesc"
```

### Eğer RPLIDAR kullanıyorsanız aşağıdaki adımları takip ediniz:

Bu cihazları filtrelememiz çalışma açısından kolaylık sağlayacaktır:
```bash
lsusb | grep "9d0f\|STMicro\"
```
Çıktı:
```bash
Bus 001 Device 006: ID 1b4f:9d0f
Bus 001 Device 005: ID 0483:5740 STMicroelectronics STM32F407
```
Bu cihazlara ait Vendor ID ve/veya Product ID'ye ihtiyacımız vardır. `'lsusb` komutu aşağıdaki format ile bu iki bilgiyi de vermektedir:
```html
Bus <bus number> Device <device number>: ID <vendor id>:<product id> <Device name>
```

İlk cihaz IMU, ikinci cihaz(`STMicroelectronics`) ise VESC cihazlarını göstermektedir. Örneğin VESC'e ait vendor ID `0483`, ve product ID `5740` olarak görünmektedir. Bu ID'ler sizin cihazınızda farklı olabilir. Lütfen kend komut satırı çıktısınızda gördüğünüz ID'leri kullanınız.

Usb port kurallarını ayarlamak için usb kural dosyasını düzenleyeceğiz:
```bash
sudo nano /etc/udev/rules.d/99-usb-serial.rules
```
Aşağıda gösterilen kodları usb kural dosyasına yapıştırınız, `idVendor` karşısına kendi cihazınızın "vendor id"lerini, `idProduct` karşısına kendi cihazınızın "product id"lerini yazınız.
```bash
ATTRS{idVendor}=="1b4f", SYMLINK+="imu"
ATTRS{idProduct}=="9d0f", SYMLINK+="imu"

ATTRS{idVendor}=="0483", SYMLINK+="vesc"
ATTRS{idProduct}=="5740", SYMLINK+="vesc"
```

Buraya kadar herşey çalışıyorsa programı incelemeye başlayabilirsiniz! Örnek ROS dökümanlarımıza ve örnek kodlara göz atabilirsiniz:  
[ROS temelleri](lecture%20materials/ros%20fundamentals.md)  
[Racecar örnek kodları](https://github.com/openzeka/racecar-controllers/tree/bwsi_2017/marc-examples)


# Projenin belirlenen çalışma alanına taşınması
[Proje klonlandıktan sonra](#installracecar) belirlenen çalışma alanına taşınıp takım arkadaşlarınızla Git kullanmak isterseniz:

ana dizindeki `.gitmodules` dosyasına gir. Buna benziyor olmalı:
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
Bu gördüklerine submodule deniyor (Ayrıntı için: [7.11 Git Tools - Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)). Submodule ler sayesinde birden fazla git projesini bir proje altında toplayabiliyoruz, burada da öyle yaptık. Fakat muhtemelen değişiklik yaptığında commit ve push dediğin zaman iznin olmadığını söyleyecek, bunun için kendi özel repository'ni oluşturmalısın.  
* Github da her proje için (racecar, racecar-controllers, racecar-simulator, vesc) bir repository oluşturun ([Github: Create A Repo](https://help.github.com/articles/create-a-repo/))  
Veya değişiklik yapmayacağını düşündüğün repo varsa (mesela muhtemelen VESC), o repository için bunu yapmana gerek yok. 
* Repoların adreslerini az önceki `.gitmodules` dosyasında değiştir, kendininkileri yaz. Mesela şunun gibi:
```bash
[submodule "src/racecar"]
	path = src/racecar
	url = <buraya senin git repository adresin>
```
* Şimdi de racecar-ws için bir repository oluştur. Ve proje ana dizininde (`.gitmodules` dosyasının olduğu yer) Şu komutu çalıştır:
```bash
git remote remove origin
git remote add origin <senin racecar-ws repository nin linki>
```
Doğrulamak için:
```bash
git remote -v
```
Çıktı buna benzemeli:
```bash
origin	https://github.com/MuhsinFatih/racecar-workspace (fetch)
origin	https://github.com/MuhsinFatih/racecar-workspace (push)
```

* Tekrar ana dizinde iken kendi repository'ne push yap:
```bash
git push origin master
```
* Son olarak az önce oluşturduğun repository ler için bunları tekrar et:
```bash
git remote remove origin
git remote add origin <senin racecar/simulator/controller vb. repository nin adresi>
git push origin master
```

Github a bak ve kontrol et, projen artık senin adresinde olmalı. Submodule'lere basınca da işaret ettikleri repository'e yönlendirmesi gerek. Bunları yapıyorsa herşey tamam demektir.

---

## ROS uzaktan bağlantısının varsayılan olarak açılması için gerekli konfigürasyonlar (Bu aşama opsiyoneldir)

`nano ~/.profile` dosyasını açtıktan sonra aşağıdaki satırları ekleyiniz:

 ---
 Uzak makinede (robot):
```bash
export ROS_MASTER_URI=http://$(echo -e $(hostname -I)):11311
export ROS_IP=$(hostname -I)
```
---
Yerel makinede (Bilgisayar veya sanal bilgisayar):
```bash
export ROS_MASTER_URI=http://$(sudo arp-scan --localnet | grep NVIDIA | awk '{print $1;}'):11311
export ROS_IP=$(hostname -I)
```
---
`nano ~/.bashrc` dosyasını açınız ve aşağıdaki satırı (**dosyanın sonuna değil, başına**) ekleyiniz:
```bash
source ~/.profile
```

## Gerekli eklentilerin kurulması

### Caffe Kurulumu

Caffe kurulumuna geçmeden önce caffe için bazı gereksinimlerin kurulu olması gerekmektedir. Aşağıdaki komutları sırasıyla çalıştırınız:

```bash
sudo add-apt-repository universe
sudo apt-get update -y
sudo apt-get install cmake -y
# Genel gereksinimler
sudo apt-get install libprotobuf-dev libleveldb-dev libsnappy-dev libhdf5-serial-dev protobuf-compiler -y
sudo apt-get install --no-install-recommends libboost-all-dev -y
# BLAS
sudo apt-get install libatlas-base-dev -y
# Diğer gereksinimler
sudo apt-get install libgflags-dev libgoogle-glog-dev liblmdb-dev -y
sudo apt-get install python-dev python-numpy -y
sudo usermod -a -G video $USER
```

Yukarıdaki kurulumlar tamamlandıktan sonra aşağıdaki adımları takip ediniz. Caffe, GitHub'dan indirildikten sonra derlenmelidir:

```bash
cd ~
git clone https://github.com/BVLC/caffe.git 
cd caffe
cp Makefile.config.example Makefile.config
# config dosyası içinde yapılması gereken bazı değişiklikler
sudo sed -i 's/# USE_CUDNN := 1/USE_CUDNN := 1/' Makefile.config
sudo sed -i 's/# WITH_PYTHON_LAYER := 1/WITH_PYTHON_LAYER := 1/' Makefile.config
```

Devam etmeden önce "config" dosyasına Jetson TX2'ye ait mimari kodlarının da eklenmesi gerekmektedir. `Makefile.config` dosyasını çift tıklayarak açın.
**CUDA_ARCH** değişkenini dosya içerisinde bulun. `compute_62` ve `sm_62` satırları (aşağıdaki son iki satır) varsayılan olarak bulunmuyor olabilir. Eğer yoksa bu dosyaya onları eklediğinizden emin olun. **Makefile.config** dosyası içerisinde bulunan **CUDA_ARCH** değişkeninin aşağıdaki ile aynı olduğundan emin olun ve dosyayı kaydederek kapatın. 

```bash
CUDA_ARCH := -gencode arch=compute_20,code=sm_20 \
		-gencode arch=compute_20,code=sm_21 \
		-gencode arch=compute_30,code=sm_30 \
		-gencode arch=compute_35,code=sm_35 \
		-gencode arch=compute_50,code=sm_50 \
		-gencode arch=compute_52,code=sm_52 \
		-gencode arch=compute_53,code=sm_53 \
		-gencode arch=compute_60,code=sm_60 \
		-gencode arch=compute_61,code=sm_61 \
		-gencode arch=compute_62,code=sm_62 \
		-gencode arch=compute_62,code=compute_62 
```

Bu değişiklilk yapıldıktan sonra devam edilebilir. 


```bash
# Ubuntu 16.04, static cuda ile ilgili bir hata verdiğinden, derleme işlemi yapılırken bu özelliğin kapatılması gerekmektedir.
cmake -DCUDA_USE_STATIC_CUDA_RUNTIME=OFF
# Derleme işlemi, Jetson'da bulunan 6 çekirdek de kullanılarak yapılacaktır. Bu işlem sırasında başka bir işlem yapmamanız önerilir.
# Ayrıca bu adımdan önce Jetsonu yüksek performans moduna almak işlemi hızlandıracaktır. 
sudo nvpmodel -m 0
sudo ~/jetson_clocks.sh
make -j6 all
# Derleme işlemi bittikten sonra testleri çalıştıralım
make -j6 runtest
# Hızlı bir test (opsiyonel)
cd ~/caffe
tools/caffe time --model=models/bvlc_alexnet/deploy.prototxt --gpu=0
```

Eğer işlemler sırasında hdf5 hatası alınırsa şu adımları uyguladıktan sonra tekrar derleyin. 

```bash
echo “INCLUDE_DIRS += /usr/include/hdf5/serial” >> Makefile.config
echo “LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/hdf5/serial” >> Makefile.config
```

Buraya kadar herhangi bir hata ile karşılaşmadıysanız caffe kurulumu başarılı bir şekilde tamamlanmıştır. 
Eğer bu adımları takip ederek caffe'yi derlediyseniz Caffe executable dosyasını `~/caffe/tool` dizini altında bulabilirsiniz. 

### Torch Kurulumu

Öncelikle Torch için bazı gereksinimlerin kurulumunu yapalım. Aşağıdaki kodları sırasıyla terminalde çalıştırın. 

```bash 
sudo apt-get install --no-install-recommends git software-properties-common -y
```

Genel gereksinimleri kurduktan sonra github'tan Torch'u çekelim ve kuruluma geçelim.

```bash
echo 'export TORCH_ROOT=~/torch' >> ~/.bashrc
git clone https://github.com/torch/distro.git ~/torch --recursive
cd ~/torch
./install-deps
./install.sh -b
source ~/.bashrc
```

Aşağıdaki kurulumları tamamlandıktan sonra **Torch** kurulumu tamamlanacaktır. 


```bash
sudo apt-get install --no-install-recommends libhdf5-serial-dev liblmdb-dev -y
luarocks install tds
luarocks install dpnn
luarocks install "https://raw.github.com/deepmind/torch-hdf5/master/hdf5-0-0.rockspec"
luarocks install "https://raw.github.com/Neopallium/lua-pb/master/lua-pb-scm-0.rockspec"
luarocks install lightningmdb 0.9.18.1-1 LMDB_INCDIR=/usr/include LMDB_LIBDIR=/usr/lib/x86_64-linux-gnu
```

### Tensorflow 
İlk verimizi toplamadan önce bazı eklentilerin kurulması gerekmektedir. Öncelikle Tensorflow'u kurun. Bunun için şu adımları takip edin.

```bash
git clone https://github.com/openzeka/Tensorflow-for-Jetson-TX2.git ~/tensorflow
cd ~/tensorflow/Tensorflow-for-Jetson-TX2/Jetpack-3.1/1.6
sudo pip install tensorflow-1.6.0rc1-cp27-cp27mu-linux_aarch64.whl
```
Tensorflow kurulumu tamamlandıktan sonra aşağıdaki gibi test edebilirsiniz. 

```python
import tensorflow as tf
hello = tf.constant('Hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

### Keras ve diğer eklentiler
Şu kodu çalıştırmanız,bu adım için yeterli olacaktır.
```bash
sudo pip install h5py keras matplotlib scipy pandas
```
Bu kurulum uzun sürecektir(~45 dakika).

### Jupyter Notebook

Jupyter Notebook kurulumunu aşağıdaki komut ile yapabilirsiniz.

```bash
sudo -H pip install jupyter
```

Bu işlem, jupyter'i sadece **python2** kerneli ile kuracaktır. Eğer **python3** kernelini de kurmak isterseniz aşağıdaki kodu kullanabilirsiniz. 
```bash
python3 -m pip install ipykernel
python3 -m ipykernel install --user
```
## Jetson TX2 Yüksek Performans modu

Jetson'u farklı senaryolarda yüksek performans yada enerji tasarrufu ayarı ile kullanabiliriz. NVIDIA, bunun için bize **NVPModel**'i sunmaktadır. 

Jetson, 4 adet ARM A57 ve 2 adet Denver 2 çekirdeği ile gelmektedir. Default olarak Denver 2 çekirdekleri _off_ konumunda gelmektedir. Bunu **tegrastat**'tan da görebiliriz. 

Tegrastats, sistem kaynaklarının ne kadarının kullanıldığını gösteren ve derin öğrenme tarafında uygulamalar geliştirirken takip etmemiz gereken bir uygulamadır. Çalıştırmak için aşağıdaki komutu çalıştırın. 

```bash
~/tegrastats
```
Çıktı şu şekilde olacaktır. 

<p align="center">
  <img src="images/tegrastats.png" />
</p>

Burada GPU kullanımı gösterilmemektedir. GPU kullanımı takip etmek için _super\_user_ modunda çalıştırmamız gerekiyor. Bunun için : 
```bash
sudo su
./tegrastats
```
En sağda gösterilen **GR3D** bizim GPU'muzu göstermektedir. 

<p align="center">
  <img src="images/tegrastats_gpu.png" />
</p>

NVPModel'in farklı modlarda nasıl çalıştığını aşağıdaki tabloda görebilirsiniz. 

Mode | Mode Name | Denver 2 | Frequency | ARM A57 | Frequency | GPU Frequency
---- | --------- | -------- | --------- | ------- | --------- | -------------
0 | Max-N | 2 | 2.0 GHz | 4 | 2.0 GHz | 1.30 Ghz
1 | Max-Q | 0 | | 4 | 1.2 Ghz | 0.85 Ghz
2 | Max-P Core-All | 2 | 1.4 GHz | 4 | 1.4 GHz | 1.12 Ghz
3 | Max-P ARM | 0 | | 4 | 2.0 GHz | 1.12 Ghz
4 | Max-P Denver | 2 | 2.0 GHz | 0 | | 1.12 Ghz

Çalıştırmak için aşağıdaki kodu uygulayabilirsiniz.

```bash
sudo nvpmodel -m 0
# Burada 0 yerine istediğiniz modu girebilirsiniz.
# 0 yüksek performans modudur ve tüm çekirdekleri aktif hale getirir. 
```
## Verilerin Toplanması

Buraya kadar başarıyla gelebildiyseniz artık veri toplayabiliriz. Veri toplama işlemine başlamadan önce aracı aracın bataryalarının dolu olduğundan ve tüm donanımın özellikle de joystick ve kameranın bağlı olduğundan emin olun. Teleop'u çalıştırmadan önce joytstick'in doğru ayarlandığından emin olalım. 
Öncelikle joystick'in açık olduğundan ve _mode_ ışığının **yanmadığından** emin olun. Vibration tuşu ile kontrol edin. **D** moduna alın. 

<p align="center">
  <img src="images/joystick_d_mode.png" />
</p>

Aşağıda gösterilen dizini kontrol edin

```bash
ls /dev/input
```

<p align="center">
  <img src="images/js0.png" />
</p>


Bu komutun çıktısında **js0** adında bir dosya görüyor olmanız lazım. Eğer **js1** gibi bir dosya var ise aşağıdaki komutu çalıştırın.

```bash
sudo mv /dev/input/js1 /dev/input/js0
```

Eğer hiç **js** ile başlayan bir dosya göremiyorsanız, joystickin araca bağlı olduğundan, mode ışığının yanmadığından ve **D** modunda olduğundan emin olun. 

Şimdi **teleop**'u çalıştırabiliriz.

```bash
cd ~/racecar-workspace
source devel/setup.bash
roslaunch racecar teleop.launch
```
Komut çalıştırıldıktan sonra joystick ile aracı kontrol edebilirsiniz. 
**Aracı kontrol etmek için LB tuşuna basılı tutmayı unutmayın.**
Teleopun çalıştığı terminali kapatmadan yeni bir terminal açın ve veri toplamak için aşağıdaki kodu çalıştırın. 

```bash
cd ~/racecar-workspace
source devel/setup.bash
rosrun deep_learning collect_data.py
```

Kodu çalıştırdıktan sonra aşağıdaki gibi araca ait hız ve açı değerleri akmaya başlayacaktır. 

<p align="center">
  <img src="images/data_flowing.png" />
</p>

Joystick ile kontrol etmeye başladığınızda hız ve açı değerlerinin resimdeki gibi değiştiğini göreceksiniz.

<p align="center">
  <img src="images/daha_flowing_change.png" />
</p>

Aracı sürmeye başlayarak veri toplayabilirsiniz. Kameradan alınan görüntü, hız ve açı değerleri **racecar-workspace/src/racecar-controllers/marc-examples/deep_learning/data/** klasörüne kaydedilecektir. Her collect_data.py dosyasını çalıştırdığınızda **001**'den başlayarak ve artarak yeni bir klasör oluşturulacak ve veriler o klasöre kaydedilecektir. 

## Toplanan verilerle ağın eğitilmesi

Verileri topladıktan sonra eğitim aşamasına geçebiliriz. **racecar-workspace/src/racecar-controllers/marc-examples/ktrain** klasörüne gidin. Klasörün içinde bulunan **model_trainer.ipynb** dosyasını jupyter notebook yardımıyla açın.

```bash
cd ~/racecar-workspace/src/racecar-controllers/marc-examples/ktrain
jupyter notebook model_trainer.ipynb
```

Dosyada belirtilen adımları takip ederek **train** işlemini tamamlayabilirsiniz. Bu işlemi Jetson TX2 üzerinde gerçekleştirebilir ya da daha güçlü bir GPU olan bilgisayarda yapabilirsiniz. 

**Eğer verilerin olduğu klasörün yerini değiştirirseniz aynı zamanda yine resimlerin olduğu klasörün içinde bulunan _seyir.csv_ dosyasındaki resimlerin yolunu da değiştirmeniz gerekmektedir. Aksi halde hata alacaksınız. Bu dosya resimlerin bilgisayardaki bulunduğu yeri ve her resime ait açı ve hız değerlerini bulundurmaktadır.**

Eğitim bittikten sonra **ktrain** dosyasında oluşturulmuş olan **model_new.h5** ve **model_new_json** dosyalarını **racecar-workspace/src/racecar-controllers/marc-examples/deep_learning** klasörüne kopyalayın. 

## Eğitilen ağın kullanılması

 **model_new.h5** ve **model_new_json** dosyalarını _predict.py_ ile aynı klasöre kopyaladıktan sonra **teleop**'u çalıştırın. (Eğer çalışıyorsa tekrar çalıştırmanıza gerek yok)
 
 ```bash
cd ~/racecar-workspace
source devel/setup.bash
roslaunch racecar teleop.launch
```

Yeni bir terminal açın ve eğittiğiniz ağı kullanmak için aşağıdaki kodu kullanın

 ```bash
cd ~/racecar-workspace
source devel/setup.bash
rosrun deep_learning predict.py
```

Eğer bu komutu çalıştırdıktan sonra _No such file or directory_ hatası alırsanız, **predict.py** içindeki 28.satırda bulunan **model_name** değişkeninin doğru yolu gösterdiğinden emin olun. 

![predict.py](images/predict.png)


# Bitti :metal:

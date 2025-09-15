# FashionStar总线伺服舵机 C++ SDK

SDK:

https://fashionrobo.com/wp-content/uploads/download/fashionstar-uart-servo-cpp-sdk.zip

https://fashionrobo.com/downloadcenter/

通信协议：

https://wiki.fashionrobo.com/uartbasic/uart-protocol/

支持操作系统：Windows，Linux

使用文档

* [UART总线伺服舵机库-使用说明(Windows)](doc/FashionStar总线伺服舵机库-使用说明(Windows).pdf)
* [UART总线伺服舵机库-使用说明(Ubuntu)](doc/FashionStar总线伺服舵机库-使用说明(Ubuntu).pdf)
* [总线伺服舵机SDK使用手册](doc/总线伺服舵机SDK使用手册.pdf)

## Windows

Open "Developer Command Prompt for VS 2022"

编译CSerialPort lib

```
cd C:\ws\code\gimbal\fashionstar-uart-servo-cpp-sdk\dependency\CSerialPort
rmdir /s /q build
mkdir build
cd build
cmake .. -D CMAKE_INSTALL_PREFIX="c:/ws/tmp/CPP_LIBRARIES/"
msbuild CSerialPort.sln /p:Configuration=Debug /p:Platform="x64"
cmake --build . --target INSTALL --config Debug
```

编译fashionstar sdk

```
cd C:\ws\code\gimbal\fashionstar-uart-servo-cpp-sdk\
# modify CMakeLists.txt: SET(CMAKE_INSTALL_PREFIX "c:/ws/tmp/CPP_LIBRARIES/")
rmdir /s /q build
mkdir build
cd build
cmake ..
msbuild UART_DEMO.sln /p:Configuration=Debug /p:Platform="x64"
cmake --build . --target INSTALL --config Debug
```

编译sample app

```
cd C:\ws\code\gimbal\fashionstar-uart-servo-cpp-sdk\example\servo_set_angle\
# modify CMakeLists.txt: SET(CMAKE_INSTALL_PREFIX "c:/ws/tmp/CPP_LIBRARIES/")
# modify servo_set_angle.cpp: #define SERVO_PORT_NAME "COM5" 
rmdir /s /q build
mkdir build
cd build
cmake ..
msbuild SERVO_QUERY_ANGLE.sln /p:Configuration=Debug /p:Platform="x64"
cmake --build . --target INSTALL --config Debug
```

运行sample app

```
cd C:\ws\tmp\CPP_LIBRARIES\bin\
servo_set_angle.exe
```

## Ubuntu

找不到ttyUSB0设备的解决方法

```
$ lsusb
Bus 001 Device 010: ID 1a86:7523 QinHeng Electronics CH340 serial converter

$ lsmod | grep ch34
ch341                  24576  0
usbserial              69632  1 ch341

$ sudo dmesg | tail
[860994.429850] usb 1-6: new full-speed USB device number 11 using xhci_hcd
[860994.556915] usb 1-6: New USB device found, idVendor=1a86, idProduct=7523, bcdDevice= 2.64
[860994.556920] usb 1-6: New USB device strings: Mfr=0, Product=2, SerialNumber=0
[860994.556922] usb 1-6: Product: USB Serial
[860994.558955] ch341 1-6:1.0: ch341-uart converter detected
[860994.559356] usb 1-6: ch341-uart converter now attached to ttyUSB0
[860995.096895] input: BRLTTY 6.4 Linux Screen Driver Keyboard as /devices/virtual/input/input21
[860995.194563] usb 1-6: usbfs: interface 0 claimed by ch341 while 'brltty' sets config #1
[860995.195019] ch341-uart ttyUSB0: ch341-uart converter now disconnected from ttyUSB0
[860995.195031] ch341 1-6:1.0: device disconnected

问题原因
idVendor=1a86, idProduct=7523 是 CH340/CH341 芯片。系统插入时先识别为串口设备（ch341 模块）产生 ttyUSB0。
接着 brltty 的规则（udev 规则）系统认为这个设备是盲文设备（Braille），占用了接口，于是断开 ttyUSB0。

解决方法：停用 brltty 服务和规则
如果你想保留 brltty 但让它不干扰 CH340/CH341，可用下面方式：
# 停止服务
sudo systemctl stop brltty.service
sudo systemctl stop brltty-udev.service
# 禁用自动启动
sudo systemctl disable brltty.service
sudo systemctl mask brltty-udev.service
sudo systemctl stop brltty-udev.service
这会阻止 brltty 在启动时或插入设备时抢占接口。

用以下命令恢复 brltty 服务
# 取消 mask
sudo systemctl unmask brltty-udev.service
# 使能服务（enable）
sudo systemctl enable brltty-udev.service
sudo systemctl enable brltty.service
# 启动服务（start）
sudo systemctl start brltty-udev.service
sudo systemctl start brltty.service
# 检查状态，确认服务正在运行
systemctl status brltty.service
systemctl status brltty-udev.service
如果状态显示为 active（running）或至少 loaded + enable + active，就说明恢复成功。


$ sudo dmesg | tail
[861427.497362] usb 1-7.3: usbfs: interface 0 claimed by ch341 while 'brltty' sets config #1
[861427.498015] ch341-uart ttyUSB0: ch341-uart converter now disconnected from ttyUSB0
[861427.498026] ch341 1-7.3:1.0: device disconnected
[861459.067892] usb 1-7.3: USB disconnect, device number 14
[861462.847849] usb 1-7.3: new full-speed USB device number 15 using xhci_hcd
[861462.949483] usb 1-7.3: New USB device found, idVendor=1a86, idProduct=7523, bcdDevice= 2.64
[861462.949489] usb 1-7.3: New USB device strings: Mfr=0, Product=2, SerialNumber=0
[861462.949502] usb 1-7.3: Product: USB Serial
[861462.957317] ch341 1-7.3:1.0: ch341-uart converter detected
[861462.957915] usb 1-7.3: ch341-uart converter now attached to ttyUSB0

$ ls /dev/ttyUSB0
/dev/ttyUSB0
```

编译CSerialPort lib

```
cd ~/share/code/gimbal/fashionstar-uart-servo-cpp-sdk/dependency/CSerialPort
rm -rf build
mkdir build
cd build
cmake ..
make
sudo make install
```

编译fashionstar sdk

```
cd ~/share/code/gimbal/fashionstar-uart-servo-cpp-sdk
rm -rf build
mkdir build
cd build
cmake ..
make
sudo make install
```

编译sample app

```
cd ~/share/code/gimbal/fashionstar-uart-servo-cpp-sdk/example/servo_set_angle
# modify servo_set_angle.cpp: #define SERVO_PORT_NAME "/dev/ttyUSB0"
rm -rf build
mkdir build
cd build
cmake ..
make
sudo ./servo_set_angle
# 如果以前ttyUSB0设备没有找到，修复之后，最好先重启一下小板子，再执行上面的命令。
```


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

```
cd C:\ws\code\gimbal\fashionstar-uart-servo-cpp-sdk\dependency\CSerialPort
rmdir /s /q build
mkdir build
cd build
cmake .. -D CMAKE_INSTALL_PREFIX="c:/ws/tmp/CPP_LIBRARIES/"
msbuild CSerialPort.sln /p:Configuration=Debug /p:Platform="x64"
cmake --build . --target INSTALL --config Debug
```

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

```
cd C:\ws\tmp\CPP_LIBRARIES\bin\
servo_set_angle.exe
```


